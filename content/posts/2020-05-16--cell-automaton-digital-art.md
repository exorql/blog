---
title: "セルオートマトンで描くジェネラティブアート"
date: "2020-05-16T22:40:32.169Z"
template: "post"
draft: false
slug: "cell-automaton-digital-art"
category: "ジェネラティブアート"
tags:
  - "math"
  - "programming"
  - "art"
description: "格子状のセルと単純な規則による、離散的計算モデルである。計算可能性理論、数学、物理学、複雑適応系、数理生物学、微小構造モデリングなどの研究で利用される。非常に単純化されたモデルであるが、生命現象、結晶の成長、乱流といった複雑な自然現象を模した、驚くほどに豊かな結果を与えてくれる。"
rule90lImage: "/media/rule90.png"
Image: "/media/probability-ca.png"
cell: "/media/cell-automaton2.png"
drawCell: "/media/draw-cell-automaton2.png"
---


## セルオートマトンとは
_格子状のセルと単純な規則による、離散的計算モデルである。計算可能性理論、数学、物理学、複雑適応系、数理生物学、微小構造モデリングなどの研究で利用される。非常に単純化されたモデルであるが、生命現象、結晶の成長、乱流といった複雑な自然現象を模した、驚くほどに豊かな結果を与えてくれる。[wikipedia](https://ja.wikipedia.org/wiki/%E3%82%BB%E3%83%AB%E3%83%BB%E3%82%AA%E3%83%BC%E3%83%88%E3%83%9E%E3%83%88%E3%83%B3)_
<br>

簡単に言うと、セルオートマトンとはセル（状態をもつマス目）と遷移ルールによって、段階的に遷移するシステムのことです。

## 1次元セルオートマトン
1次元セルオートマトンは名前の通り、1行に並んだセルで表現したものです。
セルオートマトンの中で一番単純なもので、左右に隣接するセルの状態から次の世代の状態が決まります。
一次元セルオートマトンで紹介する以下のルール30・ルール90は、イギリスの理論物理学者である[スティーブン・ウルフラム](https://ja.wikipedia.org/wiki/%E3%82%B9%E3%83%86%E3%82%A3%E3%83%BC%E3%83%96%E3%83%B3%E3%83%BB%E3%82%A6%E3%83%AB%E3%83%95%E3%83%A9%E3%83%A0)によって考案された1次元セルオートマトンのルールの代表的なモデルです。

### ルール30
全てのセルはその現在の状態とルールに基づいて変化することができます。
<br>
ルール30の場合、次の状態へと変化させるルールが以下になります。

| 現在の状態| 111 | 110 | 101 | 100 | 011 | 010 | 001 | 000 | 
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 中央のセルの次の状態 | 0 | 0 | 0 |  1 | 1 | 1 | 1 | 0 |

この00011110を10進数に変換すると30になるためルール30と呼ばれています。

### ルール90
![ルール90](/media/rule90.png)

ルール90では、フラクタル図形（自己相似性や再帰性を持つ図形）のシェルピンスキーのギャスケット（再帰的な三角形分割ルールによって書かれる図形）を生成します。
<br>
例えば、上記の画像のように三角形の中に三角形があり、その中にまた三角形がありと繰り返し構造になっているのが分かると思います。これはまさしく「再帰性」と三角形の分割ルールによってこのような形になっています。
<br>
ルール90の場合、次の状態へと変化させるルールが以下になります。

| 現在の状態| 111 | 110 | 101 | 100 | 011 | 010 | 001 | 000 | 
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 中央のセルの次の状態 | 0 | 1 | 0 |  1 | 1 | 0 | 1 | 0 |

### 確率的セルオートマトン
セルオートマトンは人工生命の分野でも有名で、セルは細胞の状態変化のような挙動をします。実際の自然現象における状態変化は一定ではなく偶発的な差異によって挙動が変わることも起こります。

![確率的セルオートマトン](/media/probability-ca.png)

以下が実際のソースコードになります。ルール90のプログラムで使ったtransition関数にランダム要素を加えただけになりますが、これだけでも毎回違う結果になるのがわかります。
興味がある方は手元で動かしてみてください。<br>
※ 実行環境はprocessingです

```
int num = 200;  //表示する世代数
int mod = 2;  //法とする数
int[] state = {1};  //初期状態
int gen = 0;
void setup(){
  size(1000, 500);
  colorMode(HSB, 1);
  background(0, 0, 1);
}
void draw(){
  if (gen < num){
    drawCell(gen);
    updateState();
  }
}
void mouseClicked(){
  gen = 0;
  state = new int[]{1};  //初期状態
  mod = int(random(2, 20));
  println(mod);
  background(0, 0, 1);
}

void drawCell(float y){
  float scalar = width * 0.5 / num; // セルの大きさ
  float x = (width - state.length * scalar) * 0.5; // セルのx座標
  y *= scalar;
  noStroke(); //輪郭線を描かない
  for (int i = 0; i < state.length; i++){
    fill(state[i] * 1.0 / mod, state[i] * 1.0 / mod, 1);
    rect(x, y, scalar, scalar); // 四角形を描画する関数
    x += scalar; // x座標方向にセルをずらす
  }
}

// 簡単なランダム関数を使って状態遷移ルールのプログラムに偶発性を入れます。
int transition(int a, int b, int c){
  int d;
  if (random(1) < 0.999) {
    d = a + b + c; //99.9%の確率でこのルールを選択
  } else {
    d = a + c; //0.1%の確率
  }
  d %= mod;
  return d;
}

void updateState() {
  int[] BOUNDARY = {0, 0};
  int[] nextState = new int[state .length + 2]; // 次の世代の状態
  state = splice(state, BOUNDARY, 0); // 既存の配列に境界値を加える
  state = splice(state, BOUNDARY, state.length); // 配列の最後に境界値を加える
  for(int i=1; i < state.length -1; i++){
    nextState[i-1] = transition(state[i-1], state[i], state[i+1]); //次世代のセルの状態の計算
  }
  state = nextState; //セルの状態を更新
  gen++; //世代を一つ増やす
}
```

## 2次元セルオートマトン
1次元セルオートマトンは1行に並べられたセルの時間発展を表したものでした。
2次元セルオートマトンは縦横2つの方向に隣接するセルから中央のセルが決まるものになります。
2次元セルオートマトンでは、ノイマン近傍とムーア近傍と呼ばれる有名な定義があります。ノイマン近傍は以下のように4つのセルを近傍とするのに対し、ムーア近傍は斜めも含む8つのセルを近傍とします。
[wikipedia](https://ja.wikipedia.org/wiki/%E3%82%BB%E3%83%AB%E3%83%BB%E3%82%AA%E3%83%BC%E3%83%88%E3%83%9E%E3%83%88%E3%83%B3#%E6%A6%82%E8%A6%81)

![2次元セルオートマトン・ノイマン近傍](/media/cell-automaton2.png)

ノイマン近傍の定義を遷移ルールに適用して描いたのが以下です。

![2次元セルオートマトン](/media/draw-cell-automaton2.png)

ソースコードは以下になります。
```
int num = 250; //行と列の長さ
int mod = 2; // 法とする数
int[][] state = new int[num][num]; //セルの状態を表す行列

void setup(){
  size(500, 500);
  colorMode(HSB, 1);
  initialize();
}
void draw(){
  drawCell();
  updateState();
}

void drawCell() {
  float scalar = (float) height / num; // セルのサイズ
  float y = 0; // セルのy座標
  float x;
  for (int i=0; i < num; i++){
    x = 0;  // セルのx座標
    for (int j = 0; j < num; j++){
      noStroke();
      fill(state[i][j] * 1.0 / mod, state[i][j] * 1.0 / mod, 1); //セルの色
      rect(x, y, scalar, scalar);
      x += scalar;
    }
    y += scalar;
  }
}

// 初期状態にする関数
void initialize() {
  for (int i=0; i < num; i++){
    for (int j=0; j < num; j++){
      if (i == num / 2 && j == num /2){
        state[i][j] = 1; // 真ん中の成分のみ１
      } else {
        state[i][j] = 0;
      }
    }
  }
}

// 状態を更新する関数
void updateState() {
  int[][] nextState = new int[num][num]; // 次世代の状態
  for (int i=0; i < num ; i++) {
    for (int j=0; j < num;  j++) {
      nextState[i][j] = traonsition(i, j); //　遷移
    }
  }
  state = nextState; //　更新
}

// 遷移ルール
int traonsition(int i, int j){
  int nextC;
   nextC = state[(i - 1 + num) % num][j] // 上のセル 
     + state[i][(j - 1 + num) % num] // 左のセル
     + state[i][j] // 中央のセル
     + state[i][(j + 1) % num] // 右のセル
     + state[(i + 1) % num][j]; // 下のセル
   nextC %= mod;
   return nextC;
}

void mouseClicked(){
  initialize();
  mod = int(random(2, 20));
  println(mod);
  background(0, 0, 1);
}
```