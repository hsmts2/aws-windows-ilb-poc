# aws-windows-ilb-poc

## 概要

Windows Server を利用した業務システム基盤を題材に、AWS上での内部ロードバランサー（ILB）、プライベートサブネット、踏み台サーバー、DB接続を含む構成を整理したPoCケーススタディです。

本リポジトリは、実務で扱った構成要素をもとにしていますが、公開にあたりリソース名、IPアドレス、インスタンスID、顧客情報、拠点情報などはすべて匿名化しています。

## 実務背景と抽象化範囲

検証環境と本番相当環境の構成差分を整理し、Windows Server を含む業務アプリケーション基盤を第三者に説明できる形へ再構成しています。

公開用資料では、実環境の値や固有名詞を使わず、設計パターン、役割、ネットワーク分離、可用性、運用観点に焦点を当てます。

## リポジトリの目的

- Windows Server を含むAWS基盤構成の理解を示す
- Public / Private Subnet、NAT、踏み台、内部ロードバランサーの設計意図を説明する
- 内部LBを使う理由を、セキュリティ、可用性、運用性の観点で整理する
- 検証環境と本番相当環境の違いを整理する
- 実務構成を公開可能なドキュメントへ抽象化する力を示す

## アーキテクチャ

構成の詳細、検証環境と本番相当環境の差分、設計意図は以下に整理しています。

| ドキュメント | 内容 |
| --- | --- |
| [docs/architecture.md](docs/architecture.md) | 公開用に抽象化したAWS構成、内部LBの役割、環境差分 |
| [docs/operations.md](docs/operations.md) | 接続、メンテナンス、障害時の考え方 |
| [examples/naming-and-parameters.example.md](examples/naming-and-parameters.example.md) | 公開用の命名例、パラメータ例、匿名化ルール |

## 想定構成

| 環境 | 概要 |
| --- | --- |
| 検証環境 | 最小構成でアプリケーション動作と接続経路を確認する環境 |
| 本番相当環境 | 複数台構成、内部ロードバランサー、DB接続を含む環境 |

## 作成される主な AWS リソース

| リソース | 役割 |
| --- | --- |
| VPC | 業務システム用ネットワーク |
| Public Subnet | 踏み台サーバー、NAT Gatewayなどを配置 |
| Private Subnet | Windows Server、アプリケーションサーバー、DB接続先を配置 |
| Internal Load Balancer | Private Subnet内のサーバーへ内部向けに負荷分散 |
| EC2 | Windows Server、踏み台、アプリケーションサーバー |
| RDS | アプリケーション用データベース |
| Security Group | 接続元と接続先を役割単位で制御 |

## CloudFormation テンプレート

本番相当環境のサンプルテンプレートを以下に配置しています。

| テンプレート | 内容 |
| --- | --- |
| [templates/prod-windows-ilb.yaml](templates/prod-windows-ilb.yaml) | VPC、Public / Private Subnet、NAT Gateway、踏み台、Windows Server 2台、Internal ALB、RDS、Security Group |

このテンプレートは公開用のPoCサンプルです。実務構成をそのまま再現したものではありません。

Windows Server にはUserDataでIISと簡易HTMLを配置し、Internal Load Balancer のヘルスチェックを確認できる最小構成にしています。

## パラメータファイル

パラメータファイルのサンプルは以下に配置しています。

| ファイル | 内容 |
| --- | --- |
| [parameters/prod.example.json](parameters/prod.example.json) | 本番相当環境向けのサンプルパラメータ |

実際にデプロイする場合は、サンプルファイルをコピーして使用します。

```bash
cp parameters/prod.example.json parameters/prod.json
```

`parameters/prod.json` には、自分の環境に合わせた値を設定します。実環境の値を含むため、GitHubにはコミットしない運用とします。

## 公開用に対象外にしたもの

- 実環境のリソース名
- 実CIDR、実IPアドレス
- EC2インスタンスID
- 顧客名、案件名、拠点名
- 業務PPTX原本
- 実運用の接続先、通知先、認証情報

## ディレクトリ構成

```text
.
├── README.md
├── docs/
│   ├── architecture.md
│   └── operations.md
├── diagrams/
│   └── README.md
├── examples/
│   └── naming-and-parameters.example.md
├── parameters/
│   └── prod.example.json
└── templates/
    └── prod-windows-ilb.yaml
```

## 設計ドキュメント

実務由来の構成を扱うため、CloudFormationテンプレートは公開用に抽象化したサンプルとして作成しています。

実装コードだけでなく、構成要素、設計意図、セキュリティ上の配慮、運用観点もあわせて記載します。

## 命名・パラメータ方針

公開用のリソース名、CIDR、接続先、環境名はすべてサンプル値を使用します。

実環境の情報を連想しやすい名称、ID、IPアドレスは記載しません。

## 前提条件

本リポジトリは公開用のPoCケーススタディです。実環境の完全な構成情報ではなく、そのまま実環境へ適用することを目的としていません。

CloudFormationサンプルには、EC2、NAT Gateway、Application Load Balancer、RDSなどの課金対象リソースが含まれます。検証する場合は、利用リージョン、インスタンスサイズ、削除手順を事前に確認してください。

## デプロイ・運用方法

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

- 踏み台サーバーへ管理者端末から接続できること
- 踏み台サーバー経由でPrivate Subnet内のWindows Serverへ接続できること
- Internal Load Balancer のDNS名で、Windows Server上のIISサンプルページへ通信できること
- Windows ServerからRDSエンドポイントへ接続できること

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

RDSは `DeletionPolicy: Snapshot` を指定しているため、削除時にスナップショットが作成されます。

## 設計上の補足

本番相当環境では、業務アプリケーションを稼働するWindows ServerをPrivate Subnetに配置し、外部から直接到達できない構成とします。

複数台のWindows Serverに対する通信を内部ロードバランサーに集約することで、利用側の接続先を固定しつつ、ヘルスチェック、冗長化、メンテナンス時の切り離しを行いやすくしています。

## 実務構成との差分

本リポジトリは公開用のケーススタディであり、実環境の完全な構成情報ではありません。

守秘義務とセキュリティを考慮し、構成要素と設計意図が伝わる範囲に抽象化しています。
