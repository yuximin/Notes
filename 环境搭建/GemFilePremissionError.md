# Mac gem install 报错解决 Error: While executing gem ... (Gem::FilePermissionError)

在 Mac 上尝试使用如下命令安装 `cocoapods`:

```
sudo gem install -n /usr/local/bin cocoapods
```

结果遇到如下报错：

```
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/gems/2.6.0 directory.
```

## 问题分析：

Mac 系统自带 ruby，并且有权限限制，不允许用户对其进行访问。

## 解决方案：

系统自带的 ruby 我们最好不要对其进行升级或修改，防止出现不可预料的。

可以使用 homebrew 再安装一个 ruby，这样系统用系统的，我们使用自己安装的。

## 步骤

1. 安装ruby

```shell
brew install ruby

# 安装指定版本
brew install ruby@3.0

# 如果上一个命令报错，试着执行这个（报错信息里面应该也有提示）
arch -arm64 brew install ruby
```

2. 设置环境变量

```shell
# 设置环境变量
echo 'export PATH="/opt/homebrew/opt/ruby@3.0/bin:&PATH"' >> ~/.zshrc

# 保存设置
source ~/.zshrc
```

