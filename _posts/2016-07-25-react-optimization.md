---
layout: post
title: '[JavaScript] 如何提高 React App 的效能'
keywords: js, javascript, react, reactjs, optimization, shouldComponentUpdate, immutable
published: true
---

這篇是我最近在鑽研 React.js 時的一些心得<br>
關於怎麼使用 `shouldComponentUpdate` 及 `immutable.js` 提高效能<br>

<img src="http://blog.addthiscdn.com/wp-content/uploads/2014/11/addthis-react-flux-javascript-scaling.png" width="400">

---

## Virtual DOM

因為使用傳統的方式對 DOM 進行操作很慢<br>
為此 React 設計了 Virtual DOM 這個中間層來降低操作 DOM 的成本<br>
Virtual DOM 是一個類似實際 DOM 節點的樹狀結構<br>
當有資料改變時才會透過 diff 演算法計算最小差異並更新到實際的 DOM 上<br>
<br>
所以當我們 render 時其實都是 render 到 Virtual DOM 上<br>
React 會自己幫我們算出最小差異然後更新<br>

![](https://docs.google.com/drawings/d/11ugBTwDkqn6p2n5Fkps1p3Elp8ZToIRzXzvM4LJMYaU/pub?w=543&h=229)

---

## shouldComponentUpdate

在講 `shouldComponentUpdate` 之前先來看看 Component 的生命週期<br>
當 `state` 或是 `props` 改變的時候會先呼叫 `shouldComponentUpdate()`<br>
如果回傳 `true` 才會 render 到 Virtual DOM<br>
最後再由 React 的 diff 演算法決定要怎麼更新 Real DOM<br>

![](http://imgur.com/YGROhPj.png)

因為 `shouldComponentUpdate()` 預設是回傳 true<br>
也就是 "一律重繪"<br>
不管有沒有改變一律重新 render 到 Virtual DOM 上<br>
最後由 diff 演算法判斷到底哪些需要改變<br>

雖然 React 的 diff 算法很高效<br>
但如果數量一多 React 要遞迴跑完所有 component 的 render 還是會拖慢速度<br>
所以可以自己實作`shouldComponentUpdate`<br>
如果資料相同就不要重新 render<br>

如果有一個 Component 叫 Item:

```js
// <Item content="item1" />

var Item = React.createClass({
    render(){
        return <h1> {this.props.content} </h1>;
    }
});
```

可以加入一個`shouldComponentUpdate(nextProps, nextState)`<br>

```js
shouldComponentUpdate(nextProps, nextState){
    if(this.props.content !== nextProps.content) return true;
    return false;
}
```

只有在`this.props.content`改變的時候才重新 render<br>
這樣就可以大大減低重新 render 的次數<br>

--- 

## 可能會遇到什麼問題？？

<img src="http://e.share.photo.xuite.net/qmother0615/1ee0ca0/6395131/261147045_l.jpg" width="400">

有時候可能會遇到`props`是物件或者陣列的情況<br>
但物件跟陣列沒辦法比較相等<br>
看看下面的例子<br>

```js
var obj1 = {name: 'Larry', age: 19};
var obj2 = {name: 'Larry', age: 19};

console.log(obj1 === obj2); // false
console.log(obj1.name === obj2.name && obj1.age === obj2.age); // true

var arr1 = [1, 2, 3];
var arr2 = [1, 2, 3];

console.log(arr1 === arr2); // false

// 比較 array 內所有元素 -> true
console.log(function(){
    if(arr1.length != arr2.length) return false;
    for(var i=0 ; i<arr1.length ; i++){
        if(arr1[i] !== arr2[i]) return false;
    }
    return true;
}());
```

用`===`進行比較的話只能比較是不是同一個物件<br>
如果要比較內容的話要自己進行比較<br>
陣列也是要自己遍歷整個陣列<br>
所以`shouldComponentUpdate`可能會寫成這樣<br>

```js
// <List items={['item1', 'item2', 'item3']} />

shouldComponentUpdate(nextProps, nextState){
    if(this.props.items.length != nextProps.items.length) return true;
    for(var i=0 ; i<this.props.items.length ; i++){
        if(this.props.items[i] !== nextProps.items[i]) return true;
    }
    return false;
}
```

但因為是遍歷整個陣列<br>
如果數量一多的話還是會超慢<br>
這時候就可以用`immutable.js`加速<br>

---

## Immutable.js

<img src="https://d2eip9sf3oo6c2.cloudfront.net/series/covers/000/000/022/full/immutable_js_egghead_lesson_series.jpg?1444692575" width="450">

`immutable.js` 是由 Facebook 開源的一個 library<br>
提供各種不同的結構<br>
可以用`npm install immutable`安裝<br>
比較常用的有 List 跟 Map<br>
這邊只稍微提一下怎麼用<br>
想深入了解的可以看[官方文件](http://facebook.github.io/immutable-js/docs/)<br>

immutable.js 中的 List 就像是 Array<br>

```js
var Immutable = require('immutable');
var list1 = Immutable.List.of('a', 'b', 'c');  // ['a', 'b', 'c']
var list2 = Immutable.List.of('a', 'b', 'c');  // ['a', 'b', 'c']

console.log(Immutable.is(list1, list2));  // true
console.log(list1.get(0));                // 'a'
list1 = list1.set(0, 'b');                // list1 = ['b', 'b', 'c']
console.log(Immutable.is(list1, list2));  // false
```

immutable.js 中的 Map 就像是 Object<br>

```js
var map1 = Immutable.Map({name: 'Larry', age: 19});
var map2 = Immutable.Map({name: 'Larry', age: 19});

console.log(Immutable.is(map1, map2));  // true
map1 = map1.set('name', 'Larry Lu');
// map1 = {name: 'Larry Lu', age: 19}

console.log(map1.get('name'));          // 'Larry Lu'
conosle.log(Immutable.is(map1, map2));  // false
```

這樣就可以用`Immutable.is`來比較陣列跟物件<br>
而且`Immutable.is`不是把每個值都取出來比較<br>
而是創建 List 跟 Map 時就計算得到一個 hash value<br>
比較時就比較那個 hash value 就好了<br>
所以速度會快非常多<br>

還有一個要注意的點是`immutable.js`創造出來的物件是不可變的<br>
在 js 內要更改陣列內的元素只要`arr[index] = value`<br>
但用`immutable.js`時要`list = list.set(index, value)`<br>
因為`set`時不會更改原本的而是創造一個新的`List`<br>
`map`也是一樣<br>
所以一定要`map = map.set('name', 'Larry Lu')`<br>

---

## 結論

綜合 shouldComponentUpdate 及 Immutable.js 之後<br>
最後就可以把 component 寫成這樣<br>

```js
// <Item info={Immutable.Map({'name': 'Larry', age: 19})} />

var Item = React.createClass({
    shouldComponentUpdate(nextProps, nextState){
        return !Immutable.is(this.props.info, nextProps.info);
    },
    render(){
        return (
            <div>
                <h1> {'name: ' + this.props.name} </h1>
                <h2> {'age: ' + this.props.age} </h2>
            </div>
        );
    }
});
```

這樣就可以減少很多重新 render 的次數<br>
而且在判斷要不要重新 render 時也可以非常快速<br>
讓原本就很快的 React 再更快～～<br>


