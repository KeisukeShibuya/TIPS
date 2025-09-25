# 質問1

Spring Batchで Hello, World! までできるようになりました。ここから、実行時に引数を渡してDB(MySQL) に何らかの処理するまでの流れを教えてください。

---

Spring Batchで「Hello, World!」から一歩進み、コマンド引数経由で値を受け取り、MySQLへ処理するサンプルの流れについて、**チャンクモデル**と**タスクレットモデル**の両方を例示しながら、`Job`や`Step`の関係も含めて分かりやすく解説します。

## 1. Spring Batchの基本構成とジョブ起動

- **Job（ジョブ）**: 複数の`Step`から構成され、1つのバッチ処理の単位
- **Step（ステップ）**: 各工程。`Tasklet`または`Chunk`による実装
- **Job Parameters**: 実行時に渡される引数。DB入出力やフィルタ条件に利用できる

### 実行時引数の渡し方
READMEより、下記のようにパラメータを渡します。
```textmate
./gradlew bootRun --args='--job.name=chunkDbJob inputValue=123'
```

ジョブパラメータはStepやTasklet、ItemReader/Writerで利用可能です。

---

## 2. DBテーブル例
MySQLに以下の簡単なテーブルを用意した例

```sql
CREATE TABLE messages (
  id INT AUTO_INCREMENT PRIMARY KEY,
  text VARCHAR(255),
  processed_at DATETIME
);
```


---

## 3. チャンクモデル（Chunk Oriented Processing）

### 概要

- データを一定単位（例：5件ずつなど）で読み取り、処理、書き込みを行う
- ItemReader → ItemProcessor（省略可）→ ItemWriterの3層構造

### 処理イメージ

1. ItemReaderでDBやファイル等から「読む」
2. 必要に応じてItemProcessorで「加工」
3. ItemWriterで「書き込む」
4. トランザクション・コミットはチャンク単位

---

### 実装イメージ

1. **JobParameters受け取り**  
例：`jobParameters.getString("inputValue")` で値を取得

2. **ItemReader/Writerの実装**  
MySQLの`messages`へパラメータのテキストをinsertする例

```java
@Bean
public Step chunkDbStep(JobRepository jobRepository, PlatformTransactionManager tm, DataSource dataSource) {
    return new StepBuilder("chunkDbStep", jobRepository)
            .<String, String>chunk(10, tm)
            .reader(new ItemReader<String>() {
                @Override
                public String read() throws Exception {
                    // JobParametersから値取得
                    String text = (String) StepContext.getJobParameters().get("inputValue");
                    // 1回だけ値を返す（その後nullを返して終了）
                    return text != null ? text : null;
                }
            })
            .writer(new ItemWriter<String>() {
                @Override
                public void write(List<? extends String> items) throws Exception {
                    for (String msg : items) {
                        // JdbcTemplateなどでDB登録
                        jdbcTemplate.update("INSERT INTO messages (text, processed_at) VALUES (?, NOW())", msg);
                    }
                }
            })
            .build();
}
```


3. **Job構成**
```java
@Bean
public Job chunkDbJob(JobRepository jobRepository, Step chunkDbStep) {
    return new JobBuilder("chunkDbJob", jobRepository)
            .start(chunkDbStep)
            .build();
}
```


---

## 4. タスクレットモデル（Tasklet Model）

### 概要
- 1つのStepの中で任意のJavaロジックをまとめて実装する（繰返ししない単純な処理向け）

### 実装イメージ

1. **JobParameters受け取り**
```java
@Bean
public Step taskletDbStep(JobRepository jobRepository, PlatformTransactionManager tm, JdbcTemplate jdbcTemplate) {
    return new StepBuilder("taskletDbStep", jobRepository)
            .tasklet((contribution, chunkContext) -> {
                // パラメータ取得
                String input = (String) chunkContext.getStepContext()
                                   .getJobParameters().get("inputValue");
                // DB処理例
                jdbcTemplate.update("INSERT INTO messages (text, processed_at) VALUES (?, NOW())", input);
                return RepeatStatus.FINISHED;
            }, tm)
            .build();
}
```

2. **Job構成**
```java
@Bean
public Job taskletDbJob(JobRepository jobRepository, Step taskletDbStep) {
    return new JobBuilder("taskletDbJob", jobRepository)
            .start(taskletDbStep)
            .build();
}
```


---

## 5. `Job`や`Step`の概念まとめ

- **Job** ... バッチ全体の流れを表す単位（Stepを1つ以上持つ）
- **Step** ... 各工程（chunk型 or tasklet型で作成）
    - chunk: データを分割して逐次処理したい時に最適
    - tasklet: シンプルな一括処理や1回限りの処理に最適

---

## 6. テーブル例・テストコマンド

- テスト時にはMySQLの`messages`テーブルに適当な値が保存されます
- 起動例：  
  `./gradlew bootRun --args='--job.name=taskletDbJob inputValue=バッチ処理テスト'`

---

## 7. SQSポーリングによる非同期バッチ実行について

この部分だけは大きく話が広がるため、**「SQS連携による外部キューからバッチ起動」**については
別途ご質問いただければ、**SpringBatch + SQS Polling + 非同期Job起動**の流れやアーキテクチャ提案・サンプルなど詳細に解説できます。

---

### ご要望があれば

- 実際のサンプルコードやDBスクリプト
- SQS連携実装の続き
- より高度なバッチ運用例

も追加でご案内可能です。ご質問ください！
