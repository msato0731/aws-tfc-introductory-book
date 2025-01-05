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
cd aws-tfc-introductory-book-samples/infra/aws/chapter5/
```

## ディレクトリ構成

`prod`と`stg`ディレクトリがあり、それぞれ`main.tf`ファイルを配置しています。

```bash
aws-tfc-introductory-book-samples
└── infra
    └── chapter5
        └── aws
            ├── prod
            │   └── main.tf
            └── stg
                └── main.tf
```

## tfファイルの説明

以下のファイルをprodとstgのそれぞれのディレクトリに用意しています。

今回はファイルの中身が同じなのであまり意味がないですが、`main.tf`で環境差異を吸収するパターンを想定しています。

```hcl
terraform {
  required_version = "~> 1.10.2"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.82.2"
    }
  }
}

provider "aws" {
  region = "ap-northeast-1"
}

data "aws_availability_zones" "available" {}

locals {
  name     = "${basename(path.cwd)}-hcp-tf-aws-book"
  vpc_cidr = "10.0.0.0/16"
  azs      = slice(data.aws_availability_zones.available.names, 0, 3)
}

module "vpc" {
  source             = "terraform-aws-modules/vpc/aws"
  name               = local.name
  cidr               = local.vpc_cidr
  azs                = local.azs
  private_subnets    = [for k, v in local.azs : cidrsubnet(local.vpc_cidr, 8, k)]
  enable_nat_gateway = false
}

data "aws_ssm_parameter" "amazonlinux_2023" {
  name = "/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-6.1-x86_64" # x86_64
}

resource "aws_instance" "main" {
  ami           = data.aws_ssm_parameter.amazonlinux_2023.value
  instance_type = "t3.micro"
  subnet_id     = module.vpc.private_subnets[0]
  tags = {
    Name = local.name
    # 自動デプロイのテスト時にコメント外す
    # Env = "prod"
  }
}
```
