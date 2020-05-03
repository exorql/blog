---
title: "AWS CloudFrontのキャッシュクリアをCodeBuildで自動化する"
date: "2020-05-03T22:40:32.169Z"
template: "post"
draft: false
slug: "aws-cloudfront-codebuild"
category: "AWS"
tags:
  - "S3"
  - "CloudFront"
  - "Route53"
  - "CodeBuild"
description: "デプロイするタイミングで必ずキャッシュクリアしたいのでCodeBuildで自動化しました。以下のようにbuildspec.ymlに一行追加するのみで設定できます。これでS3にファイルがアップロードされるタイミングでCacheを無効化してくれます。"
---

今回はCloudFrontのキャッシュ削除方法についてマネージドコンソールで削除する方法とCodebuildで実行する方法の2つをご紹介します。

## マネージドコンソールで実施する方法
CloudFrontのサービスページにアクセスし以下の3ステップで実行できます。
1. CloudFrontディストリビューションのDistribution IDのリンクをクリック
1. Invalidationsタブをクリックし、Create Invalidationsボタンをクリック
1. 削除したいオブジェクトを指定して実行（ex. `/*`）

## Codebuildで実行する方法
上記の方法でも良いですが、デプロイするタイミングで必ずキャッシュクリアしたいのでCodeBuildで自動化しました。
以下のように`buildspec.yml`に一行追加するのみで設定できます。
これでS3にファイルがアップロードされるタイミングでCacheを無効化してくれます。
※環境変数はCodeBuild のプロジェクト編集画面で設定できます。
```
version: 0.2
phases:
  install:
    commands:
     - yarn
  pre_build:
    commands:
      - yarn build
  build:
    commands:
      - echo run build command.
      - aws s3 sync ./public s3://${bucket_name} --delete --acl "public-read"
      ## キャッシュをクリアする
      - aws cloudfront create-invalidation --distribution-id ${cloudfront_distribution_id} --paths '/*
```

## ビルド実行前にIAMポリシーのアタッチ
上記の設定でもビルド自体は実行できますが、このままではCloudFrontへのアクセス権限がないので、パーミッションエラーが発生します。IAMロールにポリシーをアタッチしましょう。

## キャッシュ削除に対する料金
CloudFrontキャッシュ削除は1ヶ月のうち1000回まで無料となっています。個人開発用途などであれば超えることはそうないと思いますが、自動化する場合は、頻度の設定にご注意ください。
※これを超えると1つのパスにつき1件ごとに料金が発生します。
[公式ドキュメント](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html#PayingForInvalidation)