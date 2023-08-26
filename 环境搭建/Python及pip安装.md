# Python及pip安装

## python下载及安装

1. 前往 [官网](https://www.python.org/downloads/) 下载并安装python

2. 验证安装是否成功

```shell
python -V
python3 -V

// 或者
python --version
python3 --version
```

## pip安装

1. 获取 `get-pip.py`

```
curl 'https://bootstrap.pypa.io/get-pip.py' > get-pip.py
```

2. 执行 pip 安装

```
python3 get-pip.py
```

3. 检查版本

```
pip3 -V

// 或
pip3 --version
```

4. 使用 pip 安装需要的包

```
pip3 install [包名]
```