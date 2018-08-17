# 函数式编程的优点

 **宏观地了解一下函数式能为JavaScript应用程序带来的好处**

> * 促使将任务分解成简单的函数。
> * 使用流式的调用链来处理数据。
> * 通过响应式范式降低事件驱动代码的复杂性。

## 鼓励复杂任务的分解

 函数式编程实际上是分解（将程序拆分为小片段）和组合（将小片段连接到一起）之间的相互作用。 函数可以进一步分解，直到成为一个个简单的、相互独立的纯函数功能单元。  
 概念与**单一职责**原则息息相关

 组合compose。两个函数的组合是一个新的函数，它拿到一个函数的输出，并将其传递到另一个函数中。假设有两个函数 `f` 和 `g`，形式上，其组合可以如下描述：

```text
f • g = f(g(x))    
```

 由于 `compose` 接收其他函数为参数，这被称为**高阶函数**。

## 使用流式链来处理数据

 可以通过导入一些功能强大的、最优化的函数式类库来获得更多的高阶函数。在后面我会介绍很多实现于像 **Lodash.js** 和 Ramda.js 这种流行的 JavaScript 工具包中的高阶函数。尽管它们在某些方面有所重叠，但每一个都带来了独特的、可简化函数链式装配的功能。

```text
//已知道 选课数据
let enrollment = [
  {enrolled: 2, grade: 100},
  {enrolled: 2, grade: 80},
  {enrolled: 1, grade: 89}
];
```

```text
//命令式的实现可能是这样的：
var totalGrades = 0;
var totalStudentsFound = 0;
for(let i = 0; i < enrollment.length; i++) {
    let student = enrollment [i];
    if(student !== null) {
       if(student.enrolled > 1) {
          totalGrades+= student.grade;
          totalStudentsFound++;
       }
    }
}
var average = totalGrades / totalStudentsFound; //-> 90
```

与之前一样，用函数式的思维来分解这个问题，可以发现有三个主要步骤。

* * 选择合适的（选课数量大于1的）学生。
  * 获取他们的成绩。
  * 计算出他们的平均成绩。

 这样就可以用 Lodash 缝合表征这些步骤的函数，形成一个清单1.5所示的函数链（如果想知道其中每一个函数的详细说明，可以查看附录中的相应文档）。函数链是一种**惰性计算**程序，这意味着当需要时才会执行。这对程序性能是有利的，因为可以避免执行可能包含一些永不会使用的内容的整个代码序列，节省宝贵的CPU计算周期。这有效地模拟了其他函数式语言的**按需调用**的行为。

```text
_.chain(enrollment)
  .filter(student => student.enrolled > 1)
  .pluck('grade')
  .average()
  .value(); //-> 90  ⇽---调用 _.value() 会触发整个链上的所有操作
```

## 复杂异步应用中的响应

```text
var valid = false;
var elem = document.querySelector('#student-ssn');
elem.onkeyup = function(event) {
  var val = elem.value;  ⇽---访问函数作用域外的数据会产生副作用
   if(val !== null && val.length !== 0) {
      val = val.replace(/^\s*|\s*$|\-s/g, '');  ⇽---裁剪并清理输入，直接改变数据
     if(val.length === 9) {  ⇽---嵌套的分支逻辑
         console.log(`Valid SSN: ${val}!`);
         valid = true;  ⇽---访问函数作用域外的数据会产生副作用
      }
   }
   else {
      console.log(`Invalid SSN: ${val}!`);
   }
};
```

使用RX js  来实现

```text
Rx.Observable.fromEvent(document.querySelector('#student-ssn'), 'keyup')
   .map(input => input.srcElement.value)
   .filter(ssn => ssn !== null && ssn.length !== 0)
   .map(ssn => ssn.replace(/^\s*|\s*$|\-/g, ''))
   .skipWhile(ssn => ssn.length !== 9)
   .subscribe(
      validSsn => console.log(`Valid SSN ${validSsn}`)
   );
```

## 总结

* 使用纯函数的代码绝不会更改或破坏全局状态，有助于提高代码的可测试性和可维护性。 
* 函数式编程采用声明式的风格，易于推理。这提高了应用程序的整体可读性，通过使用组合和lambda表达式使代码更加精简。 
* 集合中的数据元素处理可以通过链接如map和reduce这样的函数来实现。 
* 函数式编程将函数视为积木，通过一等高阶函数来提高代码的模块化和可重用性。 
* 可以利用响应式编程组合各个函数来降低事件驱动程序的复杂性。

