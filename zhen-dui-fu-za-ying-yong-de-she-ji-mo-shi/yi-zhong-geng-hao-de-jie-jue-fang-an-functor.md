---
description: 函数式以一种完全不同的方法应对软件系统的错误处理。其思想说起来也非常简单，就是创建一个安全的容器，来存放危险代码
---

# 更好的解决方案Functor

## 包裹不安全的值

 将值包裹起来是函数式编程的一个基本设计模式，因为它直接地保证了值不会被任意篡改。这有点像给值身披铠甲，只能通过`map`操作来访问该容器中的值。

```text
class Wrapper {
  constructor(value) { //⇽--- 存储任意类型值的简单类型
    this._value = value;
  }

  // map :: (A -> B) -> A -> B
  map(f) { //⇽--- 用一个函数来 map 该类型（就像数组一样）
    return f(this.val);
  };

  toString() {
    return 'Wrapper (' + this.value + ')';
  }
}
const wrappedValue = wrap('Get Functional');
wrappedValue.map(R.identity); //-> 'Get Functional' ⇽--- 值的提取

wrappedValue.map(log);
wrappedValue.map(R.toUpper); //-> 'GET FUNCTIONAL' ⇽--- 对内部值应用函数

const wrappedNull = wrap(null);// 还是会出现null值
wrappedNull.map(doWork);//doWork 被赋予了空值检查的责任

//由于直接调用函数，完全可以交给Wrapper类型来做错误处理。换句话说，可以在调用函数之前，检查null、空字符串或者负数，等等。因此，Wrapper.map的语义就由具体的Wrapper类型来确定。
// fmap :: (A -> B) -> Wrapper[A] -> Wrapper[B]
Wrapper.prototype.fmap = function (f) {
  return wrap(f(this.val)); //先将返回值包裹到容器中，再返回给调用者
};
//fmap知道如何在上下文中应用函数值。它会先打开该容器，应用函数到值，最后把返回的值包裹到一个新的同类型容器中。拥有这种函数的类型称为Functor。

```

## Functor

### 定义

 函数不仅可以用于同一个范畴之中值的转换，还可以用于将一个范畴转成另一个范畴。这就涉及到了函子（Functor）。

{% hint style="info" %}
#### 函子的概念:

**`函子是函数式编程里面最重要的数据类型，也是基本的运算单位和功能单位`**。它首先是一种范畴，也就是说，是一个容器，包含了值和变形关系。比较特殊的是，它的变形关系可以依次作用于每一个值，将当前容器变形成另一个容器。
{% endhint %}

### 代码实现

 任何具有`map`方法的数据结构，都可以当作函子的实现

```text

class Functor {
  constructor(val) { 
    this.val = val; 
  }

  map(f) {
    return new Functor(f(this.val));
  }
}
```

 上面代码中，`Functor`是一个函子，它的`map`方法接受函数`f`作为参数，然后返回一个新的函子，里面包含的值是被`f`处理过的（`f(this.val)`）。

{% hint style="warning" %}
 **一般约定，函子的标志就是容器具有`map`方法。该方法将容器里面的每一个值，映射到另一个容器。**
{% endhint %}

```text
(new Functor(2)).map(function (two) {
  return two + 2;
});
// Functor(4)

(new Functor('flamethrowers')).map(function(s) {
  return s.toUpperCase();
});
// Functor('FLAMETHROWERS')

(new Functor('bombs')).map(_.concat(' away')).map(_.prop('length'));
// Functor(10)
```

> 上面的例子说明，函数式编程里面的运算，都是通过函子完成，即运算不直接针对值，而是针对这个值的容器----函子。函子本身具有对外接口（`map`方法），各种函数就是运算符，通过接口接入容器，引发容器里面的值的变形。
>
> 因此，**学习函数式编程，实际上就是学习函子的各种运算。**由于可以把运算方法封装在函子里面，所以又衍生出各种不同类型的函子，有多少种运算，就有多少种函子。函数式编程就变成了运用不同的函子，解决实际问题。

