---
layout: post
title: '[Node.js] 理解 Node.js 事件驅動'
keywords: node.js, async
published: true
---

![](http://tw.gitbook.net/uploads/allimg/150329/14252515U-0.jpg)

這篇講的東西比較篇概念<br>
所以文字敘述會多一點<br>
希望大家可以有耐心的看完～<br>

---

## Async(非同步)

Node.js 是一個單執行緒且非同步的語言<br>
非同步的 function 會被放進一個 event queue<br>
等其他 code 跑完之後才會跑那個 event queue<br>
如果 queue 裡面有很多事要做就會輪流做<br>
所以不會阻塞執行緒<br>

## setTimeout

Javascript 最簡單的 async 函式是 setTimeout<br>
setTimeout 會在一定的時間之後執行某個函式<br>
或說是在一定時間之後把那個函式放進 event queue<br>
等其他事情都做完就會就會開始做 event queue 內的事情<br>
來看看這一段 code<br>

### 範例1

```js
setTimeout(function(){
    console.log('callback');
}, 1000);

console.log('Hello World');
```

> 剛開始跑到 setTimeout<br>
> 系統會設定在 1 秒之後把 function 要做的事放到 event queue<br>
> 然後就繼續跑到下面輸出 "Hello World"<br>
> 輸出結束之後這時候事情都做完了<br>
> <br>
> 等了一秒 function 被塞到 event queue<br>
> 這時候沒有事情要做所以就開始跑 event queue 內的事情<br>
> 然後就輸出 "callback"<br>

### 範例2

```js
setTimeout(function(){
    console.log('callback');
}, 1000);

while(true){
    var a = 1 * 2;
}
```

> 這個跟 範例1 不太一樣<br>
> 先跑到 setTimeout<br>
> 系統會設定在 1 秒之後把 function 要做的事放到 event queue<br>
> 繼續往下跑到 while<br>
> 一直算一些東西<br>
> <br>
> 等了一秒 function 被塞到 event queue<br>
> 這時候因為一直有事情要做<br>
> 所以雖然`console.log('callback')`已經被放到 event queue <br>
> 但永遠不會執行<br>
> 因為程式要在做完主要的事情才會開始跑 event queue<br>

---

## Callback(回呼)

因為不知道 async 的那些工作什麼時候會完成<br>
但我們又需要等那些工作做完之後才能做某些事情<br>
<br>
譬如說如果要判斷一個檔案裡面有多少字元<br>
要先讀檔案<br>
接著再從讀到的字串計算有多少個字元<br>
但偏偏讀檔案的函式是 async<br>
這時候就需要 callback<br>

### 範例1

讀檔案並計算長度<br>

```js
var fs = require('fs');

// fs.readFile(filename, callback(err, content))

fs.readFile('test.txt', function(err, content){
    var str = content.toString();
    console.log(str.length);
    console.log('finish');
});

console.log('not finish');
```

> fs 是個 nodejs 的模組<br> 
> 可以用來讀檔案<br>
> 跑到`fs.readFile`的時候<br>
> 系統會把讀檔案這個任務放到 event queue<br>
> 等沒事的時候再去做<br>
> 接著就繼續往下跑輸出`not finish`<br>
> 
> 因為不知道檔案什麼時候會讀完<br>
> 所以我們把檔案讀完要做的事情放在 callback 裡面<br>
> 雖然不知道什麼時候會讀完<br>
> 但只要一讀完檔案就會做 callback 裡面的事情<br>
> 計算那個檔案裡面有多少字元<br>
> 然後輸出`finish`<br>

### 範例2

讀完檔案在檔尾加上`Hello World`<br>
再寫回去檔案<br>

```js
var fs = require('fs');

// fs.readFile(filename, callback(err, content))
// fs.writeFile(filename, content, callback(err))

fs.readFile('test.txt', function(err, content){
    var str = content.toString();
    str += 'Hello World';
    fs.writeFile('test.txt', str, function(err){
        console.log('finish');
    });
});

console.log('not finish');
```

> 用 callback 的方式可以一直疊一直疊<br>
> 讀完檔案之後在尾巴加上`Hello World`<br>
> 之後再寫入檔案<br>
> 但寫入檔案也是 async<br>
> 所以也要設定一個 callback<br>
> 寫入完成之後再輸出`not finish`<br>

### 範例3

在 callback 內檢查錯誤<br>

```js
var fs = require('fs');

fs.readFile('test.txt', function(err, content){
    if(err) console.log(err);

    var str = content.toString();
    str += 'Hello World';
});
```

> callback 內通常都會有一個`err`傳回<br>
> 如果不是`null`或`undefined`代表處理過程中有發生錯誤<br>
> 譬如說要讀取`test.txt`這個檔案<br>
> 結果根本沒有這個檔案<br>
> 那就會發生錯誤<br>
> 用 callback 一定要檢查錯誤<br>
> 如果有錯誤繼續做下去可能會發生非預期的結果<br>

---

## 自己寫一個 async function

我們也可以把一些 async 的動作包起來<br>
自己寫一個 async function<br>
然後定義一個 callback<br>

### 範例

寫一個函式叫做 append<br>
功能是在某個檔案後面加上某個字串<br>
因為 append 需要讀檔寫檔<br>
所以一定也是個 async 函式

prototype 可能長這樣<br>
`append(filename, str, callback(err))`

```js
var append = function(filename, str, callback){
    fs.readFile(filename, function(err1, content){
        if(err1) console.log(err1);
        
        var newContent = content.toString() + str;
        fs.writeFile(filename, newContent, function(err2){
            if(err2) console.log(err2);

            callback(err1 || err2);
        });
    });
};

append('text.txt', 'Hello World', function(err){
    if(err) console.log(err);
    console.log('finish');
});
```

> 在`append`內剛開始先讀檔接著寫檔<br>
> 等讀寫都完成之後就呼叫 callback<br>
> 這樣就會跑使用者定義的 callback function<br>
> <br>
> 寫完之後就可以直接像下面這樣用<br>
> 然後給一個 callback function<br>
> 在`append`都完成之後就會跑 callback 然後輸出`finish`<br>

---

## Callback Hell(回呼地獄)

因為使用 callback 可能會一直往上疊<br>
先進行 A 接著跑 B 然後 C, D 等等等<br>
B 需要 A 的結果，C 需要 B 的結果<br>
寫出來的 code 可能會長這樣<br>

```js
func1(function(err1, result1){
    if(err1){
        console.log(err1);
    } else {
        func2(result1, function(err2, result2, result3){
            if(err2){
                console.log(err2);
            } else {
                func3(result2, result3, function(err3, result4){
                    if(err3){
                        console.log(err3);
                    } else {
                        console.log(result4);
                    }
                });
            }
        });
    }
});
```

> 這樣的 code 還是對的<br>
> 只是很醜很難 debug<br>
> 可以用 async module 來改善<br>
> 避免 code 越疊越高<br>
> 可以參考[這篇 async 介紹](../../../05/31/async/)<br>
> 不然也可以用 Promise<br>
> 不過那比較難懂<br>
> 建議完全理解 callback 再開始摸 Promise<br>

## 總結

整個程式只有一個 event queue<br>
async 的 function 都會被塞到 event queue<br>
等主要的事情做完就開始跑 event queue<br>
裡面的所有任務會輪流跑<br>
跑完就呼叫 callback<br>

---

Javascript 的 callback 機制跟其他語言不一樣<br>
像 C++ / Java 都是一行一行跑<br>
但 js 因為有一堆 callback 常常不知道現在在跑哪一段 code<br>
剛開始會覺得有點亂<br>
但久了之後覺得還滿好用的<br>
不用自己去確認某件事做完了沒<br>
反正做完會有 callback<br>

Node.js 的事件驅動機制介紹就到這裡<br>
想看 async 的可以參考這一篇<br>
[如何用 async 控制流程](../../../05/31/async/)<br>

