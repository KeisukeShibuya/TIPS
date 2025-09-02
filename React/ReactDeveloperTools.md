# React Developer Tools

表示中のサイトがReactを利用しているか検出可能になり、またそれらのサイトのReactのコンポーネントツリーの中身を見ることができる。
ChromeおよびFirefoxではブラウザの拡張機能として提供されている。
ここでは、Safariでの利用を前提として利用方法を記述する。

## 1. インストール

`react-devtools`のnpmパッケージをインストールする。

```bash
# Yarn
yarn global add react-devtools

# Npm
npm install -g react-devtools
```

## 2. ツールを開く

ターミナルから開発者ツールを開く。

```bash
react-devtools
```

## 3. ウェブサイトとツールを接続

ウェブサイトの`<head>`タグ内の先頭に以下の`<script>`タグを追加して、ウェブサイトを接続する。

```html
<html>
    <head>
        <script src="http://localhost:8097"></script>
        <!-- omitted -->
    </head>
    <!-- omitted -->
</html>
```

その後、ブラウザをリロードする。

## 参考

* [React Developer Tools](https://ja.react.dev/learn/react-developer-tools)

