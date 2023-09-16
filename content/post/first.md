+++
author = "Miu"
title = "このブログについて"
date = "2023-09-17"
description = "このブログに関する説明"
tags = [
    
]
+++

# 目的

このブログは自分用の技術メモとして使います．

## 記事を書く際のTips

[MarkDownチートシート](https://qiita.com/Qiita/items/c686397e4a0f4f11683d)

## Deploy手順

```bash
git add ファイル名
git commit -m '変更内容'
git push
```

```bash
git commit 
#変更内容 
#i: インサートモード
#esc: コマンドモード(インサートモードを抜ける)
#コマンド例
#[:wq]: 保存して終了
#[dd]: 1行消去
#[/文字列]: 検索，検索状態でnを押すと次の候補
#[G]: ファイルの末尾にジャンプ
```

# hugoのブログ構築手順

1. hugoプロジェクトを作成する
1. gitリポジトリを作成(git init)
1. githubでリポジトリの作成
1. gitリポジトリにリモートリポジトリを設定(下記に詳細)
1. hugoのテーマをサブモジュールとして追加(git submodule add https://github.com/J-Siu/hugo-theme-sk3 themes/sk3)
1. hugo.toml, netlify.tomlを設定
1. ポストの中にMarkDownを追加
1. netlifyのプロジェクトやアカウント設定
1. git add, commit, push

## gitリポジトリにリモートリポジトリを設定する方法

以下のコマンドでホスト名を確認する

```bash
cat ~/.ssh/config
```

出力される内容

```config
Host [ここを知りたい]
    HostName github.com
    IdentityFile ~/.ssh/id_rsa
    TCPKeepAlive yes
    IdentitiesOnly yes
    User git
    Port 22
```

[ここを知りたい]の箇所をコピーして
GitHubからコピーしてきたURLのgithub.comの部分を以下のように差し替える
差し替え前: `git remote add origin git@github.com:MiuKanematsu/blog.git`
差し替え後: `git remote add origin git@[ここを知りたい]:MiuKanematsu/blog.git`

ssh接続時に非公開鍵を使用するため，ホスト名を置き換える必要がある．
これをしないとpushやpullが機能しない場合がある．

## サーバーの起動コマンド

```bash
hugo server --ignoreCache -w -D
```

## git push -u origin main調べた

[git pushのオプション-uとは](https://qiita.com/shumpeism/items/1b8027c8905ca826416d)