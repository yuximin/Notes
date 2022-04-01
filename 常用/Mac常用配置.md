# Mac 常用配置

## 安装Homebrew

[Homebrew Documentation](https://docs.brew.sh/)

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## 通过Homebrew安装cocoapods和swiftlint

```shell
brew install cocoapods
brew install swiftlint
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

