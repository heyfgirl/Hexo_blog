---
title: JavaScript 的深复制
date: 2017-05-26 15:24:59
updated: 2017-05-26 15:24:59
tags:
---
## 第三方库的实现 ##
### Underscore —— _.clone() ###
在 Underscore 中有这样一个方法：_.clone()，这个方法实际上是一种浅复制 (shallow-copy)，所有嵌套的对象和数组都是直接复制引用而并没有进行深复制。来看一下例子应该会更加直观：
```javascript
    var x = {
        a: 1,
        b: { z: 0 }
    };

    var y = _.clone(x);

    y === x       // false
    y.b === x.b   // true

    x.b.z = 100;
    y.b.z         // 100
```
让我们来看一下 Underscore 的源码：
```javascript
  // Create a (shallow-cloned) duplicate of an object.
  _.clone = function(obj) {
    if (!_.isObject(obj)) return obj;
    return _.isArray(obj) ? obj.slice() : _.extend({}, obj);
  };

```
如果目标对象是一个数组，则直接调用数组的slice()方法，否则就是用_.extend()方法。想必大家对extend()方法不会陌生，它的作用主要是将从第二个参数开始的所有对象，按键值逐个赋给第一个对象。而在 jQuery 中也有类似的方法。关于 Underscore 中的 _.extend() 方法的实现可以参考 underscore.js #L1006。

Underscore 的 clone() 不能算作深复制，但它至少比直接赋值来得“深”一些，它创建了一个新的对象。另外，你也可以通过以下比较 tricky 的方法来完成单层嵌套的深复制：

```javascript
  var _ = require('underscore');
  var a = [{f: 1}, {f:5}, {f:10}];
  var b = _.map(a, _.clone);       // <----
  b[1].f = 55;
  console.log(JSON.stringify(a));  // [{"f":1},{"f":5},{"f":10}]
```
### jQuery —— $.clone() / $.extend() ###
在 jQuery 中也有这么一个叫 $.clone() 的方法，可是它并不是用于一般的 JS 对象的深复制，而是用于 DOM 对象。这不是这篇文章的重点，所以感兴趣的同学可以参考jQuery的文档。与 Underscore 类似，我们也是可以通过 $.extend() 方法来完成深复制。值得庆幸的是，我们在 jQuery 中可以通过添加一个参数来实现递归extend。调用$.extend(true, {}, ...)就可以实现深复制啦，参考下面的例子：

```javascript
  var x = {
      a: 1,
      b: { f: { g: 1 } },
      c: [ 1, 2, 3 ]
  };

  var y = $.extend({}, x),          //shallow copy
      z = $.extend(true, {}, x);    //deep copy

  y.b.f === x.b.f       // true
  z.b.f === x.b.f       // false

```
在 jQuery的源码 - src/core.js #L121 文件中我们可以找到$.extend()的实现，也是实现得比较简洁，而且不太依赖于 jQuery 的内置函数，稍作修改就能拿出来单独使用。

### lodash —— _.clone() / _.cloneDeep() ###
在lodash中关于复制的方法有两个，分别是_.clone()和_.cloneDeep()。其中_.clone(obj, true)等价于_.cloneDeep(obj)。使用上，lodash和前两者并没有太大的区别，但看了源码会发现，Underscore 的实现只有30行左右，而 jQuery 也不过60多行。可 lodash 中与深复制相关的代码却有上百行，这是什么道理呢？

```javascript
  var $ = require("jquery"),
    _ = require("lodash");

  var arr = new Int16Array(5),
      obj = { a: arr },
      obj2;
  arr[0] = 5;
  arr[1] = 6;

  // 1. jQuery
  obj2 = $.extend(true, {}, obj);
  console.log(obj2.a);                            // [5, 6, 0, 0, 0]
  Object.prototype.toString.call(obj2);           // [object Int16Array]
  obj2.a[0] = 100;
  console.log(obj);                               // [100, 6, 0, 0, 0]

  //此处jQuery不能正确处理Int16Array的深复制！！！

  // 2. lodash
  obj2 = _.cloneDeep(obj);                       
  console.log(obj2.a);                            // [5, 6, 0, 0, 0]
  Object.prototype.toString.call(arr2);           // [object Int16Array]
  obj2.a[0] = 100;
  console.log(obj);                               // [5, 6, 0, 0, 0]
```
通过上面这个例子可以初见端倪，jQuery 无法正确深复制 JSON 对象以外的对象，而我们可以从下面这段代码片段可以看出 lodash 花了大量的代码来实现 ES6 引入的大量新的标准对象。更厉害的是，lodash 针对存在环的对象的处理也是非常出色的。因此相较而言，lodash 在深复制上的行为反馈比前两个库好很多，是更拥抱未来的一个第三方库。
```javascript
  /** `Object#toString` result references. */
  var argsTag = '[object Arguments]',
      arrayTag = '[object Array]',
      boolTag = '[object Boolean]',
      dateTag = '[object Date]',
      errorTag = '[object Error]',
      funcTag = '[object Function]',
      mapTag = '[object Map]',
      numberTag = '[object Number]',
      objectTag = '[object Object]',
      regexpTag = '[object RegExp]',
      setTag = '[object Set]',
      stringTag = '[object String]',
      weakMapTag = '[object WeakMap]';

  var arrayBufferTag = '[object ArrayBuffer]',
      float32Tag = '[object Float32Array]',
      float64Tag = '[object Float64Array]',
      int8Tag = '[object Int8Array]',
      int16Tag = '[object Int16Array]',
      int32Tag = '[object Int32Array]',
      uint8Tag = '[object Uint8Array]',
      uint8ClampedTag = '[object Uint8ClampedArray]',
      uint16Tag = '[object Uint16Array]',
      uint32Tag = '[object Uint32Array]';
```
## 借助 JSON 全局对象 ##
相比于上面介绍的三个库的做法，针对纯 JSON 数据对象的深复制，使用 JSON 全局对象的 parse 和 stringify 方法来实现深复制也算是一个简单讨巧的方法。然而使用这种方法会有一些隐藏的坑，它能正确处理的对象只有 Number, String, Boolean, Array, 扁平对象，即那些能够被 json 直接表示的数据结构。
```javascript
function jsonClone(obj) {
    return JSON.parse(JSON.stringify(obj));
}
var clone = jsonClone({ a:1 });
```