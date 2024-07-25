---
title: "Chapter4: HCP TerraformとAWSアカウントの準備"
---

:::message
本チャプターでは、HCP TerraformのOrganization/UserとAWSアカウントを用意します。

すでに用意できている場合は、スキップしてください。
:::

## HCP Terraform Organization・User作成

HCP TerraformにはFreeプランがあります。

Freeプランには一部の機能に制限はありますが、利用期間の制限はありません。

本書の内容は、基本的にFreeプランで試すことができます。

以下のリンクから HCP Terraformのアカウントを作成します。

[https://app.terraform.io/public/signup/account](https://app.terraform.io/public/signup/account)

メールアドレス等や必要な情報を入力します。

![](/images/chapter_4/tfc-create-account-1.png)

確認用のメールが届くため、メール内のリンクを選択します。

![](/images/chapter_4/tfc-create-account-2.png)

`create new organization`を選択します。

![](/images/chapter_4/tfc-create-account-3.png)

任意の`Organization name`を設定してOrganizationを作成します。

![](/images/chapter_4/tfc-create-account-4.png)

`Create a new Workspace`の画面が出てきたら、Organizationの作成とHCP Terraformユーザーの作成は完了です。

![](/images/chapter_4/tfc-create-account-5.png)

## AWSアカウントの作成

もし、AWSアカウントを持っていない場合は、以下の手順でAWSアカウントを作成してください。

[AWS アカウント作成の流れ【AWS 公式】](https://aws.amazon.com/jp/register-flow/)
