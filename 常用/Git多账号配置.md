# Git 多账号配置

## 前言

一般来说，安装好 git 之后，都会配置一个全局的 config 信息：

```shell
git config --global user.name "xxxx" // 配置全局用户名
git config --global user.email "xxxx" // 配置全局邮箱
```

但是，当你想在一台机器上连接多个 git 服务器时，这种配置方式就不支持了。

## 配置多账户

本文以下面两个账号为例进行多账号配置

GitHub

* 用户名：admin

* 邮箱: admin@163.com

GitLab

* 用户名: Jack

* 邮箱: Jack@163.com

### 1. 清除全局配置

使用下面命令查看全局配置：

```
git config --global --list
```

如发现有 `user.name` 和 `user.email` 信息，则执行下面命令清除：

```
git config --global --unset user.name
git config --global --unset user.email
```

### 2. 生成秘钥

```
ssh-keygen -t rsa -C "[邮箱地址]"
```

执行上面命令之后，会提示

```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/whaley/.ssh/id_rsa):
```

这一步是用来指定生成秘钥文件存放的位置。此时可以直接回车使用默认路径 `/Users/whaley/.ssh/id_rsa` ，或者修改为自定义路径。

在进行多账号配置的时候，我们一般会修改此处的秘钥名称，以区分不同的账号。

例：

对于上述的 GitHub 账号执行以下命令：

```
ssh-keygen -t rsa -C "admin@163.com"
```

同时回车之后，修改秘钥文件名为 `/Users/whaley/.ssh/id_rsa_github`

对于上述的 GitLab 账号则执行下面命令：

```
ssh-keygen -t rsa -C "Jack@163.com"
```

然后回车之后，修改秘钥文件名为 `/Users/whaley/.ssh/id_rsa_gitlab`

> 注意：这里的秘钥名称可以随意定义，用于区分不同账号

秘钥名称指定完成之后，回车，会提示输入密码，这里直接回车就行了，一般不设置密码。

到这，秘钥文件就生成好了。可以在 `/User/whaley/.ssh/` 目录查看生成好的秘钥文件。秘钥文件一般都是成对存在的，私钥名称为无后缀名的 `id_rsa` ，公钥名称为 `私钥名称.pub`，如 `id_rsa.pub` 。

### 3. 添加 SSH Keys

将上一步生成的公钥，添加到对应的 GitHub 或 GitLab 服务器设置的 `SSH Keys` 中。

### 4. 添加私钥

```
ssh-add [秘钥文件路径]
```

例：

```
ssh-add ~/.ssh/id_rsa_github
ssh-add ~/.ssh/id_rsa_gitlab
```

可在添加前后执行下面命令查看私钥列表，以确定是否添加完成

```
ssh-add -l
```

### 5. 管理秘钥

这一步是多账户配置的关键。

编辑 ~/.ssh/config 文件，如果没有，请新建。

配置内容如下：

```
Host github
  HostName github.com
  user admin
  IdentityFile ~/.ssh/id_rsa_github
Host gitlab
  HostName gitlab.com
  user Jack
  IdentityFile ~/.ssh/id_rsa_gitlab
```

其中：

`Host` 内容为自定义，是对 `HostName` 的一个别名

`HostName` 对应远程服务器的域名，一般对应于 clone 复制出来地址的域名部分

`user` 对应于你在服务器上的用户名

`IdentityFile` 对应于相应的秘钥路径

> 如：`git@github.com:admin/demo.git` 对应的 `HostName` 为 `github.com`。
>
> 注意：上文说了，`Host` 是对 `HostName` 的别名，所以这里的 `git@github.com:admin/demo.git` 也就等价于 `git@github:admin/demo.git`

### 6. 检验

到上一步，所有的配置都已完成。可以执行下面命令来检验一下是否配置成功。

```
ssh -T git@[HostName]
```

配置成功的话，会输出一条欢迎语。

```
Hi xxxx! You've successfully authenticated, but GitHub does not provide shell access.
```

这一步也可能出现如下问题：

```
The authenticity of host 'gitee.com (xxx.xxx.xxx.xxx)' can't be established.
ECDSA key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no)?
```

这个是因为配置多账号时生成多个秘钥导致 `known_hosts` 文件错误，此时直接回车或输入  `yes` 会自动生成新的 `known_hosts` 文件。再次输入验证命令即可获得正确输出。