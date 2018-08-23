# 组合函数

## 函数组合：描述与求值分离

组合的概念不仅限于函数，整个程序都可以由无副作用的纯的程序或模块组合而成（基于之前的函数定义，本书将不严格区分函数、程序和模块这3个术语，它们均表示具有输入和输出的可执行单元）。

组合是一种合取操作，这意味着其元素使用逻辑与运算连接。

```text
// 查单词数
const str = `We can only see a short distance
            ahead but we can see plenty there
           that needs to be done`;
const explode = (str) => str.split(/\s+/);
const count = (arr) => arr.length;
function compose() {
  let args = arguments;
  let start = args.length - 1;
  return function () {
    let i = start;
    let result = args[start].apply(this, arguments);
    while (i--) {
      result = args[i].call(this, result)
    }
    return result;
  }
}
const countWords = compose(count, explode);
console.log(countWords(str));//19
```

## 函数式库的组合

```text
const students = ['Rosser', 'Turing', 'Kleene', 'Church'];
const grades   = [80, 100, 90, 99];

const smartestStudent = R.compose(
    R.head,
    R.pluck(0),
    R.reverse,
    R.sortBy(R.prop(1)),
    R.zip);//用 Ramda 的一系列函数组合成新函数smartestStudent
```

 组合的多个柯里化函数构成，每个函数都能够以特定的方式对数据进行变换

* `R.zip`——通过配对两个数组的内容来创建一个新数组。本例中，配对两个数组能够得到`[['Rosser', 80], ['Turing', 100], ...]`。
* `R.prop`——指定在排序中要使用的值。本例中，以 1 作为参数指明使用子数组的第二个元素（成绩）。
* `R.sortBy`——通过给定的属性执行数组的自然升序排序。
* `R.reverse`——反转整个数组以获得第一个元素的最高数字。
* `R.pluck`——通过抽取指定索引处的元素构建数组。传递参数 0 表示提取元素为学生姓名。
* `R.head`——获取第一个元素。

## 应对纯的代码和不纯的代码

 并不需要总保证100％的纯函数以获得函数式编程的好处。理想情况下，开发者需要尽可能地分离纯的行为与不纯的行为，而且最好是在同一个函数中。

## point-free编程

[ Pointfree](http://www.ruanyifeng.com/blog/2017/03/pointfree.html) 的本质就是使用一些通用的函数，组合出各种复杂运算。上层运算不要直接操作数据，而是通过底层函数去处理。这就要求，将一些常用的操作封装成函数。

