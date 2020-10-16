---
title: "Haskell入門 基本構文"
date: "2020-10-10T22:40:32.169Z"
template: "post"
draft: false
slug: "haskell-intro"
category: "haskell"
description: "Haskellを学習中なので理解を深めるためにもまとめていきます。今回はHaskellでプログラムを書くために必要な基礎的な文法を紹介します。"
---

<a href="https://www.amazon.co.jp/%E5%85%A5%E9%96%80Haskell%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0-Will-Kurt-ebook/dp/B07SFCMP66/ref=as_li_ss_il?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&dchild=1&keywords=%E5%85%A5%E9%96%80haskell&qid=1602255224&sr=8-1&linkCode=li3&tag=10010d-22&linkId=22ea6fa71a25f08eccd8692609089c3f&language=ja_JP" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B07SFCMP66&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=10010d-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=10010d-22&language=ja_JP&l=li3&o=9&a=B07SFCMP66" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

Haskellを学習中なので理解を深めるためにもまとめていきます。今回はHaskellでプログラムを書くために必要な基礎的な文法を紹介します。<br>
Haskellの環境構築はこちらで。<br>
https://blog.harutowatanabe.com/posts/haskell-dev

## 四則演算
```haskell
Prelude> 7 + 6
13
Prelude> 7 - 6
1
Prelude> 7 * 6
42
Prelude> 7 / 6
1.1666666666666667
Prelude> 7 ^ 6
117649
Prelude> 7  `mod` 6
1
Prelude> mod 7 6
1
```
剰余はmodで求めます。
\``で囲むことで中置記法として記述できます。前置記法でも実行できます。

## 等価比較
```haskell
Prelude> 7 == 7
True
Prelude> 7 /= 7
False
```
!=ではなく/=

## 論理演算
```haskell
Prelude> True && False
False
Prelude> True || False
True
Prelude> not True
False
```
論理積と論理和は多くの言語と同じ記号が使えます。
論理否定は多くの言語では`!` を使いますがhaskellではnotと書きます

## グローバル変数
```haskell
Prelude> x = 3
Prelude> print x
3
```
haskellではトップレベル変数ともいう。

## ローカル変数
### let
```haskell
Prelude> let x = 3
Prelude> print x
3
```
おなじみのletで定義

### where
```haskell
myGCD a b = if result == 0
    then b
    else myGCD b result
    where result = a `mod` b
```
haskellでは関数の末尾にwhere句を使って一時変数を定義できます。
haskellでは変数の束縛（バインド）という。

## if文
```haskell
myGCD a b = if result == 0
    then b
    else myGCD b result
    where result = a `mod` b
```
haskellのif文では必ず戻り値を返さないといけないため`else`が必須になります。

## 関数定義
```
hoge a = a + 1
```
関数名 引数 = 関数の振る舞い

## ラムダ関数
```
hoge = \a ->  a + 1
```
\ 引数 -> 式

ラムダ関数は値を受け取って返すだけの必要最低限の関数です。
バックスラッシュはギリシャ文字のラムダ（λ）を連想させる<br>
通常の関数定義では引数を左辺に定義していたのに対しラムダは右辺に定義します。
※詳細は別途まとめます

## リスト
```haskell
Prelude> let x = [1,2,3,4,5,6]
Prelude> 1:2:3:4:5:6:[]
[1,2,3,4,5,6]
Prelude>  [1..10]
[1,2,3,4,5,6,7,8,9,10]
```

リストを作成するもっとも単純な方法は、値を空のリストとコンス（中置演算子（:））すること
リストの型はすべて同じものでなければならない

```haskell
Prelude> [1,3 .. 10] 
[1,3,5,7,9]
Prelude> [1,0 .. -10]
[1,0,-1,-2,-3,-4,-5,-6,-7,-8,-9,-10]
```
このように様々なデータを素早く生成することもできます。

## リストで使う一般的な関数
### インデックスを使ってアクセスしたい場合 !! を使う
```haskell
Prelude> let x = [1,2,3,4,5,6]
Prelude> x !! 0
1
```

```haskell
Prelude> (!!) [1,2,3,4] 0
1
```
(!!) [1,2,3] 0 前置表記も可能 部分適用などが容易になることがよくあります

### head
```haskell
Prelude> let x = [1,2,3,4,5,6]
Prelude> head x
1
```
リストの最初の要素を表示

### tail
```haskell
Prelude> tail x
[2,3,4,5,6]
```
head の後にある残りの部分

### length
```haskell
Prelude> length [1..20]
20
```
リストの長さを取得

### elem
```haskell
Prelude> elem 51 [0..100]
True
```
引数として値とリストを受け取り、その値がリストに含まれているかどうかをチェック

### take
```haskell
Prelude> take 7 [0..100]
[0,1,2,3,4,5,6]
```
引数として数値とリストを受け取り、リストの先頭から指定された数の要素を取り出す

### drop
```haskell
Prelude> drop 7 [0..10]
[7,8,9,10]
```
リストの先頭から指定された数の要素を削除する

### cycle （無限リスト）
```haskell
Prelude> take 10 (cycle [1,2,3])
[1,2,3,1,2,3,1,2,3,1]
```
haskellは遅延評価なので無限の長さの配列を作ることができる

## タプル
```haskell
Prelude> name = ("Michael", "Jordan")
Prelude> fst name
"Michael"
Prelude> snd name
"Jordan"
```
複数の値を括弧で囲って表現することをタプルという。
複数の型を含むことができる。

タプルにはfstとsnd という 2 つの有益な関数があり、
fst はタプルの 1 つ目の要素にアクセスし、snd はタプルの 2 つ目の要素にアクセスする。

### zip
```haskell
Prelude> zip "hoge" "fuga"
[('h','f'),('o','u'),('g','g'),('e','a')]
```
2 つのリストを組み合わせてタプルのペアを作成したい場合

## パターンマッチング
```haskell
omikuji :: Int -> String
omikuji 7 = "hit"
omikuji y = "lose"
```
このようなパターンマッチを実行してみると以下になる。

```haskell
*Main> omikuji 7
"hit"
*Main> omikuji 4
"lose"
*Main> omikuji 111
"lose"
```

パターンマッチは引数の値に応じて返す値を振り分けることができる。
if/elseで実現したいことをこのように関数を分割定義することで可能にしてくれる。

使用しない値を表すワイルドカードとしてアンダースコア（ ）を使用する。
