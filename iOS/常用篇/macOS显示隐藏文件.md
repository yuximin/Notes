命令行输入如下命令即可显示隐藏文件：

```
defaults write com.apple.finder AppleShowAllFiles -boolean true;killall Finder
```

输入如下命令即可恢复文件隐藏：

```
defaults write com.apple.finder AppleShowAllFiles -boolean false;killall Finder
```
