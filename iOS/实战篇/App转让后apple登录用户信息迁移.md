# App转让后apple登录用户信息迁移

## 前言

iOS App转让之后，原先使用苹果登录的用户再次登录时，获取到的用户标识会发生变化，导致后端无法将其与用户的老数据关联起来，造成数据丢失。

## 解决方案

[官方文档](https://developer.apple.com/documentation/sign_in_with_apple/transferring_your_apps_and_users_to_another_team)

[官方文档](https://developer.apple.com/documentation/sign_in_with_apple/bringing_new_apps_and_users_into_your_team)

苹果官方提供了文档，通过老主体下的用户标识，生成新主体下对应的用户标识。

根据文档提供的方案，编写脚本，将老的用户标识转换成新的用户标识，并对数据库中存量用户数据执行用户标识的替换，使用户数据绑定到新的用户标识，即可解决此问题。

## 步骤

### 一、 生成 client_secret

client_secret 其实就是一个 JWT token，使用如下方式生成：

1. 使用转移后的主体，生成一个 `Account and Organizational Data Sharing` 私钥。

[开发者后台](https://developer.apple.com/account/) --> `Certificates, Identifiers & Profiles` --> Keys --> 左上角+号 --> 选择 `Account & Organizational Data Sharing`，`Key Name`随意填写 --> Continue --> Register --> 保存生成的 `Key ID` 并且点击 `Download` 下载生成的 `p8证书`（注意：关闭页面后证书无法下载了，请做好保存）

2. 创建 JWT header

```json
{
  "alg": "ES256", // 加密算法，这里使用 ES256
  "kid": "ABCDEFGHIJ" // 1 中生成的 `Key ID`
}
```

3. 创建 JWT payload

```json
{
  "aud": "https://appleid.apple.com", // 固定值
  "exp": 1699544063, // 过期时间
  "iat": 1699457663, // 注册声明中的发布指示您生成客户端密钥的时间，以 UTC 时间（1970 年 1 月 1 日 00:00:00）之后的秒数表示。使用当前时间对应的时间戳即可。
  "iss": "QWERTYUIOP", // 新主体的 Team ID
  "sub": "com.ohla.social" // app Bundle ID
}
```

4. 生成 token

使用 [JWT](https://jwt.io/) 网站或自行编写脚本生成token。

使用 JWT 网站，将上述的 header 和 payload 拷贝到对应的输入框内，并将 1 中下载的 `p8证书` 的内容拷贝到 `Private Key` 输入框内，此时在 `Encoded` 框内就会生成我们需要的 token.

### 二、 获取用户访问令牌

1. 发送HTTP请求，获取用户访问令牌。

```http
POST /auth/token HTTP/1.1
Host: appleid.apple.com
Content-Type: application/x-www-form-urlencoded


grant_type=client_credentials&scope=user.migration&client_id={client_id}&client_secret={client_secret}
```

* grant_type

请求的资助类型。设置为 `client_credentials`。意思是使用客户端应用程序的凭据访问迁移端点。

* scope

迁移的范围。设置为 `user.migration`。

* client_id

app 包名（Bundle ID）。

* client_secret

步骤一中创建的 token.

2. 请求结果

```http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache


{
  "access_token":"adg6105ed7a4def32adec143038877c2b.0.nx.20LreF67Or9",
  "token_type":"Bearer",
  "expires_in":3600
}
```

## 三、生成传输标识符

1. 发送HTTP请求，获取传输标识符。

```http
POST /auth/usermigrationinfo HTTP/1.1
Host: appleid.apple.com
Content-Type: application/x-www-form-urlencoded
Authorization: Bearer {access_token}


sub={sub}&target={recipient_team_id}&client_id={client_id}&client_secret={client_secret}
```

* sub

老主体下的用户标识（就是要将这个转成新的替换到数据库）。

* target

新主体的 Team ID.

* client_id

app 包名（Bundle ID）。

* client_secret

步骤一中创建的 token.

2. 请求结果

```http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache


{
  "transfer_sub":"760417.ebbf12acbc78e1be1668ba852d492d8a.1827"
}
```

## 四、交换标识符

1. 发送HTTP请求，获取传输标识符。

```http
POST /auth/usermigrationinfo HTTP/1.1
Host: appleid.apple.com
Content-Type: application/x-www-form-urlencoded
Authorization: Bearer {access_token}


transfer_sub={transfer_sub}&client_id={client_id}&client_secret={client_secret}
```

* transfer_sub

步骤二中请求到的 transfer_sub 值。

* client_id

app 包名（Bundle ID）。

* client_secret

步骤一中创建的 token.

2. 请求结果

```http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache


{
  "sub":"820417.faa325acbc78e1be1668ba852d492d8a.0219"
}
```

这里 HTTP 请求结果中的 `sub` 字段就是该用户在新主体下的用户标识，即App转让后该用户通过苹果登录返回的用户唯一标识。

## 五、批量替换数据库

编写脚本代码，批量获取数据库中老的用户标识，通过上述步骤生成新的用户标识并完成替换。