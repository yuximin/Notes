# Git 本地配置多个远程账号

## 设置账号

```shell
# 设置全局账号用户名和邮箱
git config --global user.name 'your username'
git config --global user.emil 'your email'

# 查看全局配置
git config --list
```

## 生成 ssh 密钥

Mac OS 下，ssh 密钥一般存放在 `~/.ssh` 目录下，名称为 `id_rsa` 。可查看该目录下是否已经存在密钥文件，或者可以直接使用下面的命令生成密钥。

```shell
# 生成 ssh 密钥
# 该指令未指定密钥文件的生成目录，指令执行过程中会提示你确认目标路径，默认存放到 ~/.ssh 目录，你也可以对目标路径进行修改
ssh-keygen -t rsa -C "your email"

# 也可以通过下面指令指定密钥文件的生成目录
# 想要在本地配置多个账号的话，需要生成跟上面不同名称的密钥
ssh-keygen -t rsa -f ~/.ssh/id_rsa_xxx -C "your email"
```

## 添加新 ssh 密钥

默认 SSH 只会读取 id_rsa，对于新生成的密钥，需要手动添加到 SSH agent。

```shell
# 添加密钥文件
ssh-add ~/.ssh/id_rsa_xxx

# 查看所有添加过的文件
ssh-add -l

# 删除所有添加过的文件
ssh-add -D
```

## 将公钥添加到远程账号

生成的公钥需要添加到远程账号中，才能建立连接。

以 github 为例，在 settings--SSH and GPG keys 中，新建一个 SSH key。将本地生成的公钥内容复制进去，即 `~/.ssh/id_rsa.pub` 文件内容。

一个公钥对应一个远程账号，将公钥内容配置到对应的远程账号中。

## 配置 config 文件

查看 `~/.ssh/` 目录下是否存在 config 文件，如果没有则新建一个。

```shell
touch config
vim config
```

在 config 中添加如下内容：

```
# 第一个账号
Host gitlab // 仓库网站别名，随便取
HostName gitlab.weixiangtec.com // 仓库网站域名
User git // 仓库网站上的用户名
IdentityFile ~/.ssh/id_rsa // 私钥对应的绝对路径

# 第二个账号
Host github
HostName github.com
User git
IdentityFile ~/.ssh/id_rsa_github
```

## 测试是否联通

```shell
ssh -T git@gitlab.weixiangtec.com
ssh -T git@github.com
```

成功结果如下：

```shell
➜  .ssh ssh -T git@github.com
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ECDSA key fingerprint is SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com,20.205.243.166' (ECDSA) to the list of known hosts.
Hi yuximin! You've successfully authenticated, but GitHub does not provide shell access.
```

## 解决ssh-add重启电脑后失效问题

1. 打开系统自带应用【实用工具】中的【自动操作】
2. 选择菜单中【文件】-【新建】
3. 文稿类型选择【应用程序】
4. 在打开的窗口中选择【实用工具】，双击【运行Shell脚本】，填写脚本内容，即 `ssh-add ~/.ssh/id_rsa_xxx`
5. 选择菜单中【文件】-【导出】，命名为 `SSH.app`，位置选择【应用程序】，点击存储
6. 打开【系统偏好设置】-【登录项】，点击添加按钮，选择刚才导出的 `SSH.app`
7. 开机之后就会自动运行 `SSH.app` 添加SSH文件

至此就配置完成啦，可以在本地同时连接多个 git 远程账号啦。
