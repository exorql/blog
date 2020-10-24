---
title: "3Dメッシュ 入門"
date: "2020-10-24T22:40:32.169Z"
template: "post"
draft: false
slug: "openframeworks-mesh"
category: "openFrameworks"
tags:
  - "メディアアート"
  - "クリエイティブ・コーディング"
  - "3D"
description: "openFrameworksで3Dを表現する際の基本となるofMeshやメッシュを動的にしたい場合に使うofVboMeshなどを使って3Dオブジェクトを作成しました。
openFrameworksでは標準で3Dメッシュを表現するためのクラスがいくつかあるので紹介していきます。"
Image: "/media/3d-mesh.png"
Image2: "/media/3d-mesh-sin.png"
---

openFrameworksで3Dを表現する際の基本となるofMeshやメッシュを動的にしたい場合に使うofVboMeshなどを使って3Dオブジェクトを作成しました。<br>
openFrameworksでは標準で3Dメッシュを表現するためのクラスがいくつかあるので紹介していきます。

## Mesh（メッシュ）とは
Vertex（点）、Edge（辺）、Face（面）で構成された3Dオブジェクトのことをポリゴンメッシュといいます。<br>
ポリゴンというのは平面データであり、分割の細かさによって滑らかなや正確さがよりリアルに近づくが、ポリゴンの数が増えると描画処理に要する時間が長くなります。<br>
3DCGで立体の表面を形つくる時に一般的に使用されます。メッシュとはポリゴンの集まりのことを指します。<br>

## ofMeshとは
ofMeshとはポリゴンを構成する頂点の座標データとその色やテクスチャ座標の集まりを表します。<br>
このインスタンスは描画する際、頂点情報のみが必須で、あとは任意情報となります。例えばインデックス情報があれば頂点の個数を削減できますし、法線情報があればライティングによる陰影づけなどが可能となります。目的に応じて必要なデータを付与することができます。
https://openframeworks.cc/documentation/3d/ofMesh/

## ofVBO（Vertex Buffer Object）とは
VBOとは頂点バッファオブジェクトのことで、頂点データをGPU側にバッファとして確保し計算することで頂点データを多いモデルでも高速な処理を実現することができます。。ofMeshはCPU計算なので比較的重くなります。<br>
しかし、ofMeshのような便利な関数群がありません

## ofVboMeshとは
ofVboMeshはVBOを使ってmeshを描画することができます。<br>ofMeshを継承しているため便利な関数郡が呼び出せるのもありofVboMeshの利用を第一に考えるのが良いと思います。

## メッシュ板を生成
![3d-mesh](/media/3d-mesh.png)

まずは平面のメッシュ板を生成します。
<br>
ofApp.cppファイルのコード

```javascript
#include "ofApp.h"

//--------------------------------------------------------------
void ofApp::setup(){
    ofSetFrameRate(40);
    ofBackground(0); // 背景black
    ofSetColor(0,255,0);
     //板を生成
    ofPlanePrimitive plane;
    plane.set(600, 600, 30, 30);
     // 生成した板からメッシュを生成 メッシュの情報を抽出
    mesh = plane.getMesh();
}

//--------------------------------------------------------------
void ofApp::update(){
}

//--------------------------------------------------------------
void ofApp::draw(){
    cam.begin();
    ofRotateXDeg(-60);
    mesh.drawWireframe();
    cam.end();
}

```

ofApp.hファイルのコード
```javascript
#pragma once

#include "ofMain.h"

class ofApp : public ofBaseApp{

	public:
        void setup();
        void update();
        void draw();

        void keyPressed(int key);
        void keyReleased(int key);
        void mouseMoved(int x, int y );
        void mouseDragged(int x, int y, int button);
        void mousePressed(int x, int y, int button);
        void mouseReleased(int x, int y, int button);
        void mouseEntered(int x, int y);
        void mouseExited(int x, int y);
        void windowResized(int w, int h);
        void dragEvent(ofDragInfo dragInfo);
        void gotMessage(ofMessage msg);

        ofEasyCam cam; // カメラ
        ofVboMesh mesh; // VBOメッシュ
};
```

## sin波を使って3Dの波を生成
![3d-mesh-sin](/media/3d-mesh-sin.png)

頂点単位（z軸）で座標を変えていくことで面に動きをつけることができます。
<br>
3次元CGと座標系についてはこちら
http://www.f.waseda.jp/moriya/PUBLIC_HTML/education/classes/infomath6/applet/fractal/coord/

## ofNoise パーリンノイズ（Perlin noise）とは
グラデーションを表現するためにofNoise()関数を使用しました。<br>
パーリンノイズ（Perlin noise）とは、コンピュータグラフィックスのリアリティを増すために使われるテクスチャ作成技法です。
地形のシュミレーションなどで使われたりするノイズアルゴリズムで、入力値（x, y, z）の変化に応じて緩やかに変化させることができます。
<br>

ofApp.cppファイルのコードを以下に変更すると先ほど生成した3Dメッシュ板に動きが加わります。

```javascript
#include "ofApp.h"

//--------------------------------------------------------------
void ofApp::setup(){
    ofSetFrameRate(40);
    ofBackground(0); // 背景black
    ofSetColor(0,255,0);
     //板を生成
    ofPlanePrimitive plane;
    plane.set(600, 600, 30, 30);
     // 生成した板からメッシュを生成 メッシュの情報を抽出
    mesh = plane.getMesh();
}

//--------------------------------------------------------------
void ofApp::update(){
    ofColor color;
    for(int i=0; i < mesh.getVertices().size(); i++) {
        mesh.addColor(ofFloatColor(1.0, 1.0, 1.0, 1.0));  //色情報初期化
        float x = mesh.getVertices()[i].x;
        float y = mesh.getVertices()[i].y;
        //sin波を使って3Dの波を生成 ofGetElapsedTimef プログラム実行からの経過時間
        float z = sin(ofGetElapsedTimef() + x * 0.03) * 50.0;
        mesh.setVertex(i, glm::vec3(x,y,z));
        color.setHsb(ofMap(ofNoise(x * 0.005, y * 0.005, z * 0.005, ofGetFrameNum() * 0.008), 0, 1, 0, 150), 180, 230);
        mesh.setColor(i,color);
    }
}

//--------------------------------------------------------------
void ofApp::draw(){
    cam.begin();
    ofRotateXDeg(-60);
    mesh.drawWireframe();
    cam.end();
}
```