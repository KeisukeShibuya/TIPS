Gradleの使い方・配置方法についてご説明いたします。

---

## Gradleのインストールと利用方法

### 1. **マシン単位でインストールする方法（システムGradle）**

- Gradle本体を直接インストールし、`gradle` コマンドをどのディレクトリからでも実行できるようにする方法です。
- 例: Homebrew（Mac）、Chocolatey（Windows）などでインストール
- この場合 `gradle --version` でバージョン確認可能

**メリット:**  
- どこからでも `gradle` コマンドが使える
- プロジェクトに依存しない

---

### 2. **プロジェクトごとのGradle Wrapper（推奨される方法）**

- プロジェクトごとに「Gradle Wrapper」というものを使います。
- Spring Initializrからダウンロードしたプロジェクトには通常 `gradlew`（Linux/Mac用）および `gradlew.bat`（Windows用）という**実行ファイル**と、`gradle/wrapper/` フォルダが含まれています。

**使い方:**  
- ターミナルでプロジェクトのルート（`build.gradle` や `gradlew` がある場所）に移動
- `./gradlew build` （Mac/Linux）または `gradlew build`（Windows）と実行

**メリット:**  
- プロジェクトごとに**推奨/指定バージョンのGradle**が自動的にダウンロード・利用される
- 他の環境との差異の心配がない
- **システム全体へインストールは不要**

---

## 実践例

```textmate
cd path/to/your-project
./gradlew build   # Mac/Linux
# または
gradlew build     # Windows
```


> 初回実行時は、指定されたバージョンのGradleを自動的にダウンロードし、以降はそれを使います。

---

## まとめ

- **結論:**  
  Gradleは「マシン全体」「プロジェクトごと」両方可。  
  **しかし、Spring BootやSpring Initializrの標準では「プロジェクトごと（Wrapper）」の利用が推奨されます。**

- **システムにインストールされていなくても大丈夫**です。  
  → プロジェクトルートで `./gradlew` または `gradlew` を使いましょう。

---

疑問点が解消しない場合や、実行してエラーが出た場合は内容（エラーメッセージ等）を教えていただければ解決までサポートいたします！