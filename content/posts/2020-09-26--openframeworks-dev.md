---
title: "openFrameworks入門　開発環境構築（MacOS）"
date: "2020-09-26T22:40:32.169Z"
template: "post"
draft: false
slug: "openframeworks-dev"
category: "openFrameworks"
tags:
  - "メディアアート"
  - "開発環境構築"
  - "クリエイティブ・コーディング"
description: "簡単なコードのみで様々なグラフィックスやインタラクションをデザインすることができるツールです。ProcessingはJavaがベースになっていますがopenFrameworksではC++がベースになっています。（C++を知らなくてもプログラミングが可能になっています。）
クリエイティブの範囲も、グラフィック、インタラクティブ表現、ネットワーク、サウンド、画像処理、フィジカルコンピューティングなど多岐にわたっています。"
Image: "/media/advanced3dExample.png"
Image2: "/media/openFrameworks_setup.png"
---

## openFrameworksとは
簡単なコードのみで様々なグラフィックスやインタラクションをデザインすることができるツールです。ProcessingはJavaがベースになっていますがopenFrameworksではC++がベースになっています。（C++を知らなくてもプログラミングが可能になっています。）<br>
クリエイティブの範囲も、グラフィック、インタラクティブ表現、ネットワーク、サウンド、画像処理、フィジカルコンピューティングなど多岐にわたっています。

公式サイト https://openframeworks.cc/ja/

## 前準備
1. Xcodeが入っている必要があります。入っていない場合はインストールしましょう
2. Xcodeのコマンドラインツール（command line tools）をインストールします
```
xcode-select --install
```
上記のコマンドを打つと、コマンドラインツールのインストーラーが起動します。「Install」ボタンでインストールを開始します。

## openFrameworksのダウンロード
以下URLからOS Xのパッケージファイルをダウンロードします。
ダウンロードしたzipファイルで解凍します。
そうするとof_v0.10.1_osx_releaseみたいな名前のディレクトリが生成されるので任意の場所に移動します。
ここにプログラムファイルや設定ファイルなどが諸々入っています。
https://openframeworks.cc/ja/download/

※ openFrameworks環境構築はこれで以上です

## フォルダ構成
- apps - 作成するアプリケーションを配置することになります。
- examples - ビデオや3Dグラフィクス、サウンド、フォントなど様々な機能をデモするアプリケーションが含まれています。
- addons - openFrameworksのアドオン（addons）が含まれます (openFrameworksのコア機能を拡張する機能です)
- projectGenerator - 新規にopenFramworksのプロジェクトを生成することができます。

![advanced3dExample.xcodeproj](/media/advanced3dExample.png)

どんな感じかイメージをつかみたい人はexampleフォルダを開いて.xcodeproj拡張子がついているファイルを開いてみるとXcodeが起動します。
Xcodeの左上にある実行ボタンを押すと実際にプログラムが実行されます。
advanced3dExampleファルダ内のadvanced3dExample.xcodeprojを実行すると上記のようなアウトプットが出ます。

### サンプルの内容
3d - 3次元表現いろいろ
addons - 拡張機能のサンプル
communication - 外部デバイスとの通信(シリアル通信) 
empty - 制作のテンプレートとなる「空」サンプル 
events - イベント(プログラムへの外部からのアクション)処理 
gl - OpenGLの活用(Shader、VBO、カメラなど)
graphics - グラフィクスプログラミング
math - 数式による表現、ノイズ、周期など
sound - 音響生成、サウンドファイルの再生
utils - 補助的な機能の例
video - 動画の再生、カメラからの入力

openFrameworksのアプリケーションの基本的な構造は以下のようになっています。
- src - アプリケーションのソースファイルが入っています。
- bin - 最終的に実行するアプリケーションが配置されます。さらにその中のdataフォルダには、画像、サウンド、フォントといったアプリケーションのアセット（素材）が入っています。

## 新規プロジェクトの作成
projectGeneratorファルダ内にあるprojectGeneratorを起動します。
projectGeneratorの初回起動時に聞かれるpathは、ダウンロードしたopenFrameworksのフォルダそのもののpathです。<br>
createすると以下のようなセットアップする画面が表示されるので自分が作りたいものに必要な情報を記入してGenerateボタンを押すとXcodeが起動します。

![openFrameworks_setup](/media/openFrameworks_setup.png)

起動したらsrcフォルダを展開すると以下の3ファイルがあると思います。

- main.cpp<br>
アプリケーションを実行するためのファイル。このファイル内でウィンドウのサイズを設定する。基本的にはほとんど使わない。

- ofApp.cpp<br>
実際の描画やインタラクションなどの実行ファイル。

- ofApp.h<br>
ofApp.cppのヘッダファイル。このファイルにプログラム全体で使用する変数・メソッドを定義する。
ofApp.cppとofApp.hのように、.cppファイルと.hファイルの名前をそろえること。メンバ変数（インスタンス変数）はここに記述する

実際に開発する際のメインになるofApp.cppファイルを選択します。そうすると空のsetup()、update()、draw() という関数があると思います。この辺りはProcessingに似ていますね。

- setup( )<br>
このメソッドは、初期化のためのメソッドです。アプリケーションが開始した際に一度だけ実行されます、ofApp.hで宣言されたオブジェクトや変数を初期化といった用途に用いられます。

- update( )<br>
このメソッドは、アニメーションなどアプリケーションの状態を変更する際に用いられます。何らかの演算をする際には他のオブジェクトに対してupdateする必要があります、例えば動画の再生や、コンピュータビジョンや解析などあらゆる操作をするような際に当てはまります。

- draw( )<br>
このメソッドは描画用のメソッドです。

※update（更新）とdraw（描画）は無限ループと呼ばれます。アプリケーションが終了するまでは、繰り返し順番に実行されます。<br>

## 実際に動かしてみる
では実際にdraw()関数の中に、下記のコードを記入して、command+r（もしくはrunボタンをクリック）でアプリケーションをビルドして起動しましょう。

```
ofDrawBitmapStringHighlight("hellow world!", 20, 20);
```

hellow world!と表示されると思います。
