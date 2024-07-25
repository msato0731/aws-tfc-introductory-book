---
title: "　tfファイル作成"
---

:::message
本セクションでは、HCP Terraformを使用してデプロイする、EC2とVPC作成用のTerraformファイルを用意します。

:::

![](/images/chapter_5/03-diagram.png)

## サンプルリポジトリのfork

[サンプルリポジトリ](https://github.com/msato0731/aws-tfc-introductory-book-samples)をforkして、本チャプターのディレクトリに移動します。

```bash
git clone <forkしたリポジトリ>
cd aws-tfc-introductory-book-samples/infra/chapter5/
```

## ディレクトリ構成

`prod`と`stg`ディレクトリがあり、それぞれ`main.tf`ファイルを配置しています。

```bash
aws-tfc-introductory-book-samples
└── infra
  └── chapter5
    ├── ./prod
    │   └── ./prod/main.tf
    └── ./stg
        └── ./stg/main.tf
```

## tfファイルの説明

以下のファイルをprodとstgのそれぞれのディレクトリに用意しています。

今回はファイルの中身が同じなのであまり意味がないですが、`main.tf`で環境差異を吸収するパターンを想定しています。

https://github.com/msato0731/aws-tfc-introductory-book-samples/blob/main/infra/chapter5/prod/main.tf