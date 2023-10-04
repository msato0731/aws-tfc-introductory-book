---
title: "　Ephemeral workspaces"
---

## 概要

Workspaceに削除日を指定することで、自動でWorkspaceのリソースを削除する機能です。

指定時間になると、Destroy用のRunが実行されます。

リソースを自動で削除する仕組みは、色々な方法で構築することができます。

自前で作る場合、構築や運用の手間がかかります。

Ephemeral workspacesを使うことで、少ない手間で自動削除を実現できます。

![](/images/chapter_6/05-ephemeral-workspaces.png)

### ユースケース

- 検証用のリソースを自動で削除したい

## 参考

-[Destruction and Deletion \- Workspaces \- Terraform Cloud \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/settings/deletion)
- [\[アップデート\]Terraform Cloudにリソースの自動削除ができるephemeral workspaces機能が追加されました\(パブリックベータ\) \| DevelopersIO](https://dev.classmethod.jp/articles/tfc-ephemeral-workspaces/)