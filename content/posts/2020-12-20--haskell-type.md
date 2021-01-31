---
title: "Haskellの型についてのまとめ"
date: "2020-12-20T22:40:32.169Z"
template: "post"
draft: false
slug: "haskell-type"
category: "haskell"
tags:
  - "関数型プログラミング"
description: "Haskellと他の関数型プログラミング言語との違いは、Haskellの型システムにあります。
Haskellの変数や関数で型を参照していないのは、型推論に大きく依存しているためです。
Haskellのスマートなコンパイラは、関数や変数の使い方をもとに使用されている型を特定することができます。"
---

<a href="https://www.amazon.co.jp/%E5%85%A5%E9%96%80Haskell%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0-Will-Kurt-ebook/dp/B07SFCMP66/ref=as_li_ss_il?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&dchild=1&keywords=%E5%85%A5%E9%96%80haskell&qid=1602255224&sr=8-1&linkCode=li3&tag=10010d-22&linkId=22ea6fa71a25f08eccd8692609089c3f&language=ja_JP" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B07SFCMP66&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=10010d-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=10010d-22&language=ja_JP&l=li3&o=9&a=B07SFCMP66" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

Haskellにおいてデータの型を作成することは、オブジェクト指向言語においてクラスを作成することと同じくらい重要です。<br>
今どきの型システムを体感してみたい人はぜひ触ってみてください。

