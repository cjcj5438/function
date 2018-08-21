---
description: 函数的返回类型必须与接收函数的参数类型相匹配。接收函数必须声明至少一个参数才能处理上一个函数的返回值。
---

# 管道函数的兼容条件

## 管道函数的兼容条件

> 正式地讲，仅当`f`的输出类型等同于函数`g`的输入时，两个函数`f`和`g`是类型兼容的。举例来说，一个处理学生社会安全号码的简单程序

```text
// trim :: String -> String
const trim = (str) => str.replace(/^\s*|\s*$/g, '');

// normalize :: String -> String
const normalize = (str) => str.replace(/\-/g, '');

normalize(trim(' 444-44-4444 ')); 
//-> '444444444' ⇽--- 手动构建系列管道调用两个函数（之后会涉及如何自动化这一过程）。
// 使用带有首末空白符的输入测试
```

## 函数与元数：元组的应用

```text

const Tuple = function (/* types */) {
  const typeInfo = Array.prototype.slice.call(arguments, 0); //⇽--- 读取参数作为元组的元素类型
  const _T = function (/* values */) { //⇽--- 声明内部类型_T，以保障类型与值匹配
    const values = Array.prototype.slice.call(arguments, 0); //⇽--- 提取参数作为元组内的值

    function checkType(index){
      return function (v) {
        return v
      }
    }
    
    if (values.some((val) => //⇽--- 检查非空值。函数式数据类型不允许空值
      val === null || val === undefined)) {
      throw new ReferenceError('Tuples may not have any null values');
    }
    //这步是外部传进来的参数和内置函数的参数是否匹配, 如果是正确的那么参数格式正确,如果不正确的话,那么参数传错了
    if (values.length !== typeInfo.length) { //⇽--- 按照定义类型的个数检查元组的元数
      throw new TypeError('Tuple arity does notmatch its prototype');
    }
    //⇽--- 使用 checkType 检查每一个值都能匹配其类型定义。其中的元素都可以通过_n 获取， n 为元素的索引（注意是从 1 开始）
    values.map(function (val, index) {
      console.log(val,index);
      // todo:checkType函数还没有优化
      this['_' + (index + 1)] = checkType(typeInfo[index])(val);
    }, this);
    Object.freeze(this); //⇽--- 让元组实例不可变
  };
  _T.prototype.values = function () { //⇽--- 提取元组中的元素，也可以使用 ES6 的解构赋值把元素赋值到变量上
    return Object.keys(this).map(function (k) {
      return this[k];
    }, this);
  };
  return _T;
};

const Status = Tuple(Boolean, String);

const trim = (str) => str.replace(/^\s*|\s*$/g, '');
// normalize :: String -> String
const normalize = (str) => str.replace(/\-/g, '');
// isValid :: String -> Status
const isValid = function (str) {
  if(str.length === 0){
    return new Status(false,// ⇽--- 声明包含状态（ Boolean）和消息（ String）的类型Status
      'Invald input. Expected non-empty value!');
  }
  else {
    return new Status(true, 'Success!');
  }
}
isValid(normalize(trim('444-44-4444')));

/*
* Array.prototype.slice.call(arguments, 0); 能将具有length属性的对象转成数组
* Array.prototype.some(()=>{}) 测试数组中哪些可以元素可以通过毁掉函数
* */

```

