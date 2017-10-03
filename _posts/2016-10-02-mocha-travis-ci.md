---
layout: post
title: '[Node.js] 用 mocha 做單元測試並整合 Travis-CI'
keywords: js, javascript, mocha, unit test, travis ci, 單元測試, 持續整合 
published: true
---

單元測試(Unit Test)是對程式中的小單元進行測試<br>
目的是確保程式中的每個小螺絲釘都有好好的運作<br>

<img src="https://i.imgur.com/B6LfXre.png" width="400">

其實作者我之前都不寫測試<br>
程式寫完跑跑看沒有錯就拿來用了(相信很多人都是這樣XD)<br>
但最近有點懶不想手動測所以就開始寫測試<br>
一寫之後發現實在是太方便了<br>
每次改完 code 只要下一個指令就能知道有沒有錯誤<br>
<br>
很多人可能從來沒寫過測試<br>
所以這篇文章會用簡單的範例帶大家寫測試並整合至 Travis CI<br>

---

## 專案架構

這是現有程式的架構<br>

```
.
├── index.js
├── lib
│   ├── average.js
│   ├── max.js
│   └── min.js
├── node_modules
└── package.json
```

這個小專案很簡單<br>
只有三個小功能`average`、`max`跟`min`<br>
分別寫在`lib`中的三個 js 檔<br>

```js
// average.js 
// 取得陣列的平均值
module.exports = function average(array){
    var sum = 0;
    for(var i=0 ; i<array.length ; i++){
        sum += array[i];
    }
    return sum / array.length;
}
```

```js
// max.js
// 取得陣列中的最大值
module.exports = function max(array){
    var max = array[0];
    for(var i=0 ; i<array.length ; i++){
        max = array[i] > max ? array[i] : max;
    }
    return max;
}
```

```js
// min.js
// 取得陣列中的最小值
module.exports = function min(array){
    var min = array[0];
    for(var i=0 ; i<array.length ; i++){
        min = array[i] < min ? array[i] : min;
    }
    return min;
}
```

我們要為這三個小功能添加測試<br>

---

## 用 mocha 添加單元測試

`mocha`是一個 unit testing framework，用法相當的簡單<br>
除了`mocha`之外我們還需要`should`<br>
他會讓你更容易描述你要測試的內容<br>

```
npm install mocha -g            # mocha 執行檔要安裝在 global
npm install mocha --save-dev
npm install should --save-dev
```

安裝完之後在目錄下新增一個`test`資料夾<br>
裡面新增三個檔案`average.js`、`max.js`、`min.js`<br>
我們會把測試寫在各自的 js 檔內<br>

```
.
├── index.js
├── package.json
├── lib
│   ├── average.js
│   ├── max.js
│   └── min.js
├── test
│   ├── average.js
│   ├── max.js
│   └── min.js
└── node_modules
```

---

先來寫`average.js`的測試<br>
`mocha`的語法是這樣<br>

```js
describe('測試標題', function(){
    it('測試內容', function(done){
        // 進行測試
    })
    it('測試內容2', function(done){
        // 進行測試
    })
})
```

我們希望他可以求出陣列中的平均值<br>
如果陣列是空的則傳回`NaN`<br>

```js
// test/average.js
var should = require('should');
var average = require('../lib/average');

describe('#average', function(){
    it('should return the average of array', function(done){
        var avg = average([1, 2, 3, 4]);
        avg.should.equal(2.5);      // 測試算出來的平均是不是 2.5
        done();
    })

    it('should return NaN when array is empty', function(done){
        var avg = average([]);
        isNaN(avg).should.be.true;  // 測試有沒有回傳 NaN
        done();
    })
})
```

寫起來很平易近人<br>

---

接著再寫`max`的測試<br>
我們希望他可以求出陣列中的最大值<br>
如果陣列是空的則傳回`undefined`<br>

```js
// test/max.js
var should = require('should');
var max = require('../lib/max');

describe('#max', function(){
    it('should return the maximum in array', function(done){
        var maximum = max([1, 10, 100, 1000]);
        maximum.should.equal(1000);     // 測試有沒有取得正確的最大值
        done();
    })

    it('should return undefined when array is empty', function(done){
        var maximum = max([]);
        (maximum === undefined).should.be.true; // 測試有沒有傳回 undefined
        done();
    })
})
```

