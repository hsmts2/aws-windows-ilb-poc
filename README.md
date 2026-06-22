# aws-windows-ilb-poc

Windows Server と内部ロードバランサーを含む業務システム基盤を、公開用に抽象化した PoC ケーススタディです。

実務で扱った検証環境と本番相当環境の構成をもとに、VPC、Public / Private Subnet、NAT Gateway、Linux / Windows Server、Internal Load Balancer、RDS の役割と差分を整理しています。

公開にあたり、リソース名、インスタンス ID、グローバル IP、顧客情報、案件名、拠点情報、接続先情報は記載しません。

<br>

## 概要

このリポジトリでは、Windows Server を Private Subnet に配置し、内部ロードバランサー経由で通信を集約する構成を扱います。

検証環境では接続経路とアプリケーション動作を確認し、本番相当環境では 2AZ 構成、NAT Gateway 冗長化、Linux / Windows Server の複数台構成、RDS Multi-AZ を前提に整理しています。

<br>

## リポジトリの目的

- Windows Server を含む AWS 基盤構成の理解を示す
- 検証環境と本番相当環境の構成差分を説明できる形にする
- 内部ロードバランサーを利用する理由を、非公開構成、可用性、運用性の観点で整理する
- 実務由来の構成を、公開可能なドキュメントと CloudFormation サンプルへ抽象化する
- 後から再現・説明しやすいポートフォリオ用リポジトリとして整える

<br>

## アーキテクチャ

構成図は、実務PPTX原本をそのまま配置せず、公開用の名称へ置き換えて作成しています。

| 環境 | 構成図 | 内容 |
| --- | --- | --- |
| 検証環境 | [diagrams/windows-ilb-validation-architecture.svg](diagrams/windows-ilb-validation-architecture.svg) | 接続経路、最小サーバー構成、Single-AZ RDS を確認する環境 |
| 本番相当環境 | [diagrams/windows-ilb-production-architecture.svg](diagrams/windows-ilb-production-architecture.svg) | 2AZ、NAT Gateway x2、Linux / Windows Server x2、RDS Multi-AZ を想定した環境 |

<img src="./diagrams/windows-ilb-production-architecture.svg" alt="Production-like Windows ILB architecture" width="900">

詳細は [docs/architecture.md](docs/architecture.md) に整理しています。

<br>

## 検証環境と本番相当環境

| 観点 | 検証環境 | 本番相当環境 |
| --- | --- | --- |
| 目的 | 接続経路、アプリケーション動作、DB接続の確認 | 可用性、運用性、障害時の影響範囲を考慮した構成 |
| VPC CIDR | `172.28.48.0/20` | `172.28.32.0/20` |
| AZ | `ap-northeast-1a` / `ap-northeast-1c` | `ap-northeast-1a` / `ap-northeast-1c` |
| NAT Gateway | 1台構成を想定 | AZごとに1台 |
| Linux Server | 1台 | 2台 |
| Windows Server | 1台 | 2台 |
| Internal ALB | Linux 用 / Windows 用 | Linux 用 / Windows 用 |
| RDS | PostgreSQL / Single-AZ | PostgreSQL / Multi-AZ |
| 主な確認観点 | 経路確認、疎通確認、機能確認 | 冗長化、切り離し、メンテナンス、障害時対応 |

<br>

## サブネット構成

### 検証環境

| 用途 | AZ | 公開用サブネット名 | CIDR |
| --- | --- | --- | --- |
| Public | ap-northeast-1a | `validation-public-subnet-a` | `172.28.48.0/24` |
| Public | ap-northeast-1c | `validation-public-subnet-c` | `172.28.49.0/24` |
| Private App | ap-northeast-1a | `validation-private-app-subnet-a` | `172.28.50.0/24` |
| Private App | ap-northeast-1c | `validation-private-app-subnet-c` | `172.28.51.0/24` |
| Private DB | ap-northeast-1a | `validation-private-db-subnet-a` | `172.28.52.0/24` |
| Private DB | ap-northeast-1c | `validation-private-db-subnet-c` | `172.28.53.0/24` |

### 本番相当環境

| 用途 | AZ | 公開用サブネット名 | CIDR |
| --- | --- | --- | --- |
| Public | ap-northeast-1a | `production-public-subnet-a` | `172.28.32.0/24` |
| Public | ap-northeast-1c | `production-public-subnet-c` | `172.28.33.0/24` |
| Private App | ap-northeast-1a | `production-private-app-subnet-a` | `172.28.34.0/24` |
| Private App | ap-northeast-1c | `production-private-app-subnet-c` | `172.28.35.0/24` |
| Private DB | ap-northeast-1a | `production-private-db-subnet-a` | `172.28.36.0/24` |
| Private DB | ap-northeast-1c | `production-private-db-subnet-c` | `172.28.37.0/24` |

<br>

## 作成される主な AWS リソース

CloudFormation サンプルでは、本番相当環境をもとにした構成を作成します。

| リソース | 作成内容 |
| --- | --- |
| VPC | 本番相当環境用 VPC |
| Public Subnet | 2AZ の Public Subnet |
| Private App Subnet | Linux / Windows Server を配置する Private Subnet |
| Private DB Subnet | RDS を配置する Private Subnet |
| Internet Gateway | Public Subnet からの外部接続 |
| NAT Gateway | AZごとに1台 |
| EC2 | Linux Server x2、Windows Server x2、踏み台サーバー |
| Internal ALB | Linux Server 用、Windows Server 用の内部ロードバランサー |
| RDS | PostgreSQL / Multi-AZ のサンプルDB |
| Security Group | 踏み台、内部ALB、Linux、Windows、DBごとの通信制御 |

