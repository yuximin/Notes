# iOS制作cocoapods公有库和私有库

| 本文以 `github` 为例，介绍如何创建一个可供使用的 `Pod` 代码库。

## 一、github上新建一个仓库，用来存放源码

注意，需要添加一个 `license`，一般选择 `MIT License`。

创建远程仓库之后，将项目克隆到本地。

## 二、创建podspec文件

这里可以选择手动创建或者`pod lib create`创建

### 2.1 手动创建

1. 进入本地仓库目录，执行 `pod spec create projectName` 命令。

2. 执行完命令之后，会在当前目录下，生成一个名为 `projectName.podspec` 的文件。

3. 用编辑器打开和编辑生成的 spec 文件，可参考下面示例。

```s
#
# Be sure to run `pod lib lint XMKit.podspec' to ensure this is a
# valid spec before submitting.
#
# Any lines starting with a # are optional, but their use is encouraged
# To learn more about a Podspec see https://guides.cocoapods.org/syntax/podspec.html
#

Pod::Spec.new do |s|
  s.name             = 'XMKit'
  s.version          = '0.1.3'
  s.summary          = '工具库'

# This description is used to generate tags and improve search results.
#   * Think: What does it do? Why did you write it? What is the focus?
#   * Try to keep it short, snappy and to the point.
#   * Write the description between the DESC delimiters below.
#   * Finally, don't worry about the indent, CocoaPods strips it!

  s.description      = <<-DESC
iOS开发常用工具类
                       DESC

  s.homepage         = 'https://github.com/yuximin/XMKit'
  # s.screenshots     = 'www.example.com/screenshots_1', 'www.example.com/screenshots_2'
  s.license          = { :type => 'MIT', :file => 'LICENSE' }
  s.author           = { 'yuximin' => 'whaledestine@163.com' }
  s.source           = { :git => 'https://github.com/yuximin/XMKit.git', :tag => s.version.to_s }
  # s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'

  s.ios.deployment_target = '10.0'

  s.source_files = 'Sources/**/*'
  
  # s.resource_bundles = {
  #   'XMKit' => ['XMKit/Assets/*.png']
  # }

  # s.public_header_files = 'Pod/Classes/**/*.h'
  # s.frameworks = 'UIKit', 'MapKit'
  # s.dependency 'AFNetworking', '~> 2.3'
end
```

部分参数说明：

* s.name 库名称

* s.version 库版本

* s.summary 库摘要

* s.description 库描述（最好比summary多写一些描述）

* s.homepage 远程仓库地址，即 GitHub 的地址，或者你使用的其他的 Gitlab，码云的地址

* s.license MIT许可证（The MIT License），软件授权条款

* s.author 作者信息

* s.source 下载地址，即远程仓库的 GitHub下载地址(clone 地址)，使用.git结尾

* s.homepage 远程仓库地址

*  s.source_files 源码路径，在项目中的相对路径

* s.dependency 如果你的库依赖其他的 Podspecs，可以添加这些依赖项，例如 s.dependency 'AFNetworking', '~> 3.2.1'

### 2.2 pod lib create

执行 `pod lib create projectName` 命令，创建本地代码库。该操作会自动生成队形库名称的 `spec` 文件。

## 三、上传源码及设置tag

1. 将源码放到对应目录中，即 `spec` 文件中 `s.source_files` 对应的目录。

2. 将源码提交到远程仓库。

3. 执行 `git tag '0.01'` 和 `git push --tags` 添加tag并推送到远程仓库。注意，这里的tag必须和spec文件中的版本保持一致，否则会导致spec文件验证失败。

## 四、验证podspec文件

在本地目录下执行命令 `pod spec lint --allow-warnings`，验证成功会看到 `xxx passed validation.`

## 五、发布pod

### 5.1 发布到cocoapods

1. 如果没有注册过pod需要先使用以下命令注册：

`pod trunk register email@xxx.com "username"`

2. 注册成功之后，在终端执行命令发布pod

`pod trunk push projectName.podspec`

3. 发布成功之后可通过 `pod search projectName` 查询你的代码库，如果搜索不到，执行 `rm ~/Library/Caches/CocoaPods/search_index.json` 之后重新搜索。

### 5.2 发布到自己的仓库

1. github上新建一个仓库，用作私有库的索引库。

2. 添加私有索引库搜索地址

`pod repo add yourRepoName youtRepoPath`

其中 `yourRepoName` 为索引库名称，用于区分不同索引库，命名随意但是最好有标识性。 `youtRepoPath` 内容设置为1中创建的远程仓库地址，一般以`.git`结尾。

3. 添加完成后， 可通过 `pod repo list` 查看本地索引库列表

4. 将私有库的podspec文件推送到私有索引库中

`pod repo push yourRepoName yourProjectName.podspec --allow-warnings`

5. 安装远程库

Podfile最上方，新增source信息，内容为私有索引库地址。

```
source 'https://xxx.git'
```

然后正常pod引入库文件，`pod 'xxx'`，执行 `pod install` 即可。

如果，不同索引库中存在相同名称的代码库，则需要在pod时加入source来区分，示例如下：

`pod 'xxx', :source => '索引库地址'`

## 参考文章

[iOS制作cocoapods公共库以及私有库](https://juejin.cn/post/6844903871718883342)

[iOS学习（四十四）组件化：构建私有远程pod仓库及处理pattern did not match any file](https://www.jianshu.com/p/2ccc41022277)