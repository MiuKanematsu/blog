+++
author = "Miu"
title = "距離でサーボを回転させる"
date = "2023-09-26"
description = ""
tags = [
]
+++

<!-- 目的 -->

超音波距離センサでサーボの回転を制御する。実装メモの練習。

### サーボモータ

サーボモータを使用するためには、PWS(Pulse Width Modulation)というパルス幅変調方式が使われる。
パルス1周期の中にあるHIGHの時間の割合（パルス幅やデューティー比）を変えることにより、回転角度を指定している。

Arduinoには**Servoライブラリ**というライブラリが標準で用意されているので、比較的簡単にサーボモータを使用することができる。
サーボの制御においては、角度を指定するよりパルス幅を指定して動かした方が滑らかに動かせるらしい。

#### PWM制御

サーボモータには3つの端子があり、Vcc(赤)・GND（黒）・信号線（橙）となっている。
サーボモータの信号線には、パルス変調方式というPWM信号が入力される。
PWMとは、パルス（HIGH、LOWの信号）で、そのパルスの周期の中にあるHIGHの時間を変化させて制御する方式である。

この1周期の中にあるHIGHの時間**デューティー比**や、パルス幅を指定することによって、モーターを特定の角度回転させる。

[SG90](https://akizukidenshi.com/download/ds/towerpro/SG90_a.pdf)のデータシートを見てみると、PWM信号の周期が20msになっている。（周波数に直すと1/0.02=50Hz）
1秒間に50回の信号を送っていることになるが、この間にどれだけの時間HIGHだったかによってモーターの角度を指定することができる。

データシートでデューティーサイクルを見てみると、0.5~2.4msと記載されている。
つまり、周期20Hzのパルス幅の中に、 HIGHの時間を0.5msにしてあげると、サーボモータは最低角度の0度に動く。
また、2.4ms HIGHにすると、最大角度の180度に指定することができる。

![PWM制御説明画像](https://cdn-ak.f.st-hatena.com/images/fotolife/y/y-oka/20220813/20220813171203.jpg)

### 超音波距離センサー

音波センサHC-SR04を使用する。

![超音波距離センサ](https://akizukidenshi.com/img/goods/C/M-11009.jpg)

左側のTがついている方がトランスミッターで、超音波を発している。右側のRがついている方が、レシーバーで、超音波を受信している。
送信して受信するまでの時間で、距離を測定している。

#### 注意点

- 超音波が物体に反射して戻ってくる時間を測っているので、**反射面が音を拡散しやすい材質のものや、逆に吸収しやすいものだとうまく測定できなくなってしまう**

- 超音波は音なので、空気中を伝わる速度は一般的な音速の式に従う。

**音速[m/s] = 331.5 + 0.6T (℃)**

音速が変化するということは、周りの気温によって距離の値に誤差が生じるということになる。

### 距離からサーボを制御する

**距離センサ**

- Vcc: 5V
- Gnd: GND
- Trig: 3番ピン
- Trig: 2番ピン

**サーボ**

- Vcc(赤)
- GND（黒）
- 信号線（橙）

```ino:servo_dist.ino
#include <Servo.h>
Servo servo;

int TRIG = 3;
int ECHO = 2;
double duration = 0;
double distance = 0;
double speed_of_sound = 331.5 + 0.6 * 25;  // 25℃の気温の想定

void setup() {
  servo.attach(9);
  Serial.begin(9600);

  pinMode(ECHO, INPUT);
  pinMode(TRIG, OUTPUT);
}


void loop() {
  digitalWrite(TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG, LOW);
  duration = pulseIn(ECHO, HIGH);  
  if (duration > 0) {
    duration = duration / 2;  
    distance = duration * speed_of_sound * 100 / 1000000;
    Serial.print("Distance:");
    Serial.print(distance);
    Serial.print(" cm ");
  }

  int dist = constrain(distance, 0, 81);
  int servoAngle = map(distance, 0, 81, 0, 180);

  Serial.println(servoAngle);
  servo.write(servoAngle);
  delay(10);
}

```