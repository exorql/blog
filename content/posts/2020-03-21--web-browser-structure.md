---
title: "ブラウザレンダリングの仕組み"
date: "2020-03-21T22:12:03.284Z"
template: "post"
draft: false
slug: "web-browser-structure"
category: "フロントエンド"
tags:
  - "SEO"
  - "WEB"
  - "browser"
  - "UX"
description: "まず、ブラウザはリクエストしたURLからレンダリングに必要なリソースをサーバーから読み込みます。具体的には、HTMLファイル、CSSファイル、JavaScriptファイル、jpeg、png、gif、svgなどの画像ファイルをサーバからダウンロードします。"
---

<a href="https://www.amazon.co.jp/Web%E3%83%95%E3%83%AD%E3%83%B3%E3%83%88%E3%82%A8%E3%83%B3%E3%83%89-%E3%83%8F%E3%82%A4%E3%83%91%E3%83%95%E3%82%A9%E3%83%BC%E3%83%9E%E3%83%B3%E3%82%B9-%E3%83%81%E3%83%A5%E3%83%BC%E3%83%8B%E3%83%B3%E3%82%B0-%E4%B9%85%E4%BF%9D%E7%94%B0-%E5%85%89%E5%89%87-ebook/dp/B0728K5JZV/ref=as_li_ss_il?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&dchild=1&keywords=web+%E3%83%91%E3%83%95%E3%82%A9%E3%83%BC%E3%83%9E%E3%83%B3%E3%82%B9&qid=1589551035&sr=8-1&linkCode=li3&tag=10010d-22&linkId=e5d16c5fea0408dabcbb0b1952f6923e&language=ja_JP" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B0728K5JZV&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=10010d-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=10010d-22&language=ja_JP&l=li3&o=9&a=B0728K5JZV" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

[Webフロントエンドハイパフォーマンスチューニング](https://amzn.to/3abVW8T)
という本を読んだときのメモを残しておく。

サイトパフォーマンスを改善するためにはブラウザレンダリングの仕組みを知りボトルネックを突き止める必要があります。また普段の開発で実装を考える上でも重要な知識なってきますね。
## レンダリングの流れ
Loading→Scripting→Rendering→Paintingの4つの工程を分けて説明していきます。

### Loading
まず、ブラウザはリクエストしたURLからレンダリングに必要なリソースをサーバーから読み込みます。
具体的には、HTMLファイル、CSSファイル、JavaScriptファイル、jpeg、png、gif、svgなどの画像ファイルをサーバからダウンロードします。
そしてダウンロードしたリソースからHTMLとCSSの解析し、DOMツリーとCSSOMツリーを構築します。
解析されるHTMLの種類はHTML内に直接記述されている情報と外部データを参照する情報です。ブラウザは外部データに遭遇すると追加データを取得するため外部サーバーへリクエストを出します。

### Scripting
次に、レンダリングエンジンはJavaScriptのコードをJavaScriptエンジンに引き渡して実行させます。
JavaScriptのコード → 字句解析 → トークン列 → 構文解析 → 抽象構文木 → コンパイル → 実行可能コード → 実行

コードのトークン列をパースして抽象構文木を作成し、コンパイルすると、初めて実行可能になります。
JSの実行は、最初にJSファイルを読み込んだ時以外では、DOMイベントが発火しイベントリスナが起動する時にも起こる。

### Rendering
JavaScriptの実行が終わると、レイアウトツリーの構築（Rendering）が行われます。
ここでは2つの処理が行われています。
#### 1,スタイルの計算（CalculateStyle）
ドキュメントのDOMツリー内の全てのDOM要素に対して、どのようなCSSプロパティが当たるのかを計算する。
CSSルールのCSSセレクタがマッチするかを総当りで試行する。
そのあと個別のDOM要素に対して、どのようなCSSプロパティが適用されるかを判断します。

#### 2,レイアウト（Layout）
CSSプロパティを算出した後、レンダリングエンジンはDOMツリー内全てのノードの視覚的なレイアウト情報の計算、レイアウトを行う。
レイアウト情報とは
+ 要素の大きさ
+ 要素のmargin
+ 要素のpadding
+ 要素の位置
+ 要素のz軸の位置
のこと。

### Painting
レイアウト情報の算出が終わると、最後のレンダリング結果の描画に移ります。

#### 1,ペイント（Paint）
DisplayListと呼ばれる内部の低レベルな2Dグラフィックエンジン向けの命令の列を生成する。
この辺でブラウザの実装毎にグラフィックエンジンの異なりが出てくる。

#### 2,ラスタライズ（Rasterize）
生成された命令を用いて実際にピクセル(ビットマップ)へと描画する。
レイヤーごとに一枚一枚描画されていく。
レイヤーが生成されるのは要素に対して position や transform , opacityなどのプロパティが適用されている時。

#### 3,レイヤーの合成（CompositeLayers）
最後にピクセルにしたレイヤーを合成して最終的なレンダリング結果を生成。
レイヤーは基本的にCPUによって合成されるが、transformCSSプロパティに3D変形関数をつけたり、いくつかの条件を満たすとGPUによって合成される。

レンダリング完成
