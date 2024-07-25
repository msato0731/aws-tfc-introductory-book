---
title: "　Drift Detection"
---

## 概要

Health機能の一部で、Terraformコードと実際のインフラの差異(ドリフト)を検出する機能です。

Terraformで作成したリソースをコードを変更せずに、手動で変更するとコードと実際の設定値の間にドリフトが発生します。

ドリフトを放置しておくと、次にTerraformを適用した際に意図しない変更が発生する可能性があります。

Drift detectionを利用することで、ドリフトを早期に検知することが可能です。

注意点として、Health機能のチェック間隔は24時間のためドリフト後、即検知ということはできません。

![](/images/chapter_6/03-drift-detection.png)

## ユースケース

- ドリフトを早期に検知したい

## 参考

- [Health \- HCP Terraform \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/health?_gl=1*rszby1*_ga*Mzg1NTY4ODI4LjE2NTQxNTU5NjI.*_ga_P7S46ZYEKW*MTY2NzA5NDYxNy4yMC4xLjE2NjcwOTUwNTYuMC4wLjA.)
- [(チュートリアル)Manage resource drift \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/tutorials/state/resource-drift)
- [HCP TerraformでAWSリソースの手動変更を検出してみる\(Drift Detection\) \| DevelopersIO](https://dev.classmethod.jp/articles/terraform-cloud-drift-detection/)