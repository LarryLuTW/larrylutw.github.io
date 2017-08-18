---
layout: post
title: '[實用] 新一代的編輯器 - VSCode'
keywords: vscode, visual studio code, visual studio, microsoft, editor, code, edit, editing, 編輯器, 代碼, 程式碼, 編輯, 微軟, redefined, 重新定義, coding, atom, vim, vs, emacs, 跨平台, multi platform
published: true
---

俗話說 __「工欲善其事，必先利其器」__<br />
對於工程師來說好的編輯器是很重要的，因為最近剛好很多朋友在問我用什麼編輯器，所以就寫一篇文章來~~推坑~~介紹 vscode。<br />

<img src="/public/vscode-introduction/vscode.png" width="600">

在開始之前先澄清一下，VSCode 跟微軟那套 Visual Studio 一點關係都沒有，而且 <a href="https://github.com/Microsoft/vscode" target="_blank">VSCode</a> 是完全開源的，社群也非常活絡。

---

以下先介紹 10 個我覺得 VSCode 很強大的地方：

### 1. color picker

看到色碼時不知道顏色，或是寫 css 時不知道去哪裡挑顏色，不用再 google 找色碼表，直接用內建的 color picker 就可以了~

<img src="/public/vscode-introduction/color_pad.png" width="600">

<br />

### 2. Git 整合

還在用`git diff`看這次到底修改了什麼嗎(本人紅綠色盲看了極度痛苦XD)

<img src="/public/vscode-introduction/git_1.png" width="600">

快試試 VSCode 的 diff，連行數都幫你對齊好，改了什麼一目了然

<img src="/public/vscode-introduction/git_2.png" width="600">

<br />

### 3. Emmet

VSCode 內建 Emmet 省下刻 HTML 的時間<br />
~~慢慢敲 HTML 看起來就很弱阿 XDD~~

<img src="/public/vscode-introduction/emmet.gif" width="600">

<br />

### 4. 根據檔名或是引用跳到檔案

<img src="/public/vscode-introduction/jump_to_definition.gif" width="600">

<img src="/public/vscode-introduction/jump_to_file.gif" width="600">

<br />

### 5. 自定義常用的程式碼片段

我前端寫 React.js 所以很常用到`this.setState(() => ({ ... }))`

<img src="/public/vscode-introduction/snippet.gif" width="600">

<br />

### 6. 好看的檔案 icon

不同檔案類型有不同 icon，一看就知道是什麼檔案

<img src="/public/vscode-introduction/folder_file_icon.png" width="600">

<br />

### 7. 強大的 intellisense

不管是變數屬性、或是要帶什麼參數都有很好的提示

<img src="/public/vscode-introduction/intellisense_1.png" width="600">

<img src="/public/vscode-introduction/intellisense_2.png" width="600">

<br />

### 8. multi cursor

多個游標讓你可以一次修改很多地方

<img src="/public/vscode-introduction/multi_cursor.gif" width="600">

<br />

### 9. 自動 format

不用擔心 code 排版太醜，VSCode 可以自動幫你排版<br />
若團隊有制定 coding style 也可以在 vscode 中設定

<img src="/public/vscode-introduction/format.gif" width="600">

<br />

### 10. debug

可以設斷點 debug，查看目前變數值

<img src="/public/vscode-introduction/debug.png" width="600">

---

## 社群

除了功能強大之外，VSCode 也有很龐大的社群，有很多擴充功能還有主題可以選，VSCode 本身每個月也會至少更新一次

<img src="/public/vscode-introduction/extension.png" width="600">

---

## 跟其他編輯器比較

### 1. Vim

在用 VSCode 之前我有很長一段時間都是用 vim，vim 可以做很多客製化，但缺點就是學習門檻還滿高的，還有記不完的快捷鍵XD，隨著 plugin 越裝越多設定檔也會越來越複雜，雖然如此它仍然是很好的編輯器，搭配 <a href="/2017/02/14/tmux/" target="_blank">tmux</a> 用起來也很順，我目前是寫大專案就用 vscode，寫一些 script 或改改東西就用 vim。<br />
在 vscode 上也有 <a href="https://github.com/VSCodeVim/Vim" target="_blank">extension</a> 把 vim shortcut 遷移過來，使用上完全不會有問題。

### 2. Sublime

我自己只用過一小陣子 sublime，對他的印象就是 package manager 滿方便的，啟動速度也很快，但缺點就是很少更新而且沒有開源，所以有 bug 也沒辦法大家送 PR 一起修。如果要跳槽過來 vscode 的話可以試試 <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.sublime-keybindings" target="_blank">Sublime Text Keymap</a> 這個 extension，雖然我是 vim 派的沒有用過，但評價都還不錯。

### 3. Atom

我沒用過 Atom，但因為開源又是 Github 出的所以對他印象一直都不錯，之前看過介面也覺得滿漂亮，不過聽說開啟大文件的速度有點慢(不確定有沒有改善)，因為我目前 vscode 也用得很好，所以短時間內應該不會去試 atom，如果要從 atom 跳到 vscode 的朋友其實介面滿像的，所以不用太過擔心，而且 vscode 開啟的速度很快XD。

---

如果你還在各編輯器之間猶豫不決，我真心推薦用用看 vscode，他大概是微軟做過最成功的專案了XD。其實在用 vscode 之前我一直很討厭微軟，什麼 __Microsoft ❤️ Linux__ 一定都是假的XDD，擁抱開源也只是說說而已。雖然我現在還是不太喜歡微軟，不過真的 vscode 真的很好用～

<style>
    .inline {
        display: inline;
        width: calc(50% - 20px);
    }
</style>

<img class="inline" src="/public/vscode-introduction/ms_love_linux.jpg" width="50%">
<img class="inline" src="/public/vscode-introduction/ms_love_open_source.png" width="50%">

如果有什麼你覺得超神的功能沒寫到可以跟我說<br />
或是有使用上的問題也可以透過⬇下面⬇粉專私訊我
