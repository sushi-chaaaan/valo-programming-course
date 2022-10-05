# WSLセットアップ作業

## WSLの有効化

1. WindowsPowershellを起動する(windowsアイコン右クリック→ターミナル(管理者)

2. ```powershell
   wsl --isntall
   ```

3. この変更を有効にするにはシステムの再起動が必要ですと出るまで待ってPCを再起動する。

4. ここでエラーが出た場合は、[ここ](https://aka.ms/wsl2kernel)からカーネルをダウンロードしてインストール

5. Powershellを起動して
   ```powershell
   wsl --set-default-version 2
   ```

6. 

## はじめに

**<u>VSCode-serverがバグるので先に必ずWindows側へのVSCodeのインストールを済ませましょう</u>**

## リポジトリを日本国内へ

```bash
cd /etc/apt

sudo sed -i.bak -e "s/http:\/\/archive\.ubuntu\.com/http:\/\/jp\.archive\.ubuntu\.com/g" sources.list
```

## sudo

```bash
sudo update-alternatives --config editor

sudo visudo

- %sudo   ALL=(ALL:ALL) ALL

+ %sudo   ALL=(ALL:ALL) NOPASSWD:ALL
```

## いつもの

```bash
sudo apt update && sudo apt upgrade -y

sudo apt install language-pack-ja
sudo update-locale LANG=ja_JP.UTF-8
# WSL再起動

sudo dpkg-reconfigure tzdata
sudo apt install manpages-ja manpages-ja-dev
```

## homebrew系列のインストールと初期設定

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
#指示に従って.profileをいじる

sudo apt install build-essential

brew install gcc git

brew doctor

sudo chmod +x /home/linuxbrew/.linuxbrew/share/git-core/contrib/diff-highlight/diff-highlight
sudo ln -s /home/linuxbrew/.linuxbrew/share/git-core/contrib/diff-highlight/diff-highlight /usr/local/bin/diff-highlight

git config --global user.name "sushi-chaaaan"
git config --global user.email sushi_code@outlook.jp
```

## zsh

### 基本設定

```bash
# change shell from bash to zsh
brew install zsh

which zsh | sudo tee -a /etc/shells
chsh -s `which zsh`

# ここで一度exit,再ログイン

{
echo ''
echo '# homebrew'
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"'
echo 'fpath=($(brew --prefix)/share/zsh/site-functions $fpath)'
echo 'autoload -Uz compinit'
echo 'compinit -u'
} >> ~/.zshrc

source .zshrc
```

### Sheldon

```bash
brew install sheldon

sheldon init --shell zsh

{
echo ''
echo '# sheldon'
echo 'eval "$(sheldon source)"'
} >> .zshrc
```

#### config

```bash
vim .sheldon/plugins.toml

[plugins]

[plugins.zsh-defer]
github = 'romkatv/zsh-defer'

[plugins.fast-syntax-highlighting]
github = 'zdharma/fast-syntax-highlighting'

[plugins.zsh-completions]
github = 'zsh-users/zsh-completions'

[plugins.zsh-autosuggestions]
github = 'zsh-users/zsh-autosuggestions'
use = ["{{name}}.zsh"]

[plugins.history-search-multi-word]
github = 'zdharma/history-search-multi-word'
apply = ["defer"]

[plugins.powerlevel10k]
github = 'romkatv/powerlevel10k'

[plugins.zsh-256color]
github = 'chrissicool/zsh-256color'

[plugins.zsh-extract]
github = 'le0me55i/zsh-extract'

[templates]
defer = { value = 'zsh-defer source "{{ file }}"', each = true }
```

```bash
sheldon lock --update
source .zshrc
```

## 便利ツール

```bash
sudo apt update && sudo apt install unzip # 色々必須

brew install bat exa bottom duf fd

{
echo ''
echo '# alias'
echo 'alias cat="bat"'
echo 'alias ll="exa -l -h -@ -mU --icons --git --time-style=long-iso --color=automatic --group-directories-first"'
echo 'alias du="dust"'
echo 'alias df="duf"'
echo 'alias cpu="btm"'
echo 'alias reset-comp="rm -f ~/.zcompdump; compinit"'
} >> ~/.zshrc

code .
# VSCode Server Install

# Chrome Install
# GPGキーの追加
sudo wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmour -o /usr/share/keyrings/google-keyring.gpg

# リポジトリの追加
sudo sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/google-keyring.gpg] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list'

# google chromeのインストール
sudo apt update && sudo apt install -y google-chrome-stable
```

## Python環境

```bash
# dependencies
sudo apt-get update; sudo apt-get install make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev

# pyenv
curl https://pyenv.run | bash

# .zshrcへ追記
{
echo ''
echo '# pyenv'
echo 'export PYENV_ROOT="$HOME/.pyenv"'
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"'
echo 'eval "$(pyenv init -)"'
} >> ~/.zshrc

pyenv install 3.10.7
pyenv global 3.10.7

# poetry
curl -sSL https://install.python-poetry.org | python3 -
mkdir -p .zfunc
poetry completions zsh > ~/.zfunc/_poetry
poetry config virtualenvs.in-project true

{
echo ''
echo '# poetry'
echo ''
} >> ~/.zshrc
```

## LaTeX

https://texwiki.texjp.org/?Linux#texliveinstall

```bash
curl -OL http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
tar xvf install-tl-unx.tar.gz
cd install-tl-2*
sudo ./install-tl -no-gui -repository http://mirror.ctan.org/systems/texlive/tlnet/

sudo /usr/local/texlive/????/bin/*/tlmgr path add
sudo tlmgr update --self --all

mkdir -p ~/.config/ssmarco && vim ~/.config/ssmarco/macro.json

brew install perl cpanm
cpanm Log::Log4perl Log::Dispatch::File YAML::Tiny File::HomeDir Unicode::GCString
```

### macro.json

```json
[
    "ssmacro.clear",
    {
        "command": "ssmacro.replace",
        "args": {
            "info": "句点置換",
            "find": "。|．",
            "replace": ".",
            "all": true,
            "reg": true,
            "flag": "gm"
        }
    },
    {
        "command": "ssmacro.replace",
        "args": {
            "info": "読点置換",
            "find": "、|，",
            "replace": ",",
            "all": true,
            "reg": true,
            "flag": "gm"
        }
    }
]

```

### VSCodeの設定

`メニュー > ファイル > 基本設定 > キーボードショートカット > jsonを出す`

### Keybindings.json

```json
{
    "key": "ctrl+oem_comma ctrl+oem_period",
    "command": "ssmacro.macro",
    "args": {"path": "/home/{user_name}/.config/ssmacro/macro.json"}
}
```

## Node.js環境

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

# .zshrcを適宜編集する。

source .zshrc

nvm install node
nvm install --lts
```

## Ruby環境

```bash
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
# おそらくhomebrew経由でインストールされる

{
echo ''
echo '# rbenv'
echo 'eval "$(~/.rbenv/bin/rbenv init - zsh)"'
} >> ~/.zshrc

sudo apt-get update && sudo apt-get install autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libgmp-dev libncurses5-dev libffi-dev libgdbm6 libgdbm-dev libdb-dev uuid-dev

RUBY_CONFIGURE_OPTS="--with-openssl-dir=$(brew --prefix openssl@3)" rbenv install 3.1.2

brew install rbenv-gemset
```

## 困ったときは

### 保完が効かない

```bash
rm -f ~/.zcompdump; compinit
(reset-comp)
```

## シェルスクリプトを作ろう

### update.sh

2022.10.03

```sh
if type "apt" >/dev/null 2>&1; then
    sudo apt update
    sudo apt upgrade -y
elif type "yum" >/dev/null 2>&1; then
    sudo yum update -y
    sudo yum upgrade -y
else
    echo "No package manager found"
fi

if type "brew" >/dev/null 2>&1; then
    brew update && brew upgrade
else
    echo "No brew found"
fi

if type "tlmgr" >/dev/null 2>&1; then
    sudo tlmgr update --self --all
else
    echo "No tlmgr found"
fi
```

