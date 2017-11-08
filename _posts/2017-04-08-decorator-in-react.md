---
layout: post
title: '[React.js] 用 @decorator 來裝飾你的 Component 吧！'
keywords: javascript, js, decorator, es7, babel, react, component, HOC, higher-order component, react-decoration, recompose, decorator pattern, 裝飾器, 裝飾者模式, 裝飾
published: true
---

<script>
    window.location = "https://larrylu.blog/react-decorator-hoc-2536db2737cb"
</script>

Decorator 是 JS 以後會有的新語法，它讓我們用更簡單的方法幫類別(class)、函式(function) 添加一些特性，這篇主要講述如何在 React 中使用 decorator 及 HOC 裝飾你的 Component。

<img src="https://i.imgur.com/yAUPHu6.png" width="550px" />

在開始之前先提醒一下大家，因為 decorator 目前還在 Stage 2 階段，大部分環境都還沒有支援，所以需要使用 <a href="https://github.com/loganfsmyth/babel-plugin-transform-decorators-legacy" target="_blank"> babel-plugin-transform-decorators-legacy </a> 這個 babel 插件來進行轉譯。

---

## 語法

很多人可能還不太知道 decorator 的語法，先來看幾個簡單的例子

#### 幫 MyClass 加上一個 isTestable 的屬性

```js
function isTestable(target) {
    target.isTestable = true
    return target
}

// isTestable 是個 decorator
@isTestable
class MyComponent {
    // ...
}

console.log(MyComponent.isTestable) // true

export default MyComponent
```

#### 帶參數

```js
function isTestable(value) {
    return function (target) {
        target.isTestable = value;
        return target
    }
}

// isTestable 是個 function
// isTestable(true) 是個 decorator
@isTestable(true)
class MyComponent {
    // ...
}

console.log(MyComponent.isTestable) // true

export default MyComponent
```

#### 如果想要裝飾的對象是 function，可以在 export 的時候裝飾

```js
function isTestable(target) {
    target.isTestable = true
    return target
}

const MyComponent = (props) => (
    // ...
)

// decorator 也是普通的 function
export default isTestable(MyComponent) //  <-- 這裡
```

---

### Decorator 與 HOC (Higher-Order Component)

HOC 其實就是個 function，它接收某個 Component 作為他的參數，並且 return 另外一個新的 Component。像`react-redux`中的`connect`就是個 HOC，他負責訂閱 store 的改變並且通知我們的 Component，我們通常會這樣使用他：

```js
import { connect } from 'react-redux'

class MyComponent extends React.Component {
    // ...
}

export default connect(mapStateToProps, mapDispatchToProps)(MyComponent)
```

因為 HOC 跟 decorator 一樣都是接受一個 Component 作為參數，並且回傳一個新的 Component，所以我們也可以把 HOC 當成 decorator 來使用

```js
import { connect } from 'react-redux'

@connect(mapStateToProps, mapDispatchToProps)
class MyComponent extends React.Component {
    // ...
}

export default MyComponent
```

如果要使用多個 HOC 的話也可以寫成這樣

```js
import hoc1 from 'hoc1'
import hoc2 from 'hoc2'

@hoc1
@hoc2
class MyComponent extends React.Component {
    // ...
}

export default MyComponent
// export default hoc1(hoc2(MyComponent))
```

---

相信大家看完上面的範例之後都知道怎麼使用 decorator 了，但如果還要自己實作 decorator 實在是很麻煩，以下會介紹一些常用的 library 及應用場景，大家可以直接使用。

### <a href="https://github.com/mbasso/react-decoration" target="_blank"> react-decoration </a>

react-decoration 是一系列 decorator 的集合，專門給 React Component 用，以下簡單介紹幾個

#### @autobind

傳函式給其他 Component 時常常需要 bind 住 this，這時候就可以用 autobind

```js
import { autobind } from 'react-decoration'

class MyComponent extends React.Component {

    @autobind
    onClick() {
        this.setState({
            // ...
        })
    }

    render() {
        return (
            <button onClick={this.onClick} />
            // 如果沒有 autobind 就需要
            // <button onClick={this.onClick.bind(this)} />
        )
    }
}
```

---

#### @log

當某個函式被執行時自動 log 出傳過來的參數，不用在 function 內自己`console.log`，只要加個`@log`就可以了，debug 時很好用

