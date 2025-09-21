# ElasticMQの導入とSpring Batch連携

はい、**ElasticMQを用いた開発環境でのSQSエミュレーション**はとても手軽にできます。  
ElasticMQは「自己完結型」「Dockerで一発起動可能」「AWS SDK for Javaとも互換」なので、Spring Batch＋SQSの開発・テストに最適です。

---

## 1. DockerでElasticMQを起動する

ほとんどのケースで一番おすすめなのはDockerを使う方法です。

### 1-1. コマンド一発

```shell
docker run -d -p 9324:9324 --name elasticmq softwaremill/elasticmq-native
```


- `-p 9324:9324` でローカルマシンの9324番ポートにElasticMQコンテナをバインド
- `--name elasticmq` でコンテナに名前をつけます（好みで可）

起動後は http://localhost:9324 でElasticMQのエンドポイントになります。

---

## 2. AWS CLIでローカルのElasticMQにキューを作成

### 2-1. AWS CLIのプロファイルセット

- AWS CLIは"ダミー"のcredentialsでOKです（例：`dummy_profile`として作成）

```shell
aws --endpoint-url http://localhost:9324 sqs create-queue --queue-name test-queue
```


- `--endpoint-url` でAWSではなくローカルElasticMQへリクエストを送ります
- 作成したキューのURL例: `http://localhost:9324/queue/test-queue`

---

## 3. SpringアプリからElasticMQを参照する設定例

`application-dev.yml` などで

```yaml
aws:
  sqs:
    queue-url: http://localhost:9324/queue/test-queue
```


Spring BootのSQSクライアント（SDK v2）設定例：

```java
@Bean
public SqsClient sqsClient() {
    return SqsClient.builder()
        .endpointOverride(URI.create("http://localhost:9324"))    // ここをElasticMQに設定
        .region(Region.AP_NORTHEAST_1) // 適当でOK
        .credentialsProvider(StaticCredentialsProvider.create(AwsBasicCredentials.create("x", "x")))
        .build();
}
```

- 認証やRegionはダミーでOK（ElasticMQは認証無しで動作します）

---

## 4. ElasticMQ管理Web UI（参考）

直付けでは管理UIはありませんが、  
SQS Web管理用途に[elasticmq-rest-sqs](https://github.com/softwaremill/elasticmq)＋[SQS Web UI（localstack/sqs Web UIなど）](https://github.com/alfikpl/simple-sqs-browser)を組み合わせて使うこともできます。

---

## 5. 動作検証フローまとめ

1. **DockerでElasticMQ起動**
2. **AWS CLIでキュー作成**
3. **Spring Batch (SQSクライアント) のエンドポイントを`localhost:9324`に。パラメータやキュー名も一致させる**
4. **試しにCLIやJavaからsend/receive-messageで疎通テスト**

---

## 6. AWS CLIでのメッセージ送信・受信例

```shell
# メッセージ送信
aws --endpoint-url http://localhost:9324 sqs send-message --queue-url http://localhost:9324/queue/test-queue --message-body '{"jobName":"sqsJob","params":{"inputValue":"ElasticMQテスト"}}'
# メッセージ受信
aws --endpoint-url http://localhost:9324 sqs receive-message --queue-url http://localhost:9324/queue/test-queue
```


---

## 補足

- **ElasticMQはバッチ開発・テスト用途には十分安定**。本番はAWS SQSに切り替えればOK。
- テストしやすいので**SQSキュー連携の自動テストやCIとも相性良い**です！

---

ご不明点があれば、ElasticMQ導入やSpring連携、テスト支援についても続けてご案内できます！

# AWS CLIのインストール

はい、**AWS CLI（AWS Command Line Interface）をMacBook Airで利用するには、事前にAWS CLIのインストール（ダウンロード）が必要**です。

---

### AWS CLIインストール手順（Macの場合）

#### 1. **公式パッケージインストーラを使う方法（推奨）**

1. [AWS CLI公式インストーラ (最新版)](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/getting-started-install.html) にアクセス
2. Mac（x86_64 または arm64、Apple Siliconなど）用のインストーラをダウンロード
3. ターミナルで以下のコマンドを実行

```shell
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
```


#### 2. **Homebrewを使う方法**

Homebrewがインストールされている場合は、以下のコマンドでインストールできます。

```shell
brew install awscli
```


---

### インストール後の確認

インストールできたか確認するには：

```shell
aws --version
```


が実行できればOKです。

---

### まとめ

- **AWS CLIは事前にダウンロード・インストールが必要**
- 公式インストーラ または Homebrew で簡単に導入可能
- インストール後は`aws --version`で確認できます

ご不明な点があれば、続けてご質問ください！