---
title: "Ubuntu24.04(WSL2) + Zsh + Neovim + Peco で開発環境を構築する"
emoji: "💻"
type: "tech"
topics: ["WSL2", "Ubuntu", "Zsh", "Neovim", "Peco"]
published: false
---

## はじめに

本記事では、WSL2上の`Ubuntu 24.04`をベースとして、`Zsh`、`Neovim`、`Peco`などの強力なツールを組み合わせた開発環境の構築手順を紹介します。

## Ubuntu 24.04 のインストール

### 1. WSL2の有効化

まずは、WSL2を有効化し、`Ubuntu 24.04`をインストールします。

1. Windowsキー + X を押し、表示されるメニューから「Windows PowerShell (管理者)」を選択して、管理者権限でPowerShellを起動します。

2. 次のコマンドを実行して、WSL2をインストールします。

```powershell
wsl --install
```

3. インストールが完了したら、コンピューターを再起動します。

4. 再起動後、以下のコマンドを実行して、WSLのデフォルトバージョンを2に設定します。

```powershell
wsl --set-default-version 2
```

5. WSL2でのDNS解決の問題を回避するために、`C:\Users\<ユーザー名>\`ディレクトリに`.wslconfig`ファイルを作成し、以下の内容を記述します。

```text
[wsl2]
dnsTunneling=true
```

### 2. Ubuntu 24.04 のインストール

1. [Microsoft Store](https://apps.microsoft.com/detail/9nz3klhxdjp5?hl=ja-JP&gl=JP)にアクセスし、`Ubuntu 24.04`をインストールします。

2. インストールが完了したら、Ubuntuを起動します。

3. 初回起動時に、UNIXユーザー名とパスワードの設定を求められるので、適切に設定してください。

4. システムを最新の状態にするため、以下のコマンドを実行してパッケージを更新します。

```bash
sudo apt update && sudo apt upgrade -y
```

### 3. Ubuntu 24.04 の日本語環境設定

#### 1. 日本語言語パックのインストール
```bash
sudo apt -y install language-pack-ja
```

#### 2. システムロケールの日本語への変更
```bash
sudo update-locale LANG=ja_JP.UTF8
```

#### 3. WSL2の再起動
1. `exit`コマンドでUbuntuを終了します。
2. PowerShellで`wsl --shutdown`コマンドを実行し、WSL2を終了します。
3. Ubuntuを再度起動します。

#### 4. タイムゾーンの設定
以下のコマンドを実行し、「アジア」→「東京」を選択します。
```bash
sudo dpkg-reconfigure tzdata
```

#### 5. 日本語マニュアルのインストール
```bash
sudo apt install -y manpages-ja manpages-ja-dev
```

#### 6. 設定の確認
以下のコマンドを実行し、`LANG=ja_JP.UTF8`が表示されれば日本語化は成功です。
```bash
locale
```

### 4. Zsh のインストールと設定

Zshは、bashの拡張版シェルで、より多くのカスタマイズオプションと便利な機能を提供します。

#### 1. Zsh のインストール
```bash
sudo apt install zsh
```

#### 2. Zsh をデフォルトシェルに設定
```bash
chsh -s $(which zsh)
```

#### 3. WSL2 の再起動
1. `exit`コマンドでUbuntuを終了します。
2. PowerShellで`wsl --shutdown`コマンドを実行し、WSL2を終了します。
3. Ubuntuを再度起動します。

#### 4. Zsh の初期設定
Ubuntuを起動すると、以下のZshの初期設定画面が表示されます。「2」を入力して推奨設定を適用します。

```
This is the Z Shell configuration function for new users,
zsh-newuser-install.
You are seeing this message because you have no zsh startup files
(the files .zshenv, .zprofile, .zshrc, .zlogin in the directory
~).  This function can help you with a few settings that should
make your use of the shell easier.

You can:

(q)  Quit and do nothing.  The function will be run again next time.

(0)  Exit, creating the file ~/.zshrc containing just a comment.
     That will prevent this function being run again.

(1)  Continue to the main menu.

(2)  Populate your ~/.zshrc with the configuration recommended
     by the system administrator and exit (you will need to edit
     the file by hand, if so desired).

--- Type one of the keys in parentheses ---
```

#### 5. Zsh の確認
以下のコマンドを実行し、`/usr/bin/zsh`が表示されればZshの設定は成功です。
```zsh
echo $SHELL
```

#### 6. Oh My Zsh のインストール
Oh My Zshは、Zshの設定を管理するフレームワークで、多くのプラグインやテーマを提供します。
```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### 5. Neovim のインストールと設定

Neovimは、Vimの改良版で、より高速で拡張性に優れたテキストエディタです。

#### 1. Neovim のインストール
```zsh
sudo apt install neovim
```

#### 2. Neovim をデフォルトエディタに設定

```zsh
nvim ~/.zshrc
```

上記のコマンドで`~/.zshrc`ファイルを開き、下記の行を追加します。

