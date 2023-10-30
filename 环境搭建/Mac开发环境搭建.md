# Mac 开发环境搭建

## 安装 XCode

`App Store` 自行搜索下载


## 安装Homebrew

[Homebrew 官网](https://brew.sh/)

[Homebrew 中文网](https://brew.idayer.com/)

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 国内安装
/bin/bash -c "$(curl -fsSL https://gitee.com/ineo6/homebrew-install/raw/master/install.sh)"
```

## 安装 ruby

虽然 Mac OS 自带 ruby 环境，但是系统 ruby 在使用时有很多限制。所以一般我们都会再安装一个 ruby 作为日常使用。

1. 使用 brew 安装 ruby

```shell
brew install ruby
```

2. 配置环境变量（这一步主要是为了让 ruby 指令能找到我们自行安装的 ruby 版本）

```shell
# 设置环境变量
echo 'export PATH="/opt/homebrew/opt/ruby@3.0/bin:$PATH"' >> ~/.zshrc

# 保存设置
source ~/.zshrc

# 查看环境变量
echo $PATH
```

## 安装Cocoapods

```shell
sudo gem install -n /usr/local/bin cocoapods

# 指定版本
sudo gem install -n /usr/local/bin cocoapods -v 1.11.3
```

## 推荐安装iTerm2，并且安装oh-my-zsh

[iTerm2](https://iterm2.com/)

[oh-my-zsh](https://ohmyz.sh/)

```shell
# 1、下载并安装iTerm2
 
# 2、安装oh-my-zsh
$ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
 
# 3、编辑zsh配置
# - 进入zsh编辑
$ vim ~/.zshrc
# - 在plugins中添加以下
plugins=(
    git
    z
    zsh-autosuggestions # 自动补全
    zsh-syntax-highlighting # 语法高亮
)
 
# 4、使其生效
$ source ~/.zshrc
```

