---
title: "Haskell入門 開発環境構築 (MacOS)"
date: "2020-10-01T22:40:32.169Z"
template: "post"
draft: false
slug: "haskell-dev"
category: "haskell"
tags:
  - "開発環境構築"
  - "関数型プログラミング"
description: "haskell-ide-engineやghcideはhaskell-language-serverに統合されたのでVisualStudioCodeの拡張（haskell-language-server）を使って環境構築をしました。haskell-ide-engineに比べてhaskell-language-serverの方が安定していて使い勝手が良いです。"
---

haskell-ide-engineやghcideはhaskell-language-serverに統合されたのでVisualStudioCodeの拡張（haskell-language-server）を使って環境構築をしました。<br>
haskell-ide-engineに比べてhaskell-language-serverの方が安定していて使い勝手が良いです。

## 1. [公式](https://docs.haskellstack.org/en/stable/README/#how-to-install)を参考にstackをインストール
brewが入っている場合はbrewで
```bash
$ brew install haskell-stack

# バージョン確認
$ stack --version 

# 更新
$ stack update
```

## 2. パスを通す
Stack経由でインストールしたライブラリなどは、~/.local/bin/以下に保存されるのでパスを通しておく必要があります。
```bash
$ export PATH=~/.local/bin:$PATH

# 必要に応じて .bashrc や .bash_profile などに追記してください
$ echo 'export PATH=~/.local/bin:$PATH' >> ~/.bashrc
```
## 3. [公式](https://docs.haskellstack.org/en/stable/README/#start-your-new-project)を参考に新しいプロジェクトを作り，コンパイラをインストール
```bash
$ stack new my-project
$ cd my-project

# Haskell コンパイラとghci（Haskellの対話形式の REPL）をPCにインストール
$ stack setup

# ビルドに必要なツールやライブラリをインストール
$ stack build

# stack経由でghcにオプションを渡す場合は -- を挟む
$ stack ghc -- --version
The Glorious Glasgow Haskell Compilation System, version 8.4.4

# 実行コマンド
$ stack exec my-project-exe
```

## 4. Visual Studio Codeで拡張を入れる
https://marketplace.visualstudio.com/items?itemName=haskell.haskell
<br>
先程作ったプロジェクトをVisual Studio Codeで開くと対応するhaskell language serverのバイナリを自動でダウンロードしてIDE周りを設定してくれる。<br>
マルチプロジェクトは正しく動かない場合があるので，そういう場合は`hie.yaml`を参考にして書いてプロジェクトルートに追加する
https://github.com/haskell/haskell-language-server#project-configuration

以上でHaskellの環境ができます。

## HaskellでHello World!
環境構築ができたので、お決まりのHello World!を出力するプログラムを作成してHaskell開発のイメージを掴んでいきます。<br>
ファイル名はhoge.hs
```haskell
main = do
  print "Hello World!"
```
### ghcでコンパイル
ghcでコンパイルします。
```bash
$ ghc hoge.hs
```
コンパイルが正常に終了するとghcによって3つのファイルが作成される
- hoge（Windows では hoge.exe）
- hoge.hi
- hoge.o

まず、もっとも重要なファイルはhoge です。このファイルは実行可能バイナリであるため、そ
のまま実行できます。
試しに実行してみると以下のように結果が出力されます。
```bash
$ ./hoge
"Hello World!"
```
コンパイルされたファイルのデフォルトの振る舞いは、main のロジックを実行することです。デ
フォルトでは、コンパイルの対象となる Haskell プログラムには必ず main が含まれていなければ
なりません。

### ghci（Haskellの対話形式の REPL）
Haskell は、コンパイラの強力なサポートに加えて、使い勝手のよい自然な対話型環境を備えている点で独特です。
コードを書きながら出力してみる、というのが一般的なパターンですが、Haskellではコードを小さなブロックごとに書きながら対話形式で試すことができます。<br>
ghciを起動します。
```haskell
$ ghci
Prelude>
```

GHCi のもっとも重要な用途は、記述中のプログラムとのやり取りです。GHCiにファイルをロードする方法は2つ
```haskell
$ ghci hello.hs
[1 of 1] Compiling Main
Ok, modules loaded.
Prelude>
```
```haskell
もう 1 つは、対話型セッションで:l（:load）コマンドを使用する方法です。
$ ghci
Prelude> :l hello.hs
[1 of 1] Compiling Main
Ok, modules loaded.
*Main>
```
GHC でファイルをコンパイルする場合とは異なり、GHCi にロードするファイルには、main が
含まれていなくてもかまいません。ファイルをロードするたびに、関数と変数の既存の定義は上書
きされることになります。ファイルに変更を加えるたびに、ファイルを再びロードすることができ
ます。
