# Dockerについて

レジストリ（代表：[Docker Hub](https://hub.docker.com)）からイメージを取得する。

## 参考

- [【入門】Docker Hubとは？概要と仕組み、基本的な使い方を解説](https://www.kagoya.jp/howto/cloud/container/dockerhub/)

# MySQL

[Docker Hub](https://hub.docker.com)からMySQLイメージの取得〜削除

```shell
# Docker imageを取得
docker pull mysql

# 起動（初回）
## DB名：testdb
## rootユーザパスワード：root
## ポート番号：3306
## MySQLDBのイメージは最新のものを利用する
docker run --name testdb -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=testdb -P --expose=3306 -p 3306:3306 -d mysql:latest

# 停止
## testdbという名称のコンテナを停止
docker stop testdb

# 起動
## testdbという名称のコンテナを起動
docker start testdb

# 削除
## testdbという名称のコンテナを削除
docker rm testdb
```

# Linux(Ubuntu)

GitHub Copilot回答を引用する。

1. ターミナルを開く  
2. 利用したいLinuxディストリビューション（例：Ubuntu）のイメージをダウンロードする  
3. ダウンロードしたイメージからコンテナを起動する

以下に例としてUbuntuコンテナの起動コマンドを示す。

```bash
docker pull ubuntu
docker run -it ubuntu /bin/bash
```

これで、ターミナル上でUbuntuのシェルが起動し、Linux環境を操作することができる。

Ubuntuコンテナ内からMacOSのターミナルに戻る場合、コンテナのシェル上で`exit`コマンド（もしくは`CTRL + D`）を実行する。これによりコンテナから抜け、MacOSのターミナル環境に戻る。

```bash
exit
```

Ubuntuコンテナがまだ動作しているなら、同じ `run` コマンドを実行する代わりに、`exec` コマンドで既存のコンテナに接続することが可能。  
例えば、コンテナ名が `my-ubuntu` の場合は以下のように実行する。

```bash
docker exec -it my-ubuntu /bin/bash
```

もしコンテナが終了している場合は、再度 `run` コマンドを実行する。

## コマンドがインストールされていない場合の対処法

例：`hexdump`

```bash
apt-get update
apt-get install bsdmainutils
```

## VNC経由で接続したい場合

DockerのUbuntu環境でVNC経由の接続は可能だが、以下の手順が必要。

1. コンテナ内にデスクトップ環境（例：XFCE）とVNCサーバ（例：tightvncserver）をインストールする  
2. VNCの設定をして、VNCパスワードやセッションの表示解像度等を設定する  
3. Dockerの起動コマンドでVNC用のポート（通常は5901など）をホストにバインドする

例:

```shell
# Dockerfileの例
FROM ubuntu:latest

RUN apt-get update && apt-get install -y \
    xfce4 \
    tightvncserver

# VNC用の設定スクリプト等をコピー・実行する（任意）
# コンテナ起動時にvncserverを実行する
CMD ["tightvncserver", ":1"]
```

ホスト側でコンテナの作成時にポートをバインドする。

```shell
# ubuntuイメージをビルド
# イメージ名は任意：ここではmy-ubuntu-vnc .
docker build -t my-ubuntu-vnc .
# ubuntuイメージを起動
docker run -d -p 5901:5901 my-ubuntu-vnc
```

この状態で、ホストのVNCクライアントで `localhost:5901` に接続することで、Docker内のUbuntuのデスクトップにVNC経由でアクセスできる。

## 参考

- [MacでLinux環境をサクッとつくる](https://qiita.com/c00lkid/items/ebc4e768bff92214f8f9)
- [【MacでLinuxを使う】Dockerを使って環境を構築する方法](https://www.kyonakablog.com/linux/how-to-build-linux-environment-using-docker/)

# ElasticMQ

```shell
docker run -d -p 9324:9324 --name elasticmq softwaremill/elasticmq-native
```
