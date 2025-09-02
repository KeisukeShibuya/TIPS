# ローカルに作成したプロジェクトをGitHubリポジトリにpushする

## 1. ターミナルでプロジェクトのルートフォルダに移動する

```shell
# GitHubにpushするルートディレクトリを指定
# ここではbatch用のディレクトリ
cd Desktop/dev/demo-batch/batch
```
## 2. Git リポジトリとして初期化する

```shell
git init
```

## 3. 変更をadd/commitし、リモートリポジトリを設定する

```shell
git add .
git commit -m "Initial commit"
# ここでは、batch用のテストリポジトリを設定
git remote add origin https://github.com/KS-MY-STUDY/batch.git
```

## 4. `main` ブランチへpushする

```shell
git branch -M main
git push -u origin main
```

# GitHub Organization

GitHub Organization（組織）を利用すると、複数人でのリポジトリ管理や権限管理、チーム運用などが強力にサポートされる。
以下は基本的な利用方法の概要である。

## 1. GitHub Organizationの特徴・メリット
- 複数のメンバーでリポジトリを共同管理できる
- チームごとに権限（管理者、書き込み、読み取りなど）を細かく設定可能
- 請求やGitHub Appsの管理が一元化される
- 組織単位でのセキュリティポリシーや監査ログ機能も利用可能

## 2. Organizationの作成手順
1. GitHubにログイン
2. 画面右上のアイコンメニューから「New organization」を選択
3. プランを選択（無料プランも可）
4. 組織名・メールアドレスを入力
5. 必要に応じてメンバーを招待

## 3. 活用のポイント
- チーム（Team）を作成して、プロジェクト・メンバーごとにアクセス管理
- リポジトリごと、またはOrganization全体での設定管理
- GitHub ActionsなどCI/CDの一括管理
- セキュリティ設定や監査ログ（有料プランでより強化）

## 4. 注意点
- 組織オーナーは全権限を持つため、慎重に決める
- プライベートリポジトリ数や一部機能はプランによって制限あり
