# 什么是函数式编程

## 函数式编程是声明式编程

 函数式编程属于**声明式**编程范式

## 副作用带来的问题和纯函数

### 纯函数的性质:

* 仅取决于提供的输入，而不依赖于任何在函数求值期间或调用间隔时可能变化的隐藏状态和外部状态。
* 不会造成超出其作用域的变化，例如修改全局对象或引用传递的参数。\

{% hint style="info" %}
 够确保一个函数有相同的返回值是一个优点，它使得函数的结果是一致的和可预测的。这是纯函数的一个特质，称为**引用透明**。
{% endhint %}

## 引用透明和可置换性

{% hint style="info" %}
 引用透明是定义一个纯函数较为正确的方式。**纯度**在这个意义上表明一个函数的参数和返回值之间映射的纯的关系。因此，如果一个函数对于相同的输入始终产生相同的结果，那么就说它是**引用透明**的。
{% endhint %}

```text
// 有状态的函数increment不是引用透明的，因为其返回值严重依赖外部变量counter
var counter = 0;
function increment() {
    return ++counter;
}
```

```text
// 现在这个函数是稳定的，对于相同的输入每次都返回相同的输出结果。
var increment = counter => counter + 1;
```

## 存储不可变数据

 **不可变数据**是指那些被创建后不能更改的数据。与许多其他语言一样，JavaScript中的所有基本类型（`String`、`Number`等）从本质上是不可变的。

```text
var sortDesc = function (arr) {
  return arr.sort(function (a, b) {
     return b - a;
  });
}
var arr = [1,2,3,4,5,6,7,8,9];
sortDesc(arr); //-> [9,8,7,6,5,4,3,2,1]
/* 
乍一眼看去，这段代码看起来完全正常，并没有副作用
不幸的是，array.sort 函数是有状态的，会导致在排序过程中产生副作用，因为原始的引用被修改了。
这是语言的一个缺陷
*/
```

\*\*\*\*

**简单的概括下 "函数式编程"**

**函数式编程是指为创建不可变的程序，通过消除外部可见的副作用，来对纯函数的声明式的求值过程。**

**代码学习**

```text
var run2 = function(f, g) {
    return function(x) {
        return f(g(x));
    };
};

// -run- with three functions
var run3 = function(f, g, h) {
    return function(x) {
          return f(g(h(x))); 
    };
};

// Test this magical function
var add1 = function(x) {return x + 1;};
var mult2 = function(x) {return x * 2;};
var square = function(x) {return x * x;};
var negate = function(x) {return -x;};

var double = run2(add1, add1);
console.log(double(2)); //-> 4

var testRun = run3(negate, square, mult2);
console.log(testRun(2)); //->-16
```

```text
var compose = (...fns) => fns.reduce((f, g) => (...args) => f(g(...args)))
// usege:
compose(fn3, fn2, fn1);
```

