

## 1、文档使用说明

### 1.1 版本历史

| 日期      | 版本号 | 作者   | 备注       |
| --------- | ------ | ------ | ---------- |
| 2017.5.12 | 1.0    | MinHow | 新版本发布 |

### 1.2 文档介绍

&emsp;&emsp;本文档的接口遵循RESTful设计风格...。

### 1.3 接口约定

#### 1.3.1 登录认证流程

&emsp;&emsp;基于JWT认证机制，实现登录认证流程...。

#### 1.3.2 Token 验证

`Token`访问有效期为两个小时，刷新有效期为两周...。

> 注意事项：...。

#### 1.3.3 返回结果

所有返回结果以 JSON 格式返回；接口返回一共有三种情况：

1. 操作成功后返回，范例：

```json  
{
  "code": "200",//状态码
  "msg": "SUCCESS"//信息
} 
```

2. 成功返回数据，范例：

```json  
{
  "data": {//数据
    "name": "minhow",
    "age": "18"
  },
  "code": "200",//状态码
  "msg": "SUCCESS"//信息
}
```

3. 错误返回，范例：

```json  
{
  "err_code": "1001",//错误状态码
  "err_msg": "认证失败，请重新登录！"//错误信息
}
```

#### 1.3.4 全局响应状态码说明

| 状态码 | 说明                   |
| ------ | ---------------------- |
| 200    | 操作成功               |
| 1001   | 认证失败，请重新登录！ |
| 1002   | 参数不合法！           |

## 2. 登录

### 2.1 功能描述

提供手机号和密码的登录方式。

### 2.2 请求说明

> 请求方式：POST
>
> 请求URL ：[login](#)

### 2.3 请求参数

| 字段     | 字段类型 | 字段说明 |
| -------- | -------- | -------- |
| phone    | int      | 手机号   |
| password | string   | 密码     |

### 2.4 返回结果

```json  
{
  "data": {
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOi8vc2FsZS1hcGkuZGV2L2xvZ2luIiwiaWF0IjoxNDkxNTMyOTI4LCJleHAiOjE0OTIyNTI5MjgsIm5iZiI6MTQ5MTUzMjkyOCwianRpIjoiN1hCUXdwN1FHZmxUdHVVQiIsInV1aWQiOiI1MDZjYWY3MCJ9.FyyXagHtBfDBtMJZPV_hm2q6CVULpY63JPDGDHXc"
  },
  "code": "200",
  "msg": "SUCCESS"
}
```

### 2.5 返回参数

| 字段  | 字段类型 | 字段说明 |
| ----- | -------- | -------- |
| token | string   | token值  |

### 2.6 错误状态码

| 状态码 | 说明               |
| ------ | ------------------ |
| 3001   | 其他认证错误信息！ |
| 3002   | 用户不存在！       |
| 3003   | 用户名或密码有误！ |

## 3. 刷新Token值

### 3.1 功能描述

通过请求刷新 Token 接口，获取新的 Token 值。

### 3.2 请求说明

> 请求方式：PATCH<br>
> 请求URL ：[refresh-tokens](#)

### 3.3 请求参数

无参数

### 3.4 返回结果

```json  
{
  "data": {
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOi8vdm95YWdlLmRldi9hdXRob3JpemF0aW9ucy9yZWZyZXNoLXRva2VucyIsImlhdCI6MTQ4OTk4Nzg0OCwiZXhwIjoxNDg5OTg3OTc3LCJuYmYiOjE0ODk5ODc5MTcsImp0aSI6IlRvNmxzamhwTTNpcmhRQlAiLCJ1dWlkIjoiNWZlYzI0NzAifQ.hgZsQq5rT5VXAwUilEv5P1JIhLrctJPKAkKWBSqwu3c"
  },
  "code": "200",
  "msg": "SUCCESS"
}
```

### 3.5 返回参数

| 字段  | 字段类型 | 字段说明 |
| ----- | -------- | -------- |
| token | string   | token值  |

### 3.6 错误状态码

参见 [全局响应状态码说明](#124-全局响应状态码说明)	

## 4. 个人中心

### 4.1 功能描述

获取个人中心。

### 4.2 请求说明

> 请求方式：GET
>
> 请求URL ：[me/personal](#)

### 4.3 请求参数

无参数

### 4.4 返回结果

```json  
{
  "data": {
    "information": [
      {
        "avatar": "http://blog.minhow.com",
        "nickname": "minhow",
        "age": "25"
      }
    ],
    "news": [
      {
        "title": "minhow",
        "content": "I am MinHow"
      }
    ]
  },
  "code": "200",
  "msg": "SUCCESS"
}
```

### 4.5 返回参数

| 字段        | 字段类型 | 字段说明 |
| ----------- | -------- | -------- |
| information | array    | 信息     |
| avatar      | string   | 头像地址 |
| nickname    | string   | 昵称     |
| age         | int      | 年龄     |
|             |          |          |
| news        | array    | 消息列表 |
| title       | string   | 标题     |
| content     | string   | 内容     |

每组数组用空行隔开

### 4.6 错误状态码

参见 [全局响应状态码说明](../introduction.html/#134-全局响应状态码说明)