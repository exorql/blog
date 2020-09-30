---
title: "openFrameworksで3Dグラフィックス"
date: "2020-09-27T22:40:32.169Z"
template: "post"
draft: false
slug: "openframeworks-3d"
category: "openFrameworks"
tags:
  - "メディアアート"
  - "クリエイティブ・コーディング"
  - "3D"
description: "前回はopenFrameworksの開発環境構築についてまとめたので、今回は実際にopenFrameworksで3Dグラフィックスをつくっていきます。
openFrameworksの描画の基本はOpenGLです。OpenGLはそもそも３次元のグラフィクスの描画のために開発されたライブラリであり、最初から３次元空間をとり扱うための様々な機能が備わっています。ですので今回は3Dグラフィックスに、カメラ（視点）、ライティング、光と影（シェーディング）のプログラムを加えていきます。
"
Image: "/media/openframeworks-3d.png"
---

## はじめに
前回はopenFrameworksの[開発環境構築](https://blog.harutowatanabe.com/posts/openframeworks-dev)についてまとめたので、今回は実際にopenFrameworksで3Dグラフィックスをつくっていきます。<br>
openFrameworksの描画の基本はOpenGLです。OpenGLはそもそも３次元のグラフィクスの描画のために開発されたライブラリであり、最初から３次元空間をとり扱うための様々な機能が備わっています。ですので今回は3Dグラフィックスに、カメラ（視点）、ライティング、光と影（シェーディング）のプログラムを加えていきます。<br>
※今回はこちらの[資料](https://drive.google.com/file/d/0BzyVHU69QO3mM01QMDhhRkpTdUk/view)の前半部分を参考に学習したことをまとめました。

## 使用するクラス
- ofBox ofSphere - 3Dプリミティブオブジェクトの描画
- ofEasycam -	簡単に利用可能なカメラ(視点)機能
- ofLight -	ライティング効果

openFrameworksの公式リファレンス
https://openframeworks.cc/documentation/

## 3Dプリミティブの描画

![openframeworks-3d](/media/openframeworks-3d.png)

今回は上記のようなアウトプットを出力するためにofApp.hとofApp.cppに以下のプログラムを記載します。
新規プロジェクトを立ち上げて以下のソースコードを貼り付けてみてください。<br>
ofApp.hに書く必要なクラスのインスタンスは以下。
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

      ofBoxPrimitive box; // 立方体
      ofSpherePrimitive sphere; // 球
      ofEasyCam cam; // カメラ
      ofLight light; // ライト
};
```

ofApp.cppに書く実行プログラムは以下。
```javascript
#include "ofApp.h"

//--------------------------------------------------------------
void ofApp::setup(){
    ofBackground(0);
    ofEnableDepthTest(); // 深度テストを有効に
    ofEnableSmoothing(); // 表示をスムーズに
    
    // ライティングを有効に
    light.enable();
    // スポットライトを配置
    light.setSpotlight();
    // 照明の位置
    light.setPosition(-10, 100, 100);
    // 環境反射光の色
    light.setAmbientColor(ofFloatColor(0.5, 0.2, 0.2, 1.0));
    // 拡散反射光の色
    light.setDiffuseColor(ofFloatColor(0.5, 0.5, 1.0));
     // 鏡面反射光の色
    light.setSpecularColor(ofFloatColor(1.0, 1.0, 1.0));
}

//--------------------------------------------------------------
void ofApp::update(){

}

//--------------------------------------------------------------
void ofApp::draw(){
    // cam.begin();とcam.end();で囲まれた範囲が、マウスのドラッグで視点変更可能になる
    cam.begin();
    
    ofSetColor(0,255,0);
    
    // 立方体
    box.set(200); // サイズ設定
    box.setPosition(-150, 0, 0); // 位置
    box.drawWireframe(); // ワイヤーフレームを描画
    
    // 球
    sphere.set(100, 16); // 半径と面の細かさ
    sphere.setPosition(150, 0, 0);
    sphere.drawWireframe();
    
    cam.end();
}

```