---

最後再來寫`min`的測試<br>
其實`min`跟`max`大同小異<br>
只要把最大改成最小就好了<br>

```js
// test/min.js
var should = require('should');
var min = require('../lib/min');

describe('#min', function(){
    it('should return the minimum in array', function(done){
        var minimum = min([1, 10, 100, 1000]);
        minimum.should.equal(1);    // 測試有沒有取得正確的最小值
        done();
    })

    it('should return undefined when array is empty', function(done){
        var minimum = min([]);
        (minimum === undefined).should.be.true; // 測試有沒有傳回 undefined
        done();
    })
})
```

---

這樣我們就寫完這三個功能的測試了<br>
那要怎麼跑測試呢<br>
很簡單，只要在目錄下跑`mocha`就可以了<br>

<img src="https://i.imgur.com/qbf5uEI.png" width="680">

如圖，他會自動跑`test`中的所有測試<br>
如果有錯誤的話會長這樣<br>

<img src="https://i.imgur.com/FC1yIh1.png" width="680">

看到勾勾全部都亮起來心情就很好XD<br>

---

## 整合至 Travis CI

Travis CI 是一個提供持續整合(continuous integration)的服務平台<br>
如果你有在用 Github 的話就可以把你的專案跟 Travis CI 整合<br>
你有任何更新 Travis CI 會自動幫你進行測試<br>
<br>
首先到 [Travis CI 網站](https://travis-ci.org) 右上角用 Github 登入<br>

<img src="https://i.imgur.com/l18sqNy.png" width="680">

登入後他會列出你在 Github 上所有的 repository<br>
選定要做 CI 的專案<br>

<img src="https://i.imgur.com/ZtzjUoS.png" width="630">

接著在專案目錄中加入一個檔案`.travis.yml`用來寫`travis`的設定<br>

```
.
├── index.js
├── package.json
├── .travis.yml    <--- 加在這裡
├── lib/
├── test/
└── node_modules/
```

底下這個範例是自動測試 4、5、6 三個 node 版本<br>

```yml
language: node_js
node_js:
    - "6"
    - "5"
    - "4"
```

因為 Travis 會使用`npm test`進行測試<br>
所以要把`package.json`中的`test script`改成`mocha`<br>
這樣就會在 Travis 上跑 mocha 測試<br>
這是我的`package.json`<br>

```json
{
    "name": "mocha-travis-ci-example",
    "version": "1.0.0",
    "description": "A mocha unit test example",
    "main": "index.js",
    "scripts": {
        "test": "mocha # <---- 改這裡"    
    },
    "author": "Larry Lu",
    "license": "ISC",
    "devDependencies": {
        "mocha": "^3.1.0",
        "should": "^11.1.0"
    }
}
```

這樣一來就全部設定好了<br>
只要 push 到 Github 上 travis 就會幫忙測試<br>
push 之後到 travis 上看看<br>
三個版本都通過測試了<br>

<img src="http://i.imgur.com/jPJfehf.png" width="700">

可以把上面 build 狀態放在 README 裡面<br>
這樣會讓專案看起來比較可靠<br> 
若有人送 pull request 也可以跑測試來檢驗正確性<br>

<img src="https://i.imgur.com/YibFCqc.png" width="700">

---

## 總結

這篇文章用很簡單的範例帶大家走過`mocha`、`should`跟`Travis CI`<br>
文章中的範例程式碼放在<a href="https://github.com/Larry850806/mocha-travis-ci-example" target="_black"> Larry850806/mocha-travis-ci-example </a><br>
實際要測試的案例可能不會這麼簡單，但方法都是差不多的<br>
想要更深入學習可以看看他們的文件<br>

- <a href="https://mochajs.org" target="_black"> mocha </a>
- <a href="https://shouldjs.github.io" target="_black"> should.js </a>
- <a href="https://docs.travis-ci.com" target="_black"> Travis CI DOC </a>

雖然寫測試有點麻煩，但只要寫一次就好了<br>
自己手動測試難免會有疏漏而且又浪費時間<br>
時常找不出真正的 bug <br>
為了你的肝著想，今天就開始寫測試吧！<br>
