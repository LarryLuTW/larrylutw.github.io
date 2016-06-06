---
layout: post
title: '[Node.js] 用 cheerio 抓取即時天氣'
keywords: request, Node.js, Nodejs, module, cheerio
published: true
---

### cheerio 是什麼
cheerio 是一個 Node.js 的 module<br>
是可以跑在 server 端的 JQuery<br>
可以用來擷取網頁中的資料<br>

---

### 網站
先到我們要抓取天氣的地方<br>
當然是中央氣象局啦～<br>
這次先示範抓台北市的天氣狀況<br>
![](http://imgur.com/joGLJkt.png)

---

### Chrome Developer Tool
打開 Chrome Developer Tool<br>
Windows 上的快捷鍵是 Ctrl + Shift + I<br>
Mac 上的快捷鍵是 Cmd + Option + I<br>
打開之後應該像這樣<br>
![](http://imgur.com/sRByvhS.png)
可以看到整個網頁的 HTML<br>

---

### 找到我們要的區塊
我們要的主要是中間有氣象資料的那一塊<br>
從下面 HTML 的往裡面找<br>
可以找到我們要的這個區塊<br>
![](http://imgur.com/fOv7dCG.png)
被選到的地方在網頁上會反白<br>

---

### 觀察 HTML 標籤跟 class 
```html
<!--
發現在 <table class="FcstBoxTable01"> 裡面的 <tbody> 有三個 <tr>
每個 <tr> 裡面有一筆資料
總共有三筆
-->

<table class="FcstBoxTable01" summary="編排用">
    <tbody>
        <tr>
            <th scope="row">今晚至明晨 06/06 18:00~06/07 06:00</th>
            <td>25 ~ 27</td>
            <td>
            <td>舒適至悶熱</td>
            <td>20 %</td>
        </tr>
        <tr>
            <th scope="row">明日白天 06/07 06:00~06/07 18:00</th>
            <td>25 ~ 31</td>
            <td>
            <td>舒適至悶熱</td>
            <td>30 %</td>
        </tr>
        <tr>
            <th scope="row">明日晚上 06/07 18:00~06/08 06:00</th>
            <td>26 ~ 29</td>
            <td>
            <td>舒適至悶熱</td>
            <td>20 %</td>
        </tr>
    </tbody>
</table>
```

---

### 開始寫 code

```html
<!--
在 <table class="FcstBoxTable01"> 裡面的 <tbody> 有三個 <tr>
每個 <tr> 裡面有一筆資料
總共有三筆
-->
```

```js
var request = require('request');
var cheerio = require('cheerio');

var url = 'http://www.cwb.gov.tw/V7/forecast/taiwan/Taipei_City.htm';
request(url, function(err, res, body){
    // 去跟中央氣象局的網站要資料

    $ = cheerio.load(body);
    // 把要到的資料放進 cheerio

    var weather = []
    var tmp = $('.FcstBoxTable01 tbody tr').each(function(i, elem){
        weather.push($(this).text().split('\n'));
    });
    // 語法都跟 jquery 一樣
    // 找到 class = "FcstBoxTable01"
    // 再找標籤 <tbody>
    // 取得裡面的每一個 <tr>
    // 取文字部分分行之後放進 weather

    console.log(weather);

});

/*

weather:
[ 
    [
        '',
        '\t\t今晚至明晨 06/07 00:00~06/07 06:00',
        '\t\t25 ~ 26',
        '\t\t',
        '\t\t',
        '\t\t舒適',
        '\t\t30 %',
        '\t\t' 
    ],
    [
        '',
        '\t\t明日白天 06/07 06:00~06/07 18:00',
        '\t\t25 ~ 31',
        '\t\t',
        '\t\t',
        '\t\t舒適至悶熱',
        '\t\t30 %',
        '\t\t'
    ],
    [
        '',
        '\t\t明日晚上 06/07 18:00~06/08 06:00',
        '\t\t26 ~ 29',
        '\t\t',
        '\t\t',
        '\t\t舒適至悶熱',
        '\t\t20 %',
        '\t\t'
    ]
]

*/
```

---

### 整理一下我們要的資料

```js

var output = [];

for(var i=0 ; i<weather.length ; i++){
    output.push({
        time: weather[i][1].substring(2).split(' ')[0],
        temp: weather[i][2].substring(2),
        rain: weather[i][6].substring(2)
    });
}

console.log(output);

/*

output:
[
    { 
        time: '今晚至明晨',
        temp: '25 ~ 26', 
        rain: '30 %' 
    },
    {
        time: '明日白天',
        temp: '25 ~ 31',
        rain: '30 %'
    },
    {
        time: '明日晚上',
        temp: '26 ~ 29',
        rain: '20 %'
    }
]

*/
```

---

### 最後輸出出來

```js
for(var i=0 ; i<output.length ; i++){
    var time = output[i].time;
    var temp = output[i].temp;
    var rain = output[i].rain;
    var str = time + '，溫度大約' + temp + '度，降雨機率 ' + rain;
    console.log(str);
}
```

### 執行結果
每次執行都會跟網站要資料然後顯示<br>
![](http://imgur.com/Zmjnb6W.png)

---

### 總結
1. 先找到要抓哪個網站的資料
2. 用 Chrome Developer Tool 觀察 HTML 結構
3. 寫 code 用 request 向網站要資料
4. 把要來的資料用 cheerio 取出我們要的部份
5. 把資料整理完之後輸出

---

以上是用 Node.js 抓天氣的教學<br>
request 跟 cheerio 還有其他很多應用<br>
想要深入了解的可以去看他們的 github repository<br>

---

這是我在 FB 上的粉專<br>
如果有新文章或是看到好的文章都會分享在這裡<br>
[賴瑞的程式筆記](https://www.facebook.com/賴瑞的程式筆記-1755838524703270/)<br>
如果內容有誤或是有疑問的也可以透過粉專跟我說

