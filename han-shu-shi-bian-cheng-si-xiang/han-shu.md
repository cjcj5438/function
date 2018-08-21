# 函数

## 一等函数

在JavaScript中，术语是**一等的**，指的在语言层面将函数视为真实的对象。

```text
var multiplier = new Function('a', 'b', 'return a * b');
multiplier(2, 3); //-> 6
 // 我平时很少使用这个函数, 反正函数在js 里面就是一等的
```

```text
[1, 10, 21, 2].sort((p1, p2) => p1.getAge() - p2.getAge());
这种是一种高阶函数
可以接收其他函数作为参数的JavaScript函数，均属于一种函数类型——高阶函数
```

## 高阶函数

首先来看两个简单的高阶函数

```text
function applyOperation(a, b, opt) { ⇽--- opt()函数可以作为参数传入其他函数中
   return opt(a,b);
}
var multiplier = (a, b) => a * b;
applyOperation(2, 3, multiplier); // -> 6
```

```text
function add(a) {
  return function (b) { ⇽---一个返回其他函数的函数
      return a + b;
  }
}
add(3)(3); //-> 6
```

看一个实例

```text
// 假设需要打印住在美国的人员名单。一开始的实现很可能是这样的命令式代码：
function printPeopleInTheUs(people) {
   for (let i = 0; i < people.length; i++) {
     var thisPerson = people[i];
     if(thisPerson.address.country === 'US') {
       console.log(thisPerson);  ⇽---隐式调用对象的toString方法
    }
 }
}
printPeopleInTheUs([p1, p2, p3]);  ⇽--- p1、p2和p3 是Person的实例

//现在假设还需要支持打印生活在其他国家的人。通过高阶函数，我们可以很好地抽象出应用于每个人的操作，
//这里就是控制台的打印逻辑。可以给高阶函数 printPeople 提供任何 action 函数：

function printPeople(people, action) {
   for (let i = 0; i < people.length; i++) {
      action (people[i]);
   }
}
var action = function (person) {
   if(person.address.country === 'US') {
      console.log(person);
   }
}
printPeople(people,action);

//avaScript 语言中显著的命名模式之一是使用如 multiplier、comparator 以及 action 这样的受事名词。
//这也是因为这些函数是一等的，可以给变量赋值，并在之后再执行。基于函数的高阶特性将 printPeople 重构一下：

function printPeople(people, selector, printer) {
   people.forEach(function (person) { ⇽--- forEach是函数式推荐的循环方式。本章之后的部分讨论这个话题
      if(selector(person)) {
         printer(person);
      }
   });
}
var inUs = person => person.address.country === 'US';
printPeople(people, inUs, console.log);  ⇽---通过使用高阶函数，开始呈现出声明式的模式。
表达式清晰地描述了程序需要做的事 它需要一个完全拥抱函数式编程的心态。
而从上例可以看出，这段代码可以变得比一开始灵活得多，因为现在可以轻松地改变选择条件以及打印的方式。
```

扩展: 我们用ramad.js 来实战一下

```text
var countryPath = ['address', 'country'];

var countryL = R.lens(R.path(countryPath), R.assocPath(countryPath));
var inCountry = R.curry((country, person) =>
        R.equals(R.view(countryL, person), country));
//这样的代码比之前的更加函数式了：

people.filter(inCountry('US')).map(console.log);
```

## 函数调用类型

this太基础略过

## 函数方法

JavaScript 支持通过使用函数原型链上的函数方法（类似元函数）`call` 和 `apply` 来调用函数本身。

```text
/*写一个 negate 函数*/

function negate(func) { ⇽---高阶函数negate接收一个函数作为输入，并返回取反其结果的函数
   return function() {
      return !func.apply(null, arguments);  ⇽---使用fun.apply()来使用原来的参数调用函数
   };
}
function isNull(val) { ⇽---定义isNull函数
   return val === null;
}
var isNotNull = negate(isNull);  ⇽---定义isNull函数的反，即isNotNull函数
isNotNull(null); //-> false
isNotNull({});   //-> true
```

