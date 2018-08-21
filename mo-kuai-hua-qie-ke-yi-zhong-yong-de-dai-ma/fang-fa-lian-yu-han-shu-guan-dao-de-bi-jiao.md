# 方法链与函数管道的比较

## 方法链接

```text
_.chain(names)
    .filter(isValid) ⇽--- 每一个“点”后只能调用 Lodash 提供的方法
    .map(s => s.replace(/_/, ' '))
    .uniq()
    .map(_.startCase)
    .sort()
    .value();
```

{% hint style="success" %}
 比较命令式代码，这的确是一个能够极大提高代码可读性的语法改进。然而，它与方法所属的对象紧紧地耦合在一起，限制链中可以使用的方法数量，也就限制了代码的表现力。这样就只能够使用由Lodash提供的操作，而无法轻松地将不同函数库的（或自定义的）函数连接在一起。
{% endhint %}

#### 函数的管道化

![&#x51FD;&#x6570;&#x7BA1;&#x9053;&#x59CB;&#x4E8E;&#x5177;&#x6709;&#x7C7B;&#x578B;A&#x53C2;&#x6570;&#x7684;&#x51FD;&#x6570;f&#xFF0C;&#x4EA7;&#x751F;&#x4E00;&#x4E2A;&#x7C7B;&#x578B;B&#x7684;&#x5BF9;&#x8C61;&#xFF0C; &#x968F;&#x540E;&#x6309;&#x5E8F;&#x4F20;&#x5165;&#x51FD;&#x6570;g&#xFF0C;&#x5E76;&#x4EE5;&#x8F93;&#x51FA;&#x7684;&#x7C7B;&#x578B;C&#x5BF9;&#x8C61;&#x4F5C;&#x4E3A;&#x6700;&#x7EC8;&#x7ED3;&#x679C;&#x3002;&#x51FD;&#x6570; f&#x548C;g&#x65E2;&#x53EF;&#x4EE5;&#x6765;&#x81EA;&#x4E8E;&#x4EFB;&#x4F55;&#x51FD;&#x6570;&#x5E93;&#xFF0C;&#x4E5F;&#x53EF;&#x4EE5;&#x662F;&#x81EA;&#x5B9A;&#x4E49;&#x7684;&#x51FD;&#x6570;](../.gitbook/assets/1805bac030eac62039c8-original-image28.png)



