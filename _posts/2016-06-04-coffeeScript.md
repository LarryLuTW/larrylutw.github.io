---
layout: post
title: '[JavaScript] CoffeeScript 基本介紹'
keywords: node, coffee, CoffeeScript, ES5, ES6, JavaScript
published: true
---

可能有些人還沒聽過 CoffeeScript 是什麼<br>
(因為我也是幾天前才知道的XD)<br>
其實 CoffeeScript 就是 Javascript<br>
只是他的語法比較簡潔易懂<br>
寫完 CoffeeScript 之後可以把它編譯成 Javascript<br>

---

## CoffeeScript 的幾個特性 :
- 用縮排取代大括號 (Python)
- List Comprehension 語法 (Python)
- OOP

---

## 怎麼把 CoffeeScript 編譯成 Javascript
```bash
npm install -g coffee-script        # 安裝 CoffeeScript 編譯器
coffee -o index.js -c index.coffee  # 把 index.coffee 編譯成 index.js
coffee -o js/ -c coffee/            # 編譯整個資料夾 coffee 到 js
```
如果覺得安裝環境很麻煩<br>
可以直接到<a href="http://coffeescript.org/#try:alert%20%22Hello%20CoffeeScript!%22" target="_blank">這裡</a>就可以開始寫了<br>
它會自動幫你轉換成 Javascript<br>

---

## 基本語法

### 宣告變數
CoffeeScript 不用分號也不用宣告變數<br>
直接用就可以了<br>

```coffeescript
a = 10
b = 20
```

編譯後

```js
var a, b;
a = 10;
b = 20;
```

### 插入字串
這是 Ruby 的用法<br>
很方便<br>

```coffeescript
name = "Larry"
greet = "Hello, #{name}"
```

編譯後

```js
var greet, name;
name = "Larry";
greet = "Hello, " + name;
```

### Function
function 的語法也跟 Ruby 滿像的

```coffeescript
bigger = (a, b) ->
    if a > b
        return true
    else
        return false

alert(bigger 1, 2)
```

編譯後

```js
var bigger;

bigger = function(a, b) {
    if (a > b) {
        return true;
    } else {
        return false;
    }
};

alert(bigger(1, 2));    // false
```

### 交換及指定變數
```coffeescript
[x, y] = [y, x]
[x, y, z] = [1, 2, 3]
```

編譯後

```js
var ref, ref1, x, y, z;

ref = [y, x], x = ref[0], y = ref[1];

ref1 = [1, 2, 3], x = ref1[0], y = ref1[1], z = ref1[2];
```

### Range
```coffeescript
list = [1..5]
for i in list
    alert i
```

編譯後

```js
var i, j, len, list;

list = [1, 2, 3, 4, 5];

for (j = 0, len = list.length; j < len; j++) {
    i = list[j];
    alert(i);
}
```

### List Comprehension
```coffeescript
alert i for i in [1..10]    # alert [1..10] 內的每個元素
```

編譯後

```js
var i, j;

for (i = j = 1; j <= 10; i = ++j) {
    alert(i);
}   // 依序 alert 出 1 到 10
```

### 物件

```coffeescript
larry = {name: "Larry", age: 19, career: "student"}

larry =
    name: "Larry"
    age: 19
    career: "student"
```

編譯後，兩種寫法會得到相同的結果

```js
var larry;

larry = {
name: "Larry",
      age: 19,
      career: "student"
};
```

### OOP
Javascipt 的 OOP 並不好寫<br>
而且寫起來也不夠直覺<br>
如果用 CoffeeScript 寫起來可以很清楚<br>

```coffeescript
class Student
    constructor: (name, age) ->
        this.name = name
        this.age = age
    
    say_hi: () ->
        str = "My name is #{this.name}, I am #{this.age}."
        alert(str)

larry = new Student("Larry", 19)
larry.say_hi()
```

編譯後

```js
var Student, larry;

Student = (function() {
    function Student(name, age) {
        this.name = name;
        this.age = age;
    }

    Student.prototype.say_hi = function() {
        var str;
        str = "My name is " + this.name + ", I am " + this.age + ".";
        return alert(str);
    };

    return Student;

})();

larry = new Student("Larry", 19);

larry.say_hi();     // My name is Larry, I am 19.
```

用 CoffeeScript 寫真的簡單很多吧 ~<br>

---

以上是基本的 CoffeeScript 介紹<br>
CoffeeScript 有很多優點<br>
不過缺點就是可能要再學一些新的語法<br>
但如果是學過 Python 或是 Ruby 應該不會太難上手<br>
要看更詳細的可以到<a href="http://coffeescript.org/#try:alert%20%22Hello%20CoffeeScript!%22" target="_blank">他們的網站</a><br>

---

這是我在 FB 上的粉專<br>
如果有新文章或是看到好的文章都會分享在這裡<br>
[賴瑞的程式筆記](https://www.facebook.com/賴瑞的程式筆記-1755838524703270/)<br>
如果內容有誤或是有疑問的也可以透過粉專跟我說<br>



