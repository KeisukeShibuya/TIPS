# Spring Batch

## ブランクプロジェクトの開始

TERASOLUNA Batch ガイドラインの[3.1.5. プロジェクトの作成](https://terasoluna-batch.github.io/guideline/current/ja/single_index.html#Ch03_CreateProject_HowToCreate)を参考にまとめる。

### 1. ブランクプロジェクト作成

```shell
# demo-batchディレクトリに移動する
$ cd Desktop/dev/demo-batch

# TERASOLUNA BatchのMavenアーキタイプを使ってブランクプロジェクトを生成する
$ mvn archetype:generate \
  -DarchetypeGroupId=org.terasoluna.batch \
  -DarchetypeArtifactId=terasoluna-batch-archetype \
  -DarchetypeVersion=5.7.0.RELEASE
```

### 2. パラメータ設定

前述のコマンドを実行途中、以下のパラメータの入力を対話式で求められるため、任意の値を設定する。

| 項目名     | 設定例            |
| ------------ | ------------------- |
| groupId    | com.example.batch |
| artifactId | batch             |
| version    | 1.0.0-SNAPSHOT    |
| package    | com.example.batch |

### 3. 動作確認

ブランクプロジェクトに同梱のサンプルジョブ(`job01`)を実行して確認できる。

```shell
$ cd batch
$ mvn clean dependency:copy-dependencies -DoutputDirectory=lib package
$ java -cp 'lib/*:target/*' \
org.springframework.batch.core.launch.support.CommandLineJobRunner \
com.example.batch.jobs.Job01Config job01
```

以下の結果が確認できれば、プロジェクトの作成は完了である。

* `batch¥target`配下に `output.csv`が作成されていること。
* 標準出力に`following status: [COMPLETED]` が表示されていること。

## 参考

* [TERASOLUNA Batch Framework for Java (5.x) Development Guideline](https://terasoluna-batch.github.io/guideline/current/ja/single_index.html)
