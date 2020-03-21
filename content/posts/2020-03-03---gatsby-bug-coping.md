---
title: "Gatsby.jsの開発でエラーに遭遇した際の対処法まとめ"
date: "2020-03-03T23:46:37.121Z"
template: "post"
draft: false
slug: "gatsby-bug-coping"
category: "Gatsby"
tags:
  - "Gatsby"
  - "JavaScript"
description: "Gatsbyでブログを公開した時に遭遇したエラーとその対処法をメモとして残しておきます。内容的にはほとんど普段のフロントエンド開発に共通して行う作業になりました。"
---

Gatsbyでブログを公開した時に遭遇したエラーとその対処法をメモとして残しておきます。内容的にはほとんど普段のフロントエンド開発に共通して行う作業になりました。。

## Cannot find module 'react'
npm installしたあとに gatsby developすると発生する不具合。
解決策はyarnでinstallし直す。Gatsbyでのパッケージインストールはyarnを使うと良い。
```
yarn add gatsby-plugin-typography react-typography typography typography-theme-fairy-gates
```

## 原因がよく分からない時の対応策1
`node -v`でnodeのversionが問題ないか確認する
https://nodejs.org/ja/

使っているnodeバージョンがnpmでインストールしている最新のパッケージと互換がないのかもしれません。
例えばnodeは古いのにパッケージはそれをサポートしていないとか。

## 原因がよく分からない時の対応策2
```
1. killall node # nodeのプロセスを殺しています。どこかでlocalServerが立っている可能性を消しています
2. rm -r node_modules # 依存関係が書かれているファイルを削除してます。これはnpm i時に自動で生成されます
3. rm package.lock.json (rm yarn.lock) package.lock.jsonかyarn.lock
4. npm i or yarn i # モジュールを入れ直す