<br>

## CloudFormation テンプレート

| テンプレート | 内容 |
| --- | --- |
| [templates/prod-windows-ilb.yaml](templates/prod-windows-ilb.yaml) | 本番相当環境をもとにした VPC、Subnet、NAT Gateway、EC2、Internal ALB、RDS、Security Group |

このテンプレートは公開用の PoC サンプルです。実務構成をそのまま再現するものではありません。

Linux Server には Apache HTTP Server、Windows Server には IIS と簡易HTMLを配置し、Internal Load Balancer のヘルスチェックと振り分けを確認できる最小構成にしています。

<br>

## パラメータファイル

パラメータファイルのサンプルは以下に配置しています。

| ファイル | 内容 |
| --- | --- |
| [parameters/prod.example.json](parameters/prod.example.json) | 本番相当環境向けのサンプルパラメータ |

実際にデプロイする場合は、サンプルファイルをコピーして使用します。

```bash
cp parameters/prod.example.json parameters/prod.json
```

`parameters/prod.json` には、自分の環境に合わせた値を設定します。実環境の値を含むため、GitHub にはコミットしない運用とします。

<br>

## パラメータ

| パラメータ | デフォルト | 説明 |
| --- | --- | --- |
| `ProjectName` | `aws-windows-ilb-poc` | リソース名の接頭辞 |
| `EnvironmentName` | `prod` | 環境識別子 |
| `VpcCidr` | `172.28.32.0/20` | VPC CIDR |
| `PublicSubnetACidr` | `172.28.32.0/24` | Public Subnet A |
| `PublicSubnetCCidr` | `172.28.33.0/24` | Public Subnet C |
| `PrivateAppSubnetACidr` | `172.28.34.0/24` | Private App Subnet A |
| `PrivateAppSubnetCCidr` | `172.28.35.0/24` | Private App Subnet C |
| `PrivateDbSubnetACidr` | `172.28.36.0/24` | Private DB Subnet A |
| `PrivateDbSubnetCCidr` | `172.28.37.0/24` | Private DB Subnet C |
| `AllowedAdminCidr` | なし | 踏み台サーバーへ SSH 接続する管理元CIDR |
| `KeyName` | なし | EC2 キーペア名 |
| `LinuxAmiId` | なし | Amazon Linux 2023 AMI ID |
| `WindowsAmiId` | なし | Windows Server AMI ID |
| `DbMasterPassword` | なし | RDS 管理ユーザーのパスワード |

<br>

## ディレクトリ構成

```text
.
├── README.md
├── diagrams
│   ├── README.md
│   ├── windows-ilb-production-architecture.svg
│   └── windows-ilb-validation-architecture.svg
├── docs
│   ├── architecture.md
│   └── operations.md
├── examples
│   └── naming-and-parameters.example.md
├── parameters
│   └── prod.example.json
└── templates
    └── prod-windows-ilb.yaml
```

<br>

## 前提条件

- AWS CLI が利用できること
- CloudFormation スタックを作成できる IAM 権限があること
- EC2 キーペアを作成済みであること
- Amazon Linux 2023 と Windows Server の AMI ID を確認していること
- RDS パスワードを安全な値で用意していること

<br>

## デプロイ方法

### パラメータ準備

```bash
cp parameters/prod.example.json parameters/prod.json
```

`parameters/prod.json` の `<YOUR_...>` を自分の環境に合わせて変更します。

### テンプレート検証

```bash
aws cloudformation validate-template \
  --template-body file://templates/prod-windows-ilb.yaml
```

### スタック作成

```bash
aws cloudformation create-stack \
  --stack-name aws-windows-ilb-poc-prod \
  --template-body file://templates/prod-windows-ilb.yaml \
  --parameters file://parameters/prod.json
```

### スタック状態確認

```bash
aws cloudformation describe-stacks \
  --stack-name aws-windows-ilb-poc-prod
```

### 動作確認

- 踏み台サーバーへ管理者端末から SSH 接続できること
- 踏み台サーバー経由で Private Subnet 内の Linux / Windows Server へ接続できること
- Internal ALB のDNS名で、Linux / Windows Server 上のサンプルページへ通信できること
- Linux / Windows Server から RDS エンドポイントへ接続できること

### スタック更新

```bash
aws cloudformation update-stack \
  --stack-name aws-windows-ilb-poc-prod \
  --template-body file://templates/prod-windows-ilb.yaml \
  --parameters file://parameters/prod.json
```

### スタック削除

```bash
aws cloudformation delete-stack \
  --stack-name aws-windows-ilb-poc-prod
```

RDS は `DeletionPolicy: Snapshot` を指定しているため、削除時にスナップショットが作成されます。

<br>

## 公開用に対象外にしたもの

- 実環境のリソース名
- EC2 インスタンス ID
- グローバル IP アドレス
- 顧客名、案件名、拠点名
- 業務 PPTX 原本
- 実運用の接続先、通知先、認証情報

<br>

## 参考ドキュメント

- [docs/architecture.md](docs/architecture.md)
- [docs/operations.md](docs/operations.md)
- [examples/naming-and-parameters.example.md](examples/naming-and-parameters.example.md)
