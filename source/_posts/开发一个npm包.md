---
title: 开发一个npm包
date: 2018-05-09 11:20:33
categories: node.js
tags:
  - node.js
---
#### npm账号注册登录
- 官网（https://www.npmjs.com/）注册账号

第一次注册的时候，没有注册成功，页面显示了一个邮箱地址和一串字符码，不管三七二十一的，就将该码发送到所示的邮箱，然后收到一封邮件。
> Your request (35691) has been received.  Our support hours are Monday through Friday, 9:00AM - 6:00PM PST, excluding US holidays.  Your request is being reviewed by our support staff and we'll be responding soon.  To add additional comments, reply to this email.

然后在早上又重新的注册了下，就成功了。

#### 开发自己的包

- 确定包名
通过npm install 所起的包名,进行验证，报错，提示没有该包，那么就可以使用该包名了

- 开发代码
我的第一个包是对一个存在的包做升级处理，代码测试通过后

- package.json文件
这个文件包含了包的相关信息，包名，依赖库，版本,main配置，协议等相关信息

- README.md
readme.md作为一个包重要的使用说明信息，也是不可缺少的一部分，readme的描述清晰度，直接关系到，人们对该包的使用情况。

#### 发布安装
```
npm publish --access=public
```

**注意**

源要切换到官方的源，第三方淘宝源可能会报错