```
# Set neovim as the default editor and visual editor
export EDITOR='nvim'
export VISUAL='nvim'
```

#### 3. 変更の反映
```zsh
source ~/.zshrc
```

#### 4. システムのデフォルトエディタとして Neovim を設定
```zsh
sudo update-alternatives --install /usr/bin/editor editor $(which nvim) 100
```

#### 5. デフォルトエディタの確認
以下のコマンドを実行し、出力が`nvim`になっていれば、Neovimがデフォルトエディタとして設定されています。
```zsh
echo $EDITOR
echo $VISUAL
```

#### 6. nano のアンインストール
Neovimを使用するため、nanoは不要になります。以下のコマンドでアンインストールします。
```zsh
sudo apt remove nano
sudo apt autoremove
```

### 6. Git のアップデートと設定

#### 1. 既存の Git のアンインストール
```zsh
sudo apt remove git
sudo apt autoremove
```

#### 2. 最新バージョンの Git をインストールするためのリポジトリの追加
```zsh
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
```

#### 3. Git のインストール
```zsh
sudo apt install git
```

#### 4. Git のバージョン確認
```zsh
git --version
```

#### 5. Git の基本設定
ユーザー名とメールアドレスを設定します。
```zsh
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

#### 6. その他の便利な設定
```zsh
# デフォルトブランチ名をmainに設定
git config --global init.defaultBranch main

# デフォルトエディタをNeovimに設定
git config --global core.editor nvim

# 色付きの出力を有効化
git config --global color.ui auto

# よく使うコマンドのエイリアスを設定
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
```

### 7. SSH の設定

SSHキーを設定することで、GitHubなどのリモートリポジトリとスマートに接続できるようになります。

#### 1. SSH キーの生成
```zsh
ssh-keygen -t ed25519 -C "your_email@example.com"
```
キーの保存場所とパスフレーズを設定します。セキュリティを高めるため、パスフレーズの設定をおすすめします。

#### 2. SSH エージェントの起動
```zsh
eval "$(ssh-agent -s)"
```

#### 3. SSH キーの登録
```zsh
ssh-add ~/.ssh/id_ed25519
```

#### 4. 公開鍵のコピー
xclipがインストールされていない場合は、先に`sudo apt install xclip`でインストールしてください。
```zsh
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard
```

#### 5. GitHubへの公開鍵の登録
1. GitHubにログインします。
2. 右上のユーザーアイコンをクリックし、「Settings」を選択します。
3. 左側のメニューから「SSH and GPG keys」を選択します。
4. 「New SSH key」をクリックします。
5. タイトルを入力し、コピーした公開鍵を貼り付けます。
6. 「Add SSH key」をクリックして登録を完了します。

#### 6. SSH 接続のテスト
以下のコマンドを実行し、`Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.`と表示されれば、SSH接続は成功です。
```zsh
ssh -T git@github.com
```

### 8. Peco のインストールと設定

Pecoは、インタラクティブなフィルタリングツールで、コマンド履歴の検索などに非常に便利です。

#### 1. 最新バージョンのダウンロード
[Pecoのリリースページ](https://github.com/peco/peco/releases/)で最新バージョンを確認し、以下のコマンドのURLを適宜更新してください。

`peco_linux_amd64.tar.gz`のアーキテクチャについては、ご自身の環境のものに適宜置き換えてください。

```zsh
wget https://github.com/peco/peco/releases/download/v0.5.11/peco_linux_amd64.tar.gz
```
wgetがインストールされていない場合は、`sudo apt install wget`でインストールしてください。

#### 2. ダウンロードしたファイルの解凍
```zsh
tar -zxvf peco_linux_amd64.tar.gz
```

#### 3. pecoバイナリファイルの移動
```zsh
sudo mv peco_linux_amd64/peco /usr/local/bin/
```

#### 4. pecoバイナリファイルに実行権限を付与
```zsh
sudo chmod +x /usr/local/bin/peco
```

#### 5. インストールしたPeco のバージョンを確認
以下のコマンドでPecoのバージョンが表示されれば、Pecoのインストールは成功です。
```zsh
peco --version
```

#### 6. 不要なファイルを削除
インストールに使用したファイルは不要になります。以下のコマンドで削除します。
```zsh
rm -rf peco_linux_amd64 peco_linux_amd64.tar.gz
```

#### 7. Peco の設定
以下のコマンドで`~/.zshrc`ファイルを開きます。
```zsh
nvim ~/.zshrc
```

以下の設定を追加することで、Ctrl+Rでコマンド履歴を検索できるようにします。
```
# Configure peco for interactive filtering of command history with Ctrl+R
function peco-history-selection() {
  BUFFER=`history -n 1 | tac | awk '!a[$0]++' | peco`
  CURSOR=$#BUFFER
  zle reset-prompt
}
zle -N peco-history-selection
bindkey '^R' peco-history-selection
```

この設定により、Ctrl+Rを押すとPecoを使用してコマンド履歴を対話的に検索できるようになります。
