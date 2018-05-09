---
title: 对log4.js做格式化输出
date: 2018-05-08 14:51:00
categories: node.js
tags:
  - node.js
---

#### 开发背景
在公司的devops平台构建中，日志作为重要的一环，将系统的应用日志通过logdash接入第三方监控平台，再接入即时通信平台，从而实现对运行系统的实时监控。之前系统的日志使用的log4并对log4的输出文本做了改写。现在要按照一定的日志标准接入运维的日志系统。日志标准，运维定的是json格式,如下:


参数名 | 说明 | 示例
---|--- | --
cc_timestamp | 时间戳，精确到毫秒 | 1522317520698
message | 日志内容	| Creating order, order no. 1234567890
level | 日志等级，DEBUG, WARNING, INFO, ERROR | INFO
app | 应用名称，用于区分不同应用 | usermaster

安照表中的内容对log进行格式化输出，项目使用的log4.js这个日志包，查了下文档，这个无法支持定制化的日志展示，查了很资料，都无法满足这一需要，有一个bunyan-extend的库，主要用来做json化日志输出的，但这个并不能满足定制化的key的要求，另外，项目又对log4.js做了进一步的包装，为了减少不必要麻烦，只能自己动手写轮子了。改写策略，要保证，不修改现有的log体系，只对log4的输出做格式化处理。

#### log4.js源码分析
阅读了下log4.js的源码，核心代码不是太复杂，主要逻辑流程:

1. 通过getLogger实例化一个logger对象
2. 通过appenders加载日志输出的事件处理函数
3. 通过事件监听，触发appenders事件处理函数

```
// 核心代码
appenderList = appenders[ALL_CATEGORIES];
appenderList.forEach(function(appender) {
  loggers[loggerCategoryName].addListener("log", appender);
});
```

现在的问题，就是针对appender进行改造了,appender的结构如下：
```
function(loggingEvent) {
    consoleLog(layout(loggingEvent, timezoneOffset));
  };
```

现在的所有的问题，都集中到layout上了，只要对layout做定制化处理，就达到我们最终的目标。
对layout的改造最终形式为
```
var obj = {
  app: 'itcloudlab',
  level: loggingEvent.level.levelStr,
  cc_timestamp: loggingEvent.startTime.getTime(),
  message: loggingEvent.data
}
var str = JSON.stringify(obj);
```

以上，代码的清晰思路已经出来了，因为，时间比较紧张，真对改问题，我的处理思路是对layout做死值配置，而且，修改了log4的输入方式。

#### 对log4的扩展
这样的处理，基本满足了项目的需要，但是这种写死程序的方式，很不值得推荐，程序要有足够的健壮性。在此，理一下扩展性。抽个时间，把代码完善一下，希望可以merge到log4.js这个开源库中。

log4.js的配置文件，是通过appenders进行配置的，通过它作为扩展的入口，添加log输出类型,type－> json，再，添加可以item选项,{name:"itcloudlab", "msg": "日志内容", "time": 时间, "levle": info }，如果，存在就覆盖原有的key。

思路很简单，思路有了，具体的代码实现，等有空了，好好的理一下逻辑，再做完善。




