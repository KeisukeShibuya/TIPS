ご要望ありがとうございます！  
各種「確認コマンド」に**出力例とその解説**を加えて、さらに分かりやすくまとめ直しました。

---

# Node.js バージョン管理Tips（nvm活用編・出力例つき）

---

## 1. 現在のNode.jsバージョンの確認

```shell script
node -v
```

**出力例:**
```
v24.4.1
```

**解説:**  
今シェルで利用中のNode.jsバージョンが表示されます。

---

## 2. nvm（Node Version Manager）の役割

- 複数バージョンのNode.jsを**簡単に切替・管理**できるツール。
- プロジェクトや用途ごとに異なるNode.jsバージョンを使いたい場合に便利です。

---

## 3. nvmのインストール

**Mac/Linux:**

```shell script
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

インストール後はターミナルを再起動、または
```shell script
source ~/.nvm/nvm.sh
```

で反映。

**Windows:**
[nvm-windows](https://github.com/coreybutler/nvm-windows/releases)（公式GitHub）からインストーラーを利用。

---

## 4. nvmコマンドが使えるかの確認

```shell script
nvm --version
```

**出力例:**
```
0.39.7
```

**解説:**  
nvmのバージョンが数字で表示されれば、インストールは成功です。

---

## 5. nvmでインストール済みNode.jsバージョン一覧を確認

```shell script
nvm ls
```

**出力例:**
```
v16.20.2
->     v18.17.1
default -> v18.17.1
node -> stable (-> v18.17.1)
system
```

**解説:**  
- バージョンリストが表示されます。  
- `->`が付いているバージョンが**現在有効**。  
- `system`はnvm外で直接インストールされたNode.jsを指します。

---

## 6. nvmのエイリアス（デフォルトバージョンなど）の確認

```shell script
nvm alias
```

**出力例:**
```
default -> v18.17.1
node -> stable (-> v18.17.1)
stable -> 18.17.1 (-> v18.17.1)
iojs -> N/A (default)
```

**解説:**  
- `default`はnvmで「デフォルト」で使う指定バージョンです。

---

## 7. 今現在有効なNode.jsバージョンを確認

```shell script
nvm current
```

**出力例:**
```
v18.17.1
```

**解説:**  
現在のシェルでnvmが指し示しているNode.jsバージョンが表示されます。

---

## 8. Node.jsバージョンのインストール

```shell script
nvm install 16
```

**解説:**  
指定したNode.jsバージョンがnvm配下に新規インストールされます。

---

## 9. バージョンを切り替えて使う

```shell script
nvm use 16
```

**出力例:**
```
Now using node v16.20.2 (npm v8.19.4)
```

**解説:**  
シェルで使用されるNode.jsバージョンが切り替わります。

---

## 10. 「system」とは？

- nvmに管理されていない、もともとOSにインストールされているNode.jsを指します。
- nvmで切り替えると自動で無視されます。

---

## 11. 既存（system）Node.jsをnvm管理下に移せる？

- 既存のsystemバージョン自体を**nvm配下へ移すことはできません**。
- nvmで目的バージョンを新規インストールして使い分ける運用が推奨されます。

---

## 12. 推奨運用

- nvmで必要なNode.jsバージョンをその都度インストール・切り替え。
- システム全体でNode.js不要ならsystemインストール分は削除してOKです。

---

### 参考リンク

- [nvm公式GitHub](https://github.com/nvm-sh/nvm)
- [nvm-windows公式](https://github.com/coreybutler/nvm-windows)

---

さらに詳しく知りたいコマンドや使い方があれば、いつでもお知らせください！

# 質問1

システムで管理するNode.jsとnvmで管理するNode.jsが混在している場合、どちらが優先して使用されるのでしょうか？

---

ご質問ありがとうございます！

## 回答

**基本的には「nvmで切り替えてある場合はnvm管理下のNode.jsが優先されます」。**  
`nvm use バージョン` を実行中のシェルでは、**nvmで指定したバージョン**が優先して使われます。

---

## 詳しい解説

### 1. nvmの仕組み
- `nvm use 16` などでバージョンを切り替えると、そのシェル（ターミナル）環境変数（`PATH`）が一時的に書き換えられ、**nvmにインストールされたNode.jsのパス**が先頭に追加されます。
- そのため、コマンドで`node`や`npm`を実行した場合、**nvm配下のバージョンが呼び出される**仕組みです。

---

### 2. 優先順位まとめ

| 状況 | 優先されるNode.js |
|:--|:--|
| nvmでバージョン切替しているシェル | nvm管理下のNode.js（指定バージョン） |
| nvmを使っていない/`system`指定時 | システム（OS標準）のNode.js |

---

### 3. 例：PATHを見てみる

**`nvm use 16`後で`which node`や`echo $PATH`を見てみると、以下のようにnvm配下のディレクトリが優先されています。**

```shell script
which node
# 例：/Users/yourname/.nvm/versions/node/v16.20.2/bin/node
```


---

### 4. シェルを閉じると？

- シェル（ターミナル）を閉じて、新しく開くとデフォルトは「system」のNode.jsになる場合があります（環境による）。
- 常にnvmの特定バージョンを使いたい場合は`.bashrc`や`.zshrc`に`nvm use デフォルト番号`を書く運用もあります。

---

## まとめ

- **nvmで選択している間はnvm配下のNode.jsが優先して呼ばれる**  
- 何も指定がなければ「system」Node.jsが使われる

---

ご不明な点やさらに知りたいことがあれば、お気軽にご質問ください！

# 質問2

例えばnvmのNode.jsではなくシステムのNode.jsを使いたい場合はどのように行うのですか？

---

ご質問ありがとうございます。

## 回答  
**nvmで「system」を指定することで、システム（nvm管理外）のNode.jsへ切り替え可能です。**

---

### 手順

#### 1. nvmで「system」を明示的に指定する

```shell script
nvm use system
```


- これで、そのシェルセッションでは**nvm配下のNode.jsではなく、システム（元からインストールされている方）が優先的に使われます**。

---

#### 2. 実際のNode.jsバージョンを確認する

```shell script
node -v
```

出力例：
```
v24.4.1
```

- この出力が「nvm ls」などでsystemと表示されているバージョンであれば、system側が使われています。

---

#### 3. どのNode.jsが使われているかを確認

```shell script
which node
```

出力例（systemの場合）：
```
/usr/local/bin/node
```

- もし「.nvm/」ディレクトリではなく、「/usr/local/bin」や「/usr/bin」などのパスであれば**system側**です。

---

### 注意

- **「nvm use バージョン名」**で再びnvm管理下のバージョンに戻せます。
- シェルを再起動した場合、デフォルトは環境や設定によって異なります。常にsystemにしたい場合は、`.bashrc`などの設定に `nvm use system` を追記してください。

---

ご参考になれば幸いです。他にも質問があればご遠慮なくどうぞ！
