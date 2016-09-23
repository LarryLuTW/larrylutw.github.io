---
layout: post
title: '[Javascript] 理解 JS 中的淺拷貝和深拷貝'
keywords: js, javascript, object, assign, immutable, reference, copy, deepclone, clonedeep
published: true
---

因為前幾天在寫專案的時候要用到深拷貝<br>
所以就去查了一些相關的資料<br>
趁這個機會順便做點筆記<br>

---

## 基本型別(Primitive Type) VS 物件(Object)

<img src="http://i.imgur.com/0ZeYRHQ.png" width="600">

在 JS 中有一些基本型別像是`Number`、`String`、`Boolean`<br>
而物件就是像這樣的東西`{ name: 'Larry', skill: 'Node.js' }`<br>
物件跟基本型別最大的不同就在於他們的傳值方式<br>

---

基本型別是傳 value，像是這樣<br>

```js
var a = 10;
var b = a;
b = 20;

console.log(a); // 10
console.log(b); // 20
```

在修改`a`時並不會改到`b`<br>

---

但物件就不同，物件傳的是 reference<br>

```js
var obj1 = { a: 10, b: 20, c: 30 };
var obj2 = obj1;
obj2.b = 100;

console.log(obj1); // { a: 10, b: 100, c: 30 } <-- b 被改到了
console.log(obj2); // { a: 10, b: 100, c: 30 }
```

複製一份`obj1`叫做`obj2`<br>
然後把`obj2.b`改成`100`<br>
但卻不小心改到`obj1.b`<br>
因為他們根本是同一個物件<br>
這就是所謂的淺拷貝<br>

---

要避免這樣的錯誤發生就要寫成這樣<br>

```js
var obj1 = { a: 10, b: 20, c: 30 };
var obj2 = { a: obj1.a, b: obj1.b, c: obj1.c };
obj2.b = 100;

console.log(obj1); // { a: 10, b: 20, c: 30 } <-- b 沒被改到
console.log(obj2); // { a: 10, b: 100, c: 30 }
```

這樣就是深拷貝<br>
不會改到原本的`obj1`<br>

---

## 淺拷貝(Shallow Copy) VS 深拷貝(Deep Copy)

<img src="http://i.imgur.com/abBTAP3.png" width="480">

淺拷貝只複製指向某個物件的指標<br>
而不複製物件本身<br>
新舊物件還是共用同一塊記憶體<br>
<br>
但深拷貝會另外創造一個一模一樣的物件<br>
新物件跟原物件不共用記憶體<br>
修改新物件不會改到原物件<br>

---

## 如何做到 Deep Copy

要完全複製又不能修改到原物件<br>
這時候就要用 Deep Copy<br>
這裡會介紹幾種 Deep Copy 的方式<br>

### 自己手動複製

像上面的範例<br>
把`obj1`的屬性一個個複製到`obj2`中<br>

```js
var obj1 = { a: 10, b: 20, c: 30 };
var obj2 = { a: obj1.a, b: obj1.b, c: obj1.c };
obj2.b = 100;

console.log(obj1); // { a: 10, b: 20, c: 30 } <-- 沒被改到
console.log(obj2); // { a: 10, b: 100, c: 30 }
```

但這樣很麻煩要自己慢慢複製<br>
而且這樣其實不是 Deep Copy<br>
如果像下面這個狀況<br>

```js
var obj1 = { body: { a: 10 } };
var obj2 = { body: obj1.body };
obj2.body.a = 20;

console.log(obj1); // { body: { a: 20 } } <-- 被改到了
console.log(obj2); // { body: { a: 20 } }
console.log(obj1 === obj2); // false
console.log(obj1.body === obj2.body); // true
```

雖然`obj1`跟`obj2`是不同物件<br>
但他們會共用同一個`obj1.body`<br>
所以修改`obj2.body.a`時也會修改到舊的<br>

---

### Object.assign

`Object.assign`是 ES6 的新函式<br>
可以幫助我們達成跟上面一樣的功能<br>

```js
var obj1 = { a: 10, b: 20, c: 30 };
var obj2 = Object.assign({}, obj1);
obj2.b = 100;

console.log(obj1); // { a: 10, b: 20, c: 30 } <-- 沒被改到
console.log(obj2); // { a: 10, b: 100, c: 30 }
```

`Object.assign({}, obj1)`的意思是先建立一個空物件`{}`<br>
接著把`obj1`中所有的屬性複製過去<br>
所以`obj2`會長得跟`obj1`一樣<br>
這時候再修改`obj2.b`也不會影響`obj1`<br>
<br>
因為`Object.assign`跟我們手動複製的效果相同<br>
所以一樣只能處理深度只有一層的物件<br>
沒辦法做到真正的 Deep Copy<br>
不過如果要複製的物件只有一層的話可以考慮使用他<br>

---

### 轉成 JSON 再轉回來

用`JSON.stringify`把物件轉成字串<br>
再用`JSON.parse`把字串轉成新的物件<br>

```js
var obj1 = { body: { a: 10 } };
var obj2 = JSON.parse(JSON.stringify(obj1));
obj2.body.a = 20;

console.log(obj1); // { body: { a: 10 } } <-- 沒被改到
console.log(obj2); // { body: { a: 20 } }
console.log(obj1 === obj2); // false
console.log(obj1.body === obj2.body); // false
```

這樣做是真正的 Deep Copy<br>
但只有可以轉成`JSON`格式的物件才可以這樣用<br>
像`function`沒辦法轉成`JSON`<br>

```js
var obj1 = { fun: function(){ console.log(123) } };
var obj2 = JSON.parse(JSON.stringify(obj1));

console.log(typeof obj1.fun); // 'function'
console.log(typeof obj2.fun); // 'undefined' <-- 沒複製
```

要複製的`function`會直接消失<br>
所以這個方法只能用在單純只有資料的物件<br>

---

### jquery

相信大家應該都很熟悉 jquery 這個 library<br>
jquery 有提供一個`$.extend`可以用來做 Deep Copy<br>

```js
var $ = require('jquery');

var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};

var obj2 = $.extend(true, {}, obj1);
console.log(obj1.b.f === obj2.b.f); // false
```

---

### lodash

另外一個很熱門的函式庫 lodash<br>
也有提供`_.cloneDeep`用來做 Deep Copy<br>

```js
var _ = require('lodash');

var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};

var obj2 = _.cloneDeep(obj1);
console.log(obj1.b.f === obj2.b.f); // false
```

這是我比較推薦的方法<br>
效能還不錯<br>
使用起來也很簡單<br>

---

如果有人有更好的方法也可以跟我說<br>
我會把它補充上去讓更多人知道～<br>

