ありがとうございます。  
あなたが「rootユーザー」でログインしている場合、`sudo`は不要です。そのままコマンドを実行できます。

---

## 例：vi, vim, nano のインストール（rootユーザーの場合）

### Ubuntu / Debian系
```bash
apt update
apt install vim nano
```

### CentOS / RHEL / Fedora系
```bash
yum install vim nano
```
または（新しめのシステムなら）
```bash
dnf install vim nano
```

### Arch Linux系
```bash
pacman -S vim nano
```

---

## 使い方のポイント

- `sudo`は不要です。  
  例：`sudo apt install vim` → `apt install vim`
- 権限があるので、直接`apt`や`yum`などのパッケージ管理コマンドを実行できます。

---

ご自身のディストリビューションに応じて、上記のコマンドをお使いください。  
もし、どれを使えばよいか分からない場合は、`cat /etc/os-release`でOS情報を確認できますので、その結果を教えていただければ、最適なコマンドをご案内します。