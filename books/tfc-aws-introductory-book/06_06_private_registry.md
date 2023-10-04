---
title: "　Private Registry"
---

## 概要

Terraform Provider/Module を組織内に共有する機能です。

共有可能な対象は以下です。

1. Public Provider/Module
   1. [registry.terraform.io](https://registry.terraform.io/)に登録されている、Provider/Module
   2. 例:
      1. [aws-provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
      2. [vpc](https://registry.terraform.io/modules/terraform-aws-modules/vpc/aws/latest)
2. Private Provider/Module
   1. 組織内で作成したProvider/Module
   2. 例
      1. <システム名>-vpc

ちなみに、Terraform Registryとの違いは以下のとおりです。

| 項目        | Terraform Registry | Private Registry                                         |
|-----------|--------------------|----------------------------------------------------------|
| 公開範囲      | Public(誰でも)        | Private(組織内)                                             |
| 対応しているVCS | GitHub             | Terraform Cloudが対応しているVCS<br/>(GitHub、GitLab、Bitbucket等) |

## ユースケース

- 組織内で推奨されるPublic Provider/Moduleを明確にしたい
- 自社で開発したProvider/Moduleを組織内で使いまわしたい

## 参考

- [プライベート レジストリ \- Terraform Cloud \| テラフォーム \| HashiCorp開発者](https://developer.hashicorp.com/terraform/cloud-docs/registry)
- [Terraform CloudのPrivate Registryを使ってmoduleを組織内にだけ公開してみる \| DevelopersIO](https://dev.classmethod.jp/articles/terraform-cloud-private-registry-module-publish/)