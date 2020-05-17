---
title: "Gatsby + Route53 + CloudFront + S3 + CodeBuildで静的サイトを公開した手順まとめ"
date: "2020-05-02T22:40:32.169Z"
template: "post"
draft: false
slug: "gatsby-aws-blog-start"
category: "Gatsby"
tags:
  - "Gatsby"
  - "JAMstack"
  - "S3"
  - "CloudFront"
  - "Route53"
  - "CodeBuild"
description: "Gatsbyとは一言でいえば静的サイトジェネレータ(Static Site Generator, SSG)です。静的サイトジェネレータとは事前ビルドによりあらかじめHTMLやCSSを生成しておいてユーザーからのアクセスがきた場合、リクエストに応じて対象のコンテンツを配信する方法になります。"
---

## Gatsbyとは
一言でいえば静的サイトジェネレータ(Static Site Generator, SSG)です。静的サイトジェネレータとは事前ビルドによりあらかじめHTMLやCSSを生成しておいてユーザーからのアクセスがきた場合、リクエストに応じて対象のコンテンツを配信する方法になります。

## JAMstackとは
またこのようなアーキテクチャをJAMstackともいいます。JAMstackとはJavaScript, API, Markupの略でNetrifyの創設者Matt Biilmann氏が提唱した新しいフロントエンドスタックのことです。リクエストを受けてからサーバー上で処理を行いレスポンスを返す従来のWebサイトの配信方法とは異なり、JAMstackでは事前にビルドされたHTMLをCDN上で配信します。
JAMstackは静的なassetをただホスティングするだけなのでオンコール体制が不要になるのが最大の利点です。なのでブログやLPとかには適しているアーキテクチャといえます。

## 静的サイトジェネレータの利点
+ ソースをサーバの設定や実行なしでAWS S3などのオンラインストレージに置くことができる。
+ アプリサーバー処理やDB処理などに起因するセキュリティを気にしなくてすむ（基本的にhtml + css + JavaScriptのみなので脆弱性は少ない）
+ サイトスピードが速い（あらかじめ生成されているコンテンツを配信するだけなので）
+ CDNとの相性がよくスケールしやすい
+ 環境構築が容易（DBと実行環境を準備するのは非常に手間ですよね）
+ インターネットにつながらない環境でも書ける

一方でデメリットとして複雑なこと（レコメンドや検索機能）などができなかったり情報量がそこまで多くなかったりします。

## 公開するまでの流れ
今回はこちらのテンプレートを使いました。
https://github.com/alxshelepenok/gatsby-starter-lumen

こちらの手順のみで`http://localhost:8000`が立ち上がります！
```
gatsby new blog https://github.com/alxshelepenok/gatsby-starter-lumen
cd blog
gatsby develop
```

## 設定
以下を自分用に書き換えるとひとまず完成します。
+ contentフォルダ配下のpagesの内容変更、postsの記事削除
+ staticフォルダ配下のphoto.jpgを自分のアイコンに変更、media配下の画像を削除
+ config.jsの内容を書き換え、名前やSNSなど

## S3で公開
今回はS3をWebサーバーのように使って、静的なページを公開する「Static website hosting」というストレージ機能を用いています。
デプロイの方法はGatsby公式ページに記載があるのでそれを参考に行いました。
[https://www.gatsbyjs.org/docs/deploying-to-s3-cloudfront/](https://www.gatsbyjs.org/docs/deploying-to-s3-cloudfront/)

## S3のメリット・デメリット
### メリット
- サーバーレスなのでサーバーの保守が不要
  - サーバーのダウンや監視・復旧などを気にする必要がない
  - 一つのAZ(アベイラビリティーゾーン)がダウンしても他の拠点で自動的に配信を継続できる
- リーズナブル
  - 料金はストレージに保存されている容量とリクエスト数、データ転送料金となっている
  - https://aws.amazon.com/jp/s3/pricing/
- 独自ドメインで配信できる
  - Route53を使うと独自ドメインで自サイトを公開できる
  - [Cloud Storage](https://cloud.google.com/storage/?hl=ja) だとwww.無しのドメインで公開することができない

### デメリット
- S3単体ではPHPやRubyといったサーバーサイドスクリプトを処理する機能がないのでWebアプリケーションなどは作成できない。
  - LambdaやDynamoDBと組み合わせることでサーバーレスアプリケーションを作ることができる。

## CodeBuildを使ってS3にデプロイ
CodeBuildを使ってgithubにあげたソースコードをビルドしS3にアップロードするようにしました。
また、デプロイ時にはCloudFrontのキャッシュクリアを自動でするよう設定しました。<br>
[AWS CloudFrontのキャッシュクリアをCodeBuildで自動化する](https://blog.harutowatanabe.com/posts/aws-cloudfront-codebuild)
<br>

料金も安価で、build.general1.smallを使って1か月あたりビルドを100分使用できます。またCodeBuildの無料利用枠は、12か月間のAWS無料利用枠の期間が終了しても自動的に期限切れになることはありません。
<br>
詳細はこちら
https://aws.amazon.com/jp/codebuild/

※ GitへのPushをトリガーにビルドを開始したい場合はプライベートリポジトリである必要があります。

## ドメインをRoute53で管理
[お名前ドットコム](http://www.onamae.com/)でドメインを取得して、AmazonRoute53を設定しました。
AmazonRoute53は可用性・拡張性の高いDNSサーバーをサービスとして使えます。
料金はDNSのゾーン単位で、ゾーンの利用+月100万クエリまでの問い合わせで1$と格安のもあり、勉強用途での利用を決めました。
設定も簡単でざっくり以下のの3ステップで完了します。
1. AmazonRoute53の管理画面を開いて、Hosted Zoneの作成
1. レコードのセット（Aレコードの作成）
1. お名前.comの管理画面上からネームサーバーをRoute53に変更。

## CloudFrontでホスティングをSSL/TLSに対応させる
以下の手順で完了します。
1. AWS Certificate Manager（ACM）で証明書の取得
1. CloudFrontディストリビューションの作成
1. Route53レコードのエイリアス先を`hogehoge.cloudfront.net.`にアクセス先を変更

※SSL証明書を使うには、バージニア北部リージョンでないといけないので切り替えが必要です。