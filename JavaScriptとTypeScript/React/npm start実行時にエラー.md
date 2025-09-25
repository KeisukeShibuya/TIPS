# 質問
`npm start` を実行するとエラーが発生しました。解消方法を教えてください。

```
Starting the development server...
Error: error:0308010C:digital envelope routines::unsupported
    at new Hash (node:internal/crypto/hash:102:19)
    at Object.createHash (node:crypto:137:10)
    at module.exports (/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/webpack/lib/util/createHash.js:90:53)
    at NormalModule._initBuildHash (/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/webpack/lib/NormalModule.js:401:16)
    at handleParseError (/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/webpack/lib/NormalModule.js:449:10)
    at /Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/webpack/lib/NormalModule.js:481:5
    at /Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/webpack/lib/NormalModule.js:342:12
    at /Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/loader-runner/lib/LoaderRunner.js:373:3
    at iterateNormalLoaders (/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/loader-runner/lib/LoaderRunner.js:214:10)
    at iterateNormalLoaders (/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/loader-runner/lib/LoaderRunner.js:221:10)
    at /Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/loader-runner/lib/LoaderRunner.js:236:3
    at runSyncOrAsync (/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/loader-runner/lib/LoaderRunner.js:130:11)
    at iterateNormalLoaders (/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/loader-runner/lib/LoaderRunner.js:232:2)
    at Array.<anonymous> (/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/loader-runner/lib/LoaderRunner.js:205:4)
    at Storage.finished (/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/enhanced-resolve/lib/CachedInputFileSystem.js:55:16)
    at /Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/enhanced-resolve/lib/CachedInputFileSystem.js:91:9
(node:31901) [DEP0060] DeprecationWarning: The `util._extend` API is deprecated. Please use Object.assign() instead.
/Users/shibuyakeisuke/Desktop/dev/learning-react-2e-ja/chapter-06/6.1/02/node_modules/react-scripts/scripts/start.js:19
  throw err;
  ^
Error: error:0308010C:digital envelope routines::unsupported
```

# 回答

このエラーは、**Node.js v17 以降**と古い `webpack`（または `react-scripts`）の組み合わせでよく発生します。  
主な原因は、OpenSSLに関連したNode.jsの仕様変更によるものです。

---

# 【解決方法】

## 1. 環境変数を設定する（推奨・簡単）

以下コマンドを使い、Node.jsの互換モードをONにできます。

### macOS/Linux の場合:

```shell script
export NODE_OPTIONS=--openssl-legacy-provider
npm start
```


（毎回入力が面倒な場合は、`package.json` の `scripts` を以下のように修正してください）

```json
"scripts": {
  "start": "NODE_OPTIONS=--openssl-legacy-provider react-scripts start"
}
```


### Windows の場合（コマンドプロンプト）:

```textmate
set NODE_OPTIONS=--openssl-legacy-provider
npm start
```

Windows対応の書き方を `package.json` に入れる場合は：

```json
"start": "set NODE_OPTIONS=--openssl-legacy-provider && react-scripts start"
```


---

## 2. Node.js のバージョンを下げる

もし Node.js 17以上を使っている場合、Node.js 16系 (推奨) を使うとこのエラーは基本的に出ません。

バージョン確認:

```shell script
node -v
```


Node.js のバージョンを切り替えるには  
[nvm (Node Version Manager)](https://github.com/nvm-sh/nvm)  
などの利用をおすすめします。

例:

```shell script
nvm install 16
nvm use 16
npm install
npm start
```


---

## 3. react-scripts / webpack のアップデート

`react-scripts` や `webpack` を新しいものにすることで根本的に解決する場合もありますが、  
既存教材などでバージョン固定されている場合は、無理に更新しない方がトラブルが少ないです。

---

# まとめ

最も簡単な方法は

```shell script
export NODE_OPTIONS=--openssl-legacy-provider
npm start
```


を実行することです。

もし継続的に使う場合は `package.json` の `scripts` を修正してください。

---

何か補足情報や別のエラーが出る場合は、その内容を教えてください。