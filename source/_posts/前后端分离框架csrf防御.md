---
title: 前后端分离框架csrf防御.md
date: 2018-04-27 11:34:44
categories: node.js
tags:
  - csrf
  - 前后端分离
---
前端时间，项目被阿里的安全团队检测，报告存在csrf安全问题，csrf这个攻击方式，以前只是简单的了解，没有深入进入，现在被告知存在这个安全风险，作为已经运行了两三的年的成熟系统，现在真对这个问题，修改，难度还是有些大的。

#### 项目的基本框架
项目的web框架使用的是node.js的express框架，功能操作通过提供接口实现，页面在服务端通过ejs模版引擎渲染。
前端通过封装的ajax对接口进行调用，完成功能操作。

#### 业务架构
项目是一个进行云计算培训的教育平台，分为学习站和管理站，注册登录用户在学习站进行云资源课程的创建和学习。
管理站主要针对管理员和课程创作者使用，进行内容录入。

#### 存在的csrf问题调查
针对这样的一个运行稳定了的系统，进行csrf防护，难度还是有些大的，csrf的防护最好要在框架层面解决。
根据csrf的原理，一个主要的防御策略就是针对有数据增，删，改操作的要统一做随机数验证。
根据这点要求，对项目功能接口做调查，发现一些有一些存在数据库操作的接口使用了get请求，如：删除功能等等。
针对这个存在的问题，我的策略是将其变为post请求方式。
总后对post请求做同一的随机数验证。

#### 解决问题的思路
真对以上的get请求，项目代码命名结构等比较规范，我的策略是将get请求改为post请求，大概有五六十个（主要集中在管理站），这个只完成了第一步，然后对post请求在切面做随记数验证。

#### 实现的思路
在express框架中存在一个csrf中间件，这个中间件的实现思路
1. 在服务端生成一个随记数token，将其存储到session中，同时将改参数交给前端
2. 在请求的时候把这个token加入到请求数据或者头信息中，传给后端
3. 在服务端拦截验证session和客户端所传的token

由于项目没有使用session，使用了redis存储服务端的信息，这里考虑使用redis存储这个token。既然数据不存储在服务端，那么就要存储到客户端。也许你已经想到了，通过cookie来实现。
1. 服务端产生一个随记数token，对该token进行散列算法生成tokenStr字符串。
2. 将这两个token都设置到cookie中，返回到前端。
3. 前端发起请求的时候，从cookie中获取token，把这个token加入到请求数据或者头信息中，发送到服务端
4. 在服务端对 token做同样的散列算法，验证token的合法性
通过该思路对post请求进行改造。

#### 代码实现
 实现一个csrf的中间件,在随记数的实现和验证方法等使用了一个csrf的第三方包csrf
 ```
var Tokens     = require('csrf');
exports.csrfCheck = function csrfCheck(req, res, next) {
  var opts = {};
  var tokens = new Tokens(opts);

  var ignoreMethods = ['GET', 'HEAD', 'OPTIONS'];
  var curSecret = req.cookies._csrf;
  var curToken = req.headers['x-csrf-token'] || req.query['csrfToken'];

  if (ignoreMethods.indexOf(req.method) == -1) {
    if (!curToken || !curSecret) {
      return next(new _e('EUserForbidden', 'invalid csrf token'));
    }
    //验证token
    if (!tokens.verify(curSecret, curToken)) {
      return next(new _e('EUserForbidden', 'invalid csrf token'));
    }
  }

  //生成token
  var secret = tokens.secretSync();
   res.cookie('_csrf', secret, {
    httpOnly: true
  });

  // 设置token_key值
  var token = tokens.create(secret);
  res.cookie('_csrf-token', token, {
    httpOnly: false
  })
  return next();
}
 ```

 在前端ajax的请求中，添加csrf token
 ```
  util.apiPostJSON = function apiPostJSON(options) {
    var fullUrl  = options.query ? toolkit.appendQuery(options.url, options.query) : options.url;
    var postData = options.body ? JSON.stringify(options.body) : undefined;
    var headers = {
      'x-auth-token': localStorage.getItem('xAuthToken') || undefined,
      'x-csrf-token': $.cookie('_csrf-token') || undefined
    };

    $.ajax({
      dataType   : 'json',
      contentType: 'application/json',
      type       : 'POST',
      url        : fullUrl,
      headers    : headers,
      data       : postData,
      success: function(j, s, jqXHR) {
        // 默认成功时刷新页面
        if (!options.success) {
          toolkit.reload();
          return;
        }

        if ('function' === typeof options.success) {
          options.success(jqXHR.responseJSON, jqXHR.status);
        }
      },
      error: function(jqXHR) {
        if ('function' === typeof options.error) {
          options.error(jqXHR.responseJSON, jqXHR.status);
        }
      },
      complete: function(jqXHR) {
        if ('function' === typeof options.complete) {
          options.complete(jqXHR.responseJSON, jqXHR.status);
        }
      },
    });
  };
 ```

