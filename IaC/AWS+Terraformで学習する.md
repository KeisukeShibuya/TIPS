# Iac学習

## Terraform + AWS でIaCを学ぶための実践ロードマップ

---

### 0. 準備

- **AWS無料枠アカウント**作成
    - [AWS公式で作成](https://aws.amazon.com/jp/free/)
    - 多要素認証 (MFA)、**課金アラート(Budgets)** 設定
- **IAM管理**
    - 管理者ユーザー作成
    - アクセスキーを**Terraform実行プロファイル専用**に
- **開発環境整備**
    - Terraform
    - AWS CLI
    - [tfenv](https://github.com/tfutils/tfenv) や [asdf](https://asdf-vm.com/)
    - direnv（任意：環境変数自動切替）

---

### 1. 最小のIaC演習（所要2時間）

- **目標:**
    - VPC
    - パブリックサブネット
    - EC2(無料枠: t2.micro/t3.micro)
- **学ぶこと:**
    - provider, resource, variables, outputs
    - terraform init/plan/apply/destroy

- **手順例:**
    1. main.tf作成 → VPC, subnet, EC2作成
    2. terraform applyでリソース作成
    3. terraform destroyで削除

---

### 2. ステート管理の高度化（所要1時間）

- **目標:**
    - S3バケット& DynamoDBテーブルをTerraformで作成
    - Terraformのstate管理を**remote backend化**
- **学ぶこと:**
    - state分離
    - 並行実行の防止
    - stateファイルの暗号化/復旧

---

### 3. モジュール化と複数環境分離（半日）

- **目標:**
    - modulesディレクトリにnetwork, computeモジュール作成
    - environments/dev, environments/stgなど複数環境のコード分離
- **設計ポイント:**
    - モジュール利用方法・変数渡し
    - 出力値(outputs)を活用（例：EC2 public IPの表示）

---

### 4. CI自動化・可視化（半日）

- **目標:**
    - GitHub Actions等でCI
- **CIでやること:**
    - terraform fmt/validate/planをPR時に行う
    - mainマージでapply（必要に応じて手動承認）
    - セキュリティチェック（tfsec/checkov）
    - コスト見積もり表示（Infracost：任意）

---

### 5. 次の課題（さらなる拡張）

- **静的Webサイト:**
    - S3静的サイト+CloudFront（HTTPS/OAC）
- **データベース:**
    - RDS (PostgreSQL/無料枠) & セキュリティグループ
- **コンテナ:**
    - ECS FargateでNginx等を起動

---

### 標準的なリポジトリ構成例

```
modules/
  network/
    main.tf, variables.tf, outputs.tf
  compute/
    main.tf, variables.tf, outputs.tf
environments/
  dev/
    main.tf, providers.tf, backend.tf, variables.tf, dev.tfvars, outputs.tf
.github/
  workflows/
    terraform.yml
versions.tf
README.md
```


---

### 注意とベストプラクティス

- **全リソースにタグ付与**（運用/管理の効率化）
- **リージョンは固定**（例: ap-northeast-1）
- 変更は **小さく分割しPR** で実施
- **秘密情報はバージョン管理禁止**（環境変数 or AWS Secrets Manager経由で利用）

---

### さらに進める場合

- terratest (Go) でインフラテスト
- ポリシー強制（Sentinel/OPA）
- マルチアカウント運用（Organizations+Terraform）

---

**具体的なサンプルコードやファイル内容が必要な場合は、お題を指定してください！**

---
