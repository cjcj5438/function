# 柯里化的函数求值

{% hint style="info" %}
柯里化概念: 

柯里化是一种在所有参数被提供之前，挂起或“延迟”函数执行，将多参函数转换为一元函数序列的技术。
{% endhint %}

```text
// 创建一个log4的函数模板
const logger = function(appender, layout, name, level, message) {
    const appenders = { ⇽--- 预设 appenders
        'alert': new Log4js.JSAlertAppender(),
        'console': new Log4js.BrowserConsoleAppender()
    };
    const layouts = { ⇽--- 预设布局layouts
        'basic': new Log4js.BasicLayout(),
        'json': new Log4js.JSONLayout(),
        'xml' : new Log4js.XMLLayout()
    };
    const appender = appenders[appender];
    appender.setLayout(layouts[layout]);
    const logger = new Log4js.getLogger(name);
    logger.addAppender(appender);
    logger.log(level, message, null); ⇽--- 使用配置好的logger 打印消息
};
```

