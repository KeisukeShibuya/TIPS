# IaCを試してみたく、Terraformを使えるようにしたいです。手順を教えてください。

TerraformをmacOS Sequoia 15.6.1で利用するための手順を詳しくご案内します。

---

## 【1】Terraformのインストール

### 代表的な方法：Homebrewの利用  
HomebrewはmacOSで人気のパッケージ管理ツールです。  
まだインストールしていない場合は、[公式サイト](https://brew.sh/index_ja)を参考にインストールしてください。

```shell script
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```


> ※ `sudo` は不要です。  
> ※ コマンドでエラーが出た場合は`brew update`を実行してください。

---

## 【2】インストール確認

バージョンが表示されることを確認しましょう。

```shell script
terraform -v
```


例：
```
Terraform v1.8.2
on darwin_arm64
```


---

## 【3】Terraformの初期設定

1. 作業用ディレクトリを作成  
```shell script
mkdir my-terraform-project
cd my-terraform-project
```


2. 初期化用に `.tf` ファイルを作る（例: `main.tf`）
```shell script
touch main.tf
```


    内容例（AWSを使う場合）:
```textmate
terraform {
      required_providers {
        aws = {
          source  = "hashicorp/aws"
          version = "~> 5.0"
        }
      }

      required_version = ">= 1.0.0"
    }

    provider "aws" {
      region = "ap-northeast-1"
    }
```


3. 初期化  
```shell script
terraform init
```

    これで必要なプラグインがダウンロードされます。

---

## 【4】簡単な操作例

1. **リソース定義追加**（例：S3バケットを作る）
```textmate
resource "aws_s3_bucket" "sample" {
      bucket = "my-terraform-sample-bucket-20240831"
      acl    = "private"
    }
```

2. **反映内容の確認**
```shell script
terraform plan
```

3. **実行（反映）**
```shell script
terraform apply
```

    プロンプトが出たら「yes」と入力

---

## 【5】アンインストールしたい場合

```shell script
brew uninstall hashicorp/tap/terraform
```


---

## 【補足】

- AWSやGCPなど利用したいクラウドのCLI（`awscli`や`gcloud`など）も個別に準備してください。
- AWSの場合、`aws configure` で認証情報を設定できます。
- **Terraform公式ドキュメント：https://developer.hashicorp.com/terraform/tutorials**

---

ご不明点や「このクラウドでやってみたい」などのご希望があれば、お気軽にお知らせください！