今回はHaskellの型システムについてまとめました。[これ](https://blog.harutowatanabe.com/posts/haskell-intro)の続き。

### 目次
[一般的な型](#anchor1) <br>
[型コンストラクタとは](#anchor2)<br>
[型シノニムとは](#anchor3)<br>
[型クラスとは](#anchor4)<br>
[インスタンス宣言](#anchor5)<br>
[よく使う型クラス](#anchor6)<br>
[カインド](#anchor7)<br>


<a id="anchor1"></a>

## 一般的な型
- Int 整数型．有界．64bit 固定長整数
- Integer 整数型．非有界 大きい数を示すのに使えるがIntの方が効率的．
- Float 単精度浮動小数点 ( 32bit )
- Double 倍精度浮動小数点 ( 64bit )
- Bool 真理値型
- Char Unicode文字．文字のリストは文字列になる．

※有界とは物理メモリ上でのバイト単位の上限下限

## 型の定義
型定義は 関数名 :: 型 という形で行う。<br>
アロー演算子(->)で複数の型が繋がっている場合、一番最後の型が戻り値の型で、それ以外は引数の型

```haskell
bmi :: Int -> Int -> Int
bmi height weight = weight / height ^ 2
```

※関数定義の場合に型推論が可能なら型定義は省略できる。

## :tコマンドで型を確認する方法
```haskell
Prelude> aList = ["cat","dog","mouse"]
Prelude> :t aList
aList :: [[Char]]
```

<a id="anchor2"></a>

## 型コンストラクタとは
dataキーワードを使うと新しい型を定義していることをHaskellに認識させることができる。<br>
データコンストラクタを独自に定義すると、常に正しい型を使用しているかどうかをコンパイラでチェックできるようになる。<br>

### 例：Boolの定義
Bool型の場合、Boolが型コンストラクタでTrue or Falseが値コンストラクタと呼ぶ。
```haskell
data Bool = True | False
```

- 値コンストラクタ = データ型定義の、実際に型が取り得る値の種類を指定する
- 型コンストラクタ = データ型定義で、型を引数に取って新しい型をつくる。この引数は型引数と呼ばれる

Male と Female はどちらも値コンストラクタであり、値コンストラクタは（True | False）などと同じように扱うことができる。
```haskell
data Sex = Male | Female
```

<a id="anchor3"></a>

## 型シノニムとは
シノニムとは、別名という意味で、型シノニムは文字通り、ある型の別名を表す。<br>
定義するときは typeキーワードを使う。
```haskell
type Name = String
type Age = Int
type Height = Int
```

## レコード構文
レコード構文を使ってデータ型を定義すると各要素にラベルが付き、ラベルを使ってアクセスすることができる。
```haskell
data Name = Name String String
```

```haskell
data Name = Name {
  firstName :: String,
  lastName :: String
}
```

<a id="anchor4"></a>

## 型クラスとは
Haskellの型システムにおいて重要な抽象化の一つ。型クラスのクラスはオブジェクト指向におけるクラスとは異なりインタフェースや抽象クラスの位置付け。<br>
Haskellの型クラスは、共通の振る舞いを持つ型のグループを表す。

> 型クラスは、オブジェクト指向プログラミングにおける "クラス" と似通った概念ですが，レイヤが違う話なので注意が必要です。オブジェクト指向プログラミングにおけるクラスはデータ型であり，インスタンスはオブジェクトですが，Haskell における型クラスはデータ型のひとつ上の概念であり，インスタンスがデータ型です．
参照先 : http://walk.northcol.org/haskell/type-classes/#_%E5%9E%8B%E3%82%AF%E3%83%A9%E3%82%B9%E3%81%A8%E3%81%AF

#### 数値型全般を表すNum型クラスの場合
- Numは数の概念を一般化する型クラス。
- aはNumクラスを実装する型用のプレースホルダとしての型変数。
- Num型クラスはメソッド(+)、(-)などを持つ。
- Num型クラスのインスタンス（instance）は，具体的な数値型であるIntやDoubleなど。

```haskell
Prelude> :i Num
class Num a where
  (+) :: a -> a -> a
  (-) :: a -> a -> a
  (*) :: a -> a -> a
  negate :: a -> a
  abs :: a -> a
  signum :: a -> a
  fromInteger :: Integer -> a
  {-# MINIMAL (+), (*), abs, signum, fromInteger, (negate | (-)) #-}
        -- Defined in ‘GHC.Num’
instance Num Word -- Defined in ‘GHC.Num’
instance Num Integer -- Defined in ‘GHC.Num’
instance Num Int -- Defined in ‘GHC.Num’
instance Num Float -- Defined in ‘GHC.Float’
instance Num Double -- Defined in ‘GHC.Float’
```

※:iコマンドで型と型クラスの情報を確認する

## 型クラスの定義
```haskell
class 型クラス a where
  fun1 :: a -> a
  fun2 :: a -> String
  fun3 :: a -> String
  ...
```

<a id="anchor5"></a>

## インスタンス宣言
型クラスを実装する具象クラス (Haskellではデータ型) のことを、型クラスのインスタンスと呼ぶ。<br>
オブジェクト指向におけるインスタンス（クラスの実態）とは異なりクラスの実装にあたる。<br>
定義：instance 型クラス名 型名 where {関数 = 実装...}

```haskell
instance Show SixSidedDie where
  show S1 = "one"
  show S2 = "two"
  show S3 = "three"
```

<a id="anchor6"></a>

### よく使う型クラス
#### Ord 型クラス
- 順序付け可能な型
```haskell
Prelude> :t (>)
(>) :: Ord a => a -> a -> Bool
```

#### Eq 型クラス
- 等値性が評価可能な型
```haskell
Prelude> :t (==)
(==) :: Eq a => a -> a -> Bool
```

#### Num クラス
- 数値として扱われる型
```haskell
Prelude> :t 1
1 :: Num p => p
```

#### Bounded 型クラス
- Bounded型クラスのインスタンスは上限と下限を持つ
```haskell
Prelude> minBound :: Bool
False
Prelude> maxBound :: Bool
True
Prelude> minBound :: Int
-9223372036854775808
Prelude> maxBound :: Int
9223372036854775807
```

#### Show 型クラス
- Show型クラスが定義する重要な関数はshow
- show関数は値をString型に変換する
- GHCiで値が表示されるとしたら、それはShow型クラスのメンバだから
```haskell
Prelude> :t show
show :: Show a => a -> String
```

例えばBoolはShowを実装している
```haskell
Prelude> True
True
Prelude> False
False
```

## deriving
deriving宣言を使うとinstance宣言を省略できる。
```haskell
Prelude> data Coin = Front | Back deriving (Show)
Prelude> Front
Front
Prelude> Back
Back
```

## 代数的データ型
代数的データ型とは、他の型を組み合わせることで作成できる型のことです。<br>
型を組み合わせる方法は論理積（AND）を使って複数の型を組み合わせるか、論理和（OR）
を使って組み合わせるかの2つしかありません。

### 直積型
2つ以上の既存の型を「AND」を組み合わせることによって作成する。<br>
「直積型」という名前のせいで、複雑な手法に思えるかもしれませんが、どのプログラミング言
語においても、直積型は型を定義するためのもっとも一般的な手法です。直積型はほぼすべてのプ
ログラミング言語でサポートされています。

### 直和型
定義に並べた有限個の選択肢のうちの、どれか1つであるような型のこと。「OR」で組み合わせることによって作成する。


## パラメータ化された型
パラメータ化された型を利用すれば、既存のさまざまなデータに対応する汎用的なデータ構造を定義できるようになる。<br>
例えば以下のBox型は、他の任意の型を保持できる抽象コンテナで、Box に型を格納した瞬間に、Box型は具体的な値となる。
```haskell
Prelude> data Box a = Box a deriving Show
Prelude> n = 6 :: Int
Prelude> :t Box n
Box n :: Box Int
```

<a id="anchor7"></a>

## カインド
Haskellにはカインド（型の型）といった仕組みがある。<br>
値が何らかの型に属しているように型も何らかのカインドに属している。<br>
型のカインドは、その型がとるパラメータの数を示すもので、* を使って表現され、パラメータをとらない型のカインドは*、パラメータを 1 つとる型のカインドは* -> *、パラメータを 2 つとる型のカインドは* -> * -> *となる。

```haskell
Prelude> :k String
String :: *
Prelude> :k Maybe
Maybe :: * -> *
Prelude> :k IO
IO :: * -> *
```

## Maybe型
ほとんどの言語では、欠損値をnull値で表します。
Maybe型は、欠損しているかもしれない値をコンテキストで表すことで、ずっと安全なコードを記述できるようにします。
Maybe型により、null 値に関連するエラーはHaskellプログラムから体系的に取り除かれます。

Maybe型では、「プログラムの値が存在しないことがある」という、ただ1つの問題に対する解決策としてMaybeを使用する