```js
import { log } from 'react-decoration'

class MyComponent extends React.Component {

    constructor(){
        super()
        this.state = {
            value: ''
        }
    }

    @log
    onChange(e) {
        // console.log('Calling function "onChange" with params: ', e)
        this.setState({
            value: e.target.value,
        })
    }

    render() {
        return (
            <input
                value={this.state.value}
                onChange={this.onChange.bind(this)}
            />
       )
    }
}
```

---

#### @component

`@component`會自動幫你`extends React.Component`，這樣就不需要自己`extends`了(是有沒有這麼懶XDD)

```js
import { component } from 'react-decoration'

@component
class MyComponent {
    render() {
        return <div> Hello World </div>
    }
}
```

---

#### @throttle

這是我覺得最好用的 decorator 之一，throttle 的中文是節流閥，作用是讓某個 function 在一段時間內最多只觸發一次，可以用在監聽滾動或者其他頻繁觸發的事件上，沒有 throttle 的話 scroll 事件一秒可以被觸發上百次，如果你的 handler function 不幸又有大量運算就會讓整個網頁變得很卡

```js
import { throttle } from 'react-decoration'

class MyComponent {
    
    componentDidMount() {
        document.addEventListener('scroll', this.handleScroll)
    }

    componentWillUnmount() {
        document.removeEventListener('scroll', this.handleScroll)
    }

    // 每 300 ms 最多觸發一次
    @throttle(300)
    handleScroll(e) {
        // do something
    }

    render() {
        return (
            <div {...this.props} />
        )
    }
}
```

---

### <a href="https://github.com/acdlite/recompose" target="_blank"> recompose </a>

recompose 提供了很多實用的 HOC，可以用來增強你的 Component

#### withState

用`withState`把 state 獨立出來，以下面的例子來說，`counter`初始值是 0，`setCounter`用來設定`counter`的值，`counter`跟`setCounter`會被當成`props`傳進 Component 內

```js
import { withState } from 'recompose'

const enhance = withState('counter', 'setCounter', 0)

const Counter = ({ counter, setCounter }) => (
    <div>
        Count: {counter}
        <button onClick={() => setCounter(n => n + 1)}> Increment </button>
    </div>
)

export default enhance(Counter)
```

---

#### pure

如果你的 Component 是 Pure Component，就可以利用`pure`這個 HOC 來避免許多不必要的重新 render，因為 react 中的`shouldComponentUpdate`預設是`true`，即使`props`沒有改變也會重新`render`出新的 virtual DOM，如果底下的 Component 數量一多就會使效能低落

```js
import { pure } from 'recompose'

const Post = ({ title, content }) => (
    <div>
        <h1>{title}</h1>
        <div>{content}</div>
    </div>
)

export default pure(Post)
```

---

#### lifecycle

有時候想要在`componentDidMount`時做一些事情，但又不想把 Pure Component 寫成 ES6 Class 的形式，這時候就可以用`lifecycle`幫你的 Component 加上`componentDidMount`

```js
import { lifecycle } from 'recompose'

const enhance = lifecycle({
    componentDidMount() {
        console.log('compnent did mount')
    },
    componentWillUnmount() {
        console.log('component will unmount')
    }
})

const MyComponent = () => (
    <div> Hello World </div>
)

export default enhance(MyComponent)
```

---

#### compose

compose 可以把多個 HOC 組合起來，寫起來會比較好懂，要修改也比較容易

```js
import { compose } from 'recompose'

const MyComponent = () => (
    // ...
)

const enhance = compose(hoc1, hoc2, hoc3)

export default enhance(MyComponent)
// export default hoc1(hoc2(hoc3(MyComponent)))
```

---

## 總結

其實筆者我自己才剛接觸 decorator 沒有很久，所以也還在摸索中，若有哪裡寫得不太正確請大家多多指教。
<br />
<br />
對 decorator 跟 HOC 有興趣的話可以更深入看看 <a href="https://github.com/mbasso/react-decoration" target="_blank"> react-decoration </a> 跟 <a href="https://github.com/acdlite/recompose" target="_blank"> recompose </a> 這兩個 library，裡面還有很多 HOC 我也都還沒有用過，如果有人知道什麼其他有趣的使用方法也可以跟我說，我會補充上去讓更多人知道～


