---
layout: post
title: '[JQuery] 用 JQuery 做出卡片翻動的效果'
keywords: jquery, flip, css, animation, 翻動, 動畫
published: false
---

前陣子在做網頁的想要做卡片翻動的效果<br>
就去查了一下怎麼做<br>
發現有一個 JQuery 的 plugin 可以做出不錯的翻動<br>
效果大概像這樣 :<br>
<div id="photo" style="width: 160px;height: 160px;border: 3px; background-color: #77FFEE;"> 
    <div class="front" align="center">
        <h2 style="margin-top: 61px;"> 點我 </h2>
    </div> 
    <div class="back">
        <img id="img" src="https://avatars0.githubusercontent.com/u/10403741?v=3&s=160">
    </div> 

    <script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>
    <script src="https://cdn.rawgit.com/nnattawat/flip/master/dist/jquery.flip.min.js"></script>

    <script>
        var i = 0;
        $(function($) {
            var $img = $("#img");
            $("#photo").flip(); 

            $("#photo").on('flip:done',function(){
                i++;
                if(i % 4 == 2){
                    $img.attr("src", "http://imgur.com/qo8vP3J.png");
                } else if (i % 4 == 0){
                    $img.attr("src", "https://avatars0.githubusercontent.com/u/10403741?v=3&s=160");
                }
            });
        });
    </script>
</div>

---

## 最基本只有文字的效果 : 
<div id="card" style="width: 117px; background-color: #black;"> 
    <div class="front"> 
        Click Me
    </div> 
    <div class="back">
        Hello World
    </div> 

    <script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>
    <script src="https://cdn.rawgit.com/nnattawat/flip/master/dist/jquery.flip.min.js"></script>

    <script>
        $(function($) {
            $("#card").flip(); 
        });
    </script>
</div>
<br>

```html
<div id="card" style="width: 117px; background-color: black;"> 

    <div class="front"> 
        Click Me
    </div> 
    <div class="back">
        Hello World
    </div> 

</div>


<script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>
<script src="https://cdn.rawgit.com/nnattawat/flip/master/dist/jquery.flip.min.js"></script>

<script>
    $(function($) {
        $("#card").flip(); 
    });
</script>
```

### 註解
class="card"  是整張卡，包刮正面和背面<br>
class="front"  是正面的內容<br>
class="back"  是背面的內容<br>
<br>
裡面也可以放圖片或其他東西<br>
不一定要放文字<br>


---

## 其他參數 :
也可以加一些參數在 flip 裡面<br>
做出不一樣的效果<br>
還滿有趣的<br>
<div id="card2" style="width: 117px; background-color: #black;"> 
    <div class="front"> 
        Click Me
    </div> 
    <div class="back">
        Hello World
    </div> 

    <script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>
    <script src="https://cdn.rawgit.com/nnattawat/flip/master/dist/jquery.flip.min.js"></script>

    <script>
        $("#card2").flip({
            axis: 'x',
            trigger: 'hover'
        });
    </script>
</div>
<br>

```html
<script>
    $("#card2").flip({
        axis: 'x',          // 繞 x 軸翻轉
        trigger: 'hover'    // 游標進出
    });
</script>
```

---

想要看更詳細的可以到<a href="http://nnattawat.github.io/flip/" target="_blank">他們的網站</a><br>
也有更多其他的用法<br>
