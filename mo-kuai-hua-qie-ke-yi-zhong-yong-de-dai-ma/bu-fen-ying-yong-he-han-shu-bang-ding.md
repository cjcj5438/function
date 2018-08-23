# 部分应用和函数绑定

## 核心语言扩展

 在增强语言的表现力方面，部分应用可用于扩展如 `String` 或 `Number` 这样的核心数据类型的实用功能。注意，如果语言中加入了可造成冲突的新方法，以这种方式扩展语言可能会使代码很难在平台升级的过程中移植。

```text

String.prototype.first = _.partial(String.prototype.substring, 0, _);
//使用占位符，可以部分应用 substring一个参数 0，从而创建期待一个偏移量参数的函数
//------------------------------------------------------------------------
[_.partial]
 官方实例  // 创建一个函数。 该函数调用 func，并传入预设的 partials 参数。 
这个方法类似 _.bind，除了它不会绑定 this。 
var greet = function(greeting, name) {
  return greeting + ' ' + name;
};
 
var sayHelloTo = _.partial(greet, 'hello');
sayHelloTo('fred');
// => 'hello fred'
 
// 使用了占位符。
var greetFred = _.partial(greet, _, 'fred');
greetFred('hi');
// => 'hi fred'
```

## 延迟函数绑定

 当期望目标函数使用某个所属对象来执行时，使用函数绑定来设置上下文对象就变得尤为重要。例如，浏览器中的`setTimeout`和`setInterval`等函数，如果不将`this`的引用设为全局上下文，即`window`对象，是不能正常工作的。传递`undefined`在运行时正确设置它们的上下文。例如，`setTimeout`可用于创建一个简单的调度对象来执行延迟的任务。以下是使用`_.bind` 和`_.partial`的示例：

```text
const Scheduler = (function () {
    const delayedFn = _.bind(setTimeout, undefined, _, _);
　
    return {
      delay5:  _.partial(delayedFn, _, 5000),
      delay10: _.partial(delayedFn, _, 10000),
      delay:   _.partial(delayedFn, _, _)
    };
})();
　
Scheduler.delay5(function () {
   consoleLog('Executing After 5 seconds!')
});

[_.bind]
// 创建一个调用func的函数，thisArg绑定func函数中的 this ，并且func函数会接收partials附加参数。
// 默认是以 _ 作为附加部分参数的占位符。 

var greet = function(greeting, punctuation) {
  return greeting + ' ' + this.user + punctuation;
};
 
var object = { 'user': 'fred' };
 
var bound = _.bind(greet, object, 'hi');
bound('!');
// => 'hi fred!'
 
// Bound with placeholders.
var bound = _.bind(greet, object, _, '!');
bound('hi');
// => 'hi fred!'
```



