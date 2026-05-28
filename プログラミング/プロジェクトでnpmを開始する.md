# プロジェクトでnpmを開始する

```sh
# プロジェクトでnpmを開始する
# これにより、package.jsonが作成される
# 対話形式で作りたい場合はオプションなし
npm init

# すぐ作りたい場合はこっち
npm init -y

```

## BIOMEを導入する

プロジェクト単位で実行すると、 `package.json` および `package-lock.json` (または `pnpm-lock.yaml` )が更新される。

```sh
# package.jsonのdevDependenciesに、BIOMEが自動追加される
npm install -D @biomejs/biome

# もしくは、pnpmコマンドでもOK
pnpm add -D @biomejs/biome

# グローバルに導入するなら -g オプションを利用する
# ただし、できればプロジェクトでの導入を推奨
npm install -g @biomejs/biome
```

## VS Code で使う場合

ローカルインストール後に拡張が `biome` を見つけられない場合、 `biome.cliPath` を設定して直接指定も可能。

### 例

相対パスの場合...

```json
{
    "biome.cliPath": "./node_modules/.bin/biome"
}
```

絶対パスの場合...

```json
{
    "biome.cliPath": "/Users/yourname/Desktop/dev/javascript/node_modules/.bin/biome"
}
```
