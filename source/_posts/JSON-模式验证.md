---
title: JSON 模式验证
date: 2018-05-18 16:47:34
tags:
---
#### 后端接口参数校验
在API开发中，我们需要对数据的格式做检查，我们的思路，有两种，一种，在程序中通过条件语句进行判断，二，在切面对数据整体做拦截检查，第二种处理思想要高级些，也是我们常用的处理方法。那么怎么实现呢？
目前的项目中集成了一个object-check的库，通过在yaml或json文件中配置数据的Schema，然后在路由层中加载这个Schema

例如:
```
var bodyChecker = require('object-checker').bodyCheckMiddleware;

router.post('/api/providers/my-apps/do/add',
  authMid.requireSignIn(),
  bodyChecker(BODY_SCHEMA.app.add),
  appsAPICtrl.addApp
);

#yaml schema
app:
  add:
    newData:
      name:
        $minLength: 1
        $maxLength: 512
```
这是我们项目中现在的处理思想，但是对于错误输入使用的是统一的错误输出模版。在来看下一个通用的处理方式,参考JSON Schema标准。

#### ajv的处理方式
例子：
```
const Ajv = require('ajv');

let schema = {
  type: 'object',
  required: ['username', 'email', 'password'],
  properties: {
    username: {
      type: 'string',
      minLength: 4
    },
    email: {
      type: 'string',
      format: 'email'
    },
    password: {
      type: 'string',
      minLength: 6
    },
    age: {
      type: 'integer',
      minimum: 0
    },
    sex: {
      enum: ['boy', 'girl', 'secret'],
      default: 'secret'
    },
  }
};

let ajv = new Ajv();
let validate = ajv.compile(schema);

let valid = validate(data);
if (!valid) console.log(validate.errors);
```

以上处理方式发现报错都是统一，怎么实现个性化错误输出呢？
引入一个包ajv-errors，在scheme中，添加errorMessage这个属性，
用法示例：
```
var schema = {
  type: 'object',
  required: ['foo', 'bar'],
  properties: {
    foo: { type: 'integer' },
    bar: { type: 'string' }
  },
  errorMessage: {
    type: 'should be an object', // will not replace internal "type" error for the property "foo"
    required: {
      foo: 'should have an integer property "foo"',
      bar: 'should have a string property "bar"'
    }
  }
};
```

