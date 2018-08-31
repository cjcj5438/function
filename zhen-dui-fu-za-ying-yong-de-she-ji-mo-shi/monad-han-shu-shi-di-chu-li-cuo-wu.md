# Monad 从控制流到数据流误

 函子是一个容器，可以包含任何值。函子之中再包含一个函子，也是完全合法的。但是，这样就会出现多层嵌套的函子。

```text
Maybe.of(
  Maybe.of(
    Maybe.of({name: 'Mulburry', number: 8402})
  )
)
```

 上面这个函子，一共有三个`Maybe`嵌套。如果要取出内部的值，就要连续取三次`this.val`。这当然很不方便，因此就出现了 Monad 函子。



**Monad 函子的作用是，总是返回一个单层的函子。**它有一个flatMap方法，与map方法作用相同，唯一的区别是如果生成了一个嵌套函子，它会取出后者内部的值，保证返回的永远是一个单层的容器，不会出现嵌套的情况。
```javascript
class Monad extends Functor {
  join() {
    return this.val;
  }
  flatMap(f) {
    return this.map(f).join();
  }
}
```
如果函数f返回的是一个函子，那么this.map(f)就会生成一个嵌套的函子。所以，join方法保证了flatMap方法总是返回一个单层的函子。这意味着嵌套的函子会被铺平（flatten）。  

我开始也是看不懂什么是mond 借助一峰老师的blog 帮助理解 就是用来解决函数嵌套问题

# 使用Maybe Monad和Either Monad来处理异常 #
## Either函子 ##
> 条件运算if...else是最常见的运算之一，函数式编程里面，使用 Either 函子表达。
> Either 函子内部有两个值：左值（Left）和右值（Right）。右值是正常情况下使用的值，左值是右值不存在时使用的默认值。

## maybe函子 ##
这个比较好理解就像是 三元运算


