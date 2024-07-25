---
title: "　Cost Estimation"
---

## 概要


作成されるリソースのコストを推定する機能です。(デフォルトで有効)

Plan時に実行されます。

以下のように、時間(1ごと時間)/月/月差分が表示されます。

![](/images/chapter_6/02-cost-estimation.png)

この機能で推定されるコストはポリシー機能でも利用できます。

例えば、100USD以上のリソースのデプロイ禁止するといったポリシーを作成できます。

## ユースケース

- 変更するリソースの推定金額を確認したい

## 参考

- [Overview \- Cost Estimation \- HCP Terraform \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/cost-estimation)