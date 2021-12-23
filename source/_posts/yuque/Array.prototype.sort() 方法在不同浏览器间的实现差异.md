---
title: Array.prototype.sort() 方法在不同浏览器间的实现差异
urlname: kpad2s
date: '2021-12-23 10:44:51 +0800'
tags: []
categories: []
---

Array.prototype.sort() 方法用原地算法对数组的元素进行排序，并返回数组。默认排序顺序是在将元素转换为字符串，然后比较它们的 UTF-16 代码单元值序列时构建的

由于它取决于具体实现，因此无法保证排序的时间和空间复杂性。

## 语法

```javascript
arr.sort([compareFunction]);
```

### 参数

compareFunction 可选
用来指定按某种顺序进行排列的函数。如果省略，元素按照转换为的字符串的各个字符的 Unicode 位点进行排序。
firstEl
第一个用于比较的元素。
secondEl
第二个用于比较的元素。
返回值
排序后的数组。请注意，数组已原地排序，并且不进行复制。

### 描述

如果没有指明 compareFunction ，那么元素会按照转换为的字符串的诸个字符的 Unicode 位点进行排序。例如 "Banana" 会被排列到 "cherry" 之前。当数字按由小到大排序时，9 出现在 80 之前，但因为（没有指明 compareFunction），比较的数字会先被转换为字符串，所以在 Unicode 顺序上 "80" 要比 "9" 要靠前。

如果指明了 compareFunction ，那么数组会按照调用该函数的返回值排序。即 a 和 b 是两个将要被比较的元素：

- 如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前；
- 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。备注： ECMAScript 标准并不保证这一行为，而且也不是所有浏览器都会遵守（例如 Mozilla 在 2003 年之前的版本）；
- 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前。
  compareFunction(a, b) 必须总是对相同的输入返回相同的比较结果，否则排序的结果将是不确定的。
  所以，比较函数格式如下：

```javascript
function compare(a, b) {
  if (a < b) {
    // 按某种排序标准进行比较, a 小于 b
    return -1;
  }
  if (a > b) {
    return 1;
  }
  // a must be equal to b
  return 0;
}
```

以上是 MDN 上面的说明, 但至于各浏览器的具体的实现是没有的.

## 浏览器对 compareFunction 参数处理的差异

```javascript
var numbers = [4, 2, 5, 1, 3];
numbers.sort(function (a, b) {
  return a - b;
});
console.log(numbers);
```

直观感觉上 compareFunction 的 a, b 参数应该是, a === numbers[n], b === numbers[n + 1], 但实际上不同浏览器的处理方式却不同, 以下是 Chrome 和 Firefox 的处理结果:
Chrome

```javascript
[1, 2, 3, 4, 5].sort((a, b) => {
  console.log(a, b);
});

// 2 1
// 3 2
// 4 3
// 5 4
```

Firefox

```javascript
[1, 2, 3, 4, 5].sort((a, b) => {
  console.log(a, b);
});

// 1 2
// 2 3
// 3 4
// 4 5
```

由上面结果可以看到, Chrome 和 Firefox compareFunction 的入参方式完全相反.

这是因为 ES 标准 没有规范 compareFunction 传入的参数到底该是什么顺序和算法，只是制定 compareFunction 两数之间的比较规则, 就是上面提到的：

- 如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前；
- 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。
- 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前。

明确定义规则后，不管 a, b 两个数传进来的顺序如何, 只在乎它们两数的排序关系。这就解释了为什么同样的 compareFunction 下，尽管不同浏览器接受的参数不一样，还是能排序出一样的结果。

参考资料:
[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/sort](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
[https://tc39.es/ecma262/#sec-array.prototype.sort](https://tc39.es/ecma262/#sec-array.prototype.sort)
