---
layout: post
title: '[Node.js] 自己動手打造 ES7 開發環境'
keywords: js, javascript, react, reactjs, optimization, shouldComponentUpdate, immutable
published: true
---

![](http://i.imgur.com/ZAFeDJr.png)

因為前陣子接觸了 ES7 的 async/await<br>
習慣了之後就覺得 ES6 的 Promise 很難用XD<br>
就想用 babel 來當轉譯器<br>
架個 ES7 的開發環境來玩 async/await<br>

## 開發環境的架構

```
.
├── package.json
├── node_modules
├── gulpfile.js
├── index.js
├── src
│   ├── index.js
│   └── utils.js
├── build
    ├── index.js
    └── utils.js
```

- `package.json` 跟 `node_modules` 大家應該都很熟悉就不多做說明了
- `gulpfile.js` 是 `gulp` 的任務配置
- `src` 裡面放 ES7 的 source code
- `build` 裡面放被 babel 轉譯過後的 ES5 程式碼
- `index.js` 是整個程式的起點，什麼都不做就只跑 `build` 裡面的 `index.js`

```js
// index.js

require('./build/index');
```

---

## 開發流程

開發時只動 `src` 裡面的 code<br>
Gulp 會自動轉譯 `src` 裡面的 code 並放到 `build`<br>
<br>
要執行時就跑最外層的 `index.js`<br>
`index.js` 會自動跑 `build/index.js`<br>

---

## Gulp 配置

![](http://i.imgur.com/buXJvp2.png)

Gulp 是個很好用的自動化工具<br>
他可以運用一些別人寫好的套件來加速開發流程<br>
像是 compile、minify、uglify 等等<br>
對 Gulp 不太熟悉的可以先看看這篇[Gulp 入門指南](https://github.com/nimojs/gulp-book)<br>

### 在 Gulp 內使用 babel

```js
// gulpfile.js

var gulp = require('gulp');
var babel = require('gulp-babel');

gulp.task('babelify', function(){
    return gulp.src('src/**/*.js')
        .pipe(babel({
            presets: ['es2015', 'es2016', 'es2017'],
            plugins: [
                [
                    "transform-runtime", {
                        "polyfill": false, 
                        "regenerator": true
                    }
                ]
            ]
        }))
        .pipe(gulp.dest(build))
});
```

只要在 command line 輸入 `gulp babelify`<br>
gulp 就會把 `src` 中所有的 js 檔當做來源<br>
接著用 `babel` 將他們全部轉譯成 ES5 的 code<br>
最後再輸出到 `build` 裡面去<br>
這部分需要用到這些模組<br>

```json
{
    "babel-plugin-transform-runtime": "^6.12.0",
    "babel-preset-es2015": "^6.13.2",
    "babel-preset-es2016": "^6.11.3",
    "babel-preset-es2017": "^6.14.0",
    "gulp-babel": "^6.1.2"
}
```

### Error Handling

<img src="http://i.imgur.com/9QmkGN7.jpg" width="400">

雖然上面那段配置已經可以把 code 轉成 ES5<br>
但如果在轉譯的過程中有錯誤(可能語法錯誤)<br>
那就必須把錯誤輸出<br>
所以要在 babel 之後加上這一段<br>

```js
.on('error', function(err){
    console.log(err.stack);
    this.emit('end');
})
```

這樣就如果有錯誤就會輸出<br>
像是這樣<br>

```
SyntaxError: src/index.js: Unexpected token (4:5)

  2 | 
> 3 | func(;
    |      ^
  4 | 
  5 | 
```

### Source Map

<img src="http://i.imgur.com/Fmeb6Gf.jpg" width="500">

寫程式有時候總是會遇到錯誤需要 debug<br>
如果程式發生錯誤會告訴你發生在哪一行<br>

```
build/index.js:5
throw new Error('error');
^

Error: error
    at Object.<anonymous> (build/index.js:5:7) <-- 仔細看這行
    at Module._compile (module.js:541:32)
    at Object.Module._extensions..js (module.js:550:10)
    at Module.load (module.js:458:32)
    at tryModuleLoad (module.js:417:12)
    at Function.Module._load (module.js:409:3)
    at Module.require (module.js:468:17)
    at require (internal/module.js:20:19)
    at Object.<anonymous> (index.js:2:1)
    at Module._compile (module.js:541:32)
```

他告訴我們在 `build/index.js` 的第五行發生錯誤<br>
但 `build` 內的程式碼是 babel 產生的所以會很醜<br>
轉譯過後的程式碼跟原本的有很大的差異<br>
<br>
舉個例子<br>
轉譯前<br>

```js
// before.js

async function hello(){
    return 'Hello World';
}

hello();
```

轉譯後(真的有夠醜XD)<br>

```js
// after.js

var _regenerator = require('babel-runtime/regenerator');
var _regenerator2 = _interopRequireDefault(_regenerator);
var _asyncToGenerator2 = require('babel-runtime/helpers/asyncToGenerator');
var _asyncToGenerator3 = _interopRequireDefault(_asyncToGenerator2);

var hello = function () {
    var _ref = (0, _asyncToGenerator3.default)(_regenerator2.default.mark(function _callee() {
        return _regenerator2.default.wrap(function _callee$(_context) {
            while (1) {
                switch (_context.prev = _context.next) {
                    case 0:
                        return _context.abrupt('return', 'Hello World');

                    case 1:
                    case 'end':
                        return _context.stop();
                }
            }
        }, _callee, this);
    }));

    return function hello() {
        return _ref.apply(this, arguments);
    };
}();

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }
hello();
```

這樣會讓我們很難 debug<br>
我們想知道的是他錯在 source code 的第幾行<br>
這時候就要用 source map 了<br>
<br>
Source Map 會儲存著轉換前後程式碼的位置<br>
可以自動做對應<br>
我們只需要在裡面加上 sourcemaps<br>
現在的 `gulpfile.js` 長這樣<br>

```js
var gulp = require('gulp');
var babel = require('gulp-babel');
var sourcemaps = require('gulp-sourcemaps');

gulp.task('babelify', function(){
    return gulp.src('src/**/*.js')
        .pipe(sourcemaps.init()) // <--- 初始化 source map  
        .pipe(babel({
            presets: ['es2015', 'es2016', 'es2017'],
            plugins: [
                [
                    "transform-runtime", {
                        "polyfill": false, 
                        "regenerator": true
                    }
                ]
            ]
        }))
        .on('error', function(err){
            console.log(err.stack);
            this.emit('end');
        })
        .pipe(sourcemaps.write({   // 
            includeContent: false, // 這邊寫入 source map 
            sourceRoot: 'src'      // source root 設為 'src' 
        }))                        //
        .pipe(gulp.dest('build'))
});
```

這樣 babel 在轉譯的過程中就會自動產生 source map 了<br>
但還有一個問題就是 node 本身並不支持 source map<br>
所以需要用模組 source-map-support 來載入 source map<br>
用法很簡單只要在程式起點加上一行就可以了<br>

```js
// index.js

require('source-map-support').install(); // 加上這一行
require('./build/index');
```

再跑一次已經可以顯示出 source code 中錯誤發生地方<br>

```
src/index.js:3
throw new Error('error');
      ^
Error: error
    at Object.<anonymous> (src/index.js:3:7) <-- 仔細看這行 
    at Module._compile (module.js:541:32)
    at Object.Module._extensions..js (module.js:550:10)
    at Module.load (module.js:458:32)
    at tryModuleLoad (module.js:417:12)
    at Function.Module._load (module.js:409:3)
    at Module.require (module.js:468:17)
    at require (internal/module.js:20:19)
    at Object.<anonymous> (index.js:2:1)
    at Module._compile (module.js:541:32)
```

可以看出是在 `src/index.js` 的第三行發生錯誤<br>
這部份會用到這兩個模組

```json
{
    "gulp-sourcemaps": "^1.6.0",
    "source-map-support": "^0.4.2"
}
```

---

### Watch

現在已經完成大部分功能了<br>
但每次修改 `src` 中的檔案都要重新 `gulp babelify` 才能轉譯很麻煩<br>
所以想讓 gulp 自動監控檔案<br>
有任何變化就進行轉譯<br>
要達到這個效果使用的是 gulp 的 watch 功能<br>
在 `gulpfile.js` 中加上這一段<br>

```js
gulp.task('watch', function(){
    return gulp.watch(['src/**/*.js'], ['babelify']);
});

gulp.task('default', ['babelify', 'watch']);
```

這樣 gulp 就會自動監控 `src` 中所有 js 檔<br>
有任何變動就進行 babelify<br>

---

## 心得

這個環境真的滿方便的 (自己說XD<br>
只要在 command line 上跑 `gulp`<br>
gulp 就會自動 babelify 並且監控 js 檔<br>
<br>
如果覺得照上面的步驟慢慢做很麻煩<br>
這邊有現成的 [github repo](https://github.com/Larry850806/nodejs-project)<br>
也歡迎任何意見或 Pull Request<br>
