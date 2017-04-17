# 前端自動化：Gulp 學習路程

## 本篇目標:

- 初步認識 Gulp (安裝與使用)
- 建立前端自動化安裝環境
- 自訂屬於自己的 Gulp 開發環境

--------------------------------------------------------------------------------

## 初步認識 Gulp：

Gulp 為 Node.js 的套件，可以自訂屬於自己的前端自動化環境，簡單點說可以幫你一次解決：

- Sass、Less 自動編譯為 Css
- 自動加入 "瀏覽器前綴" 給你的 Css 屬性
- 自動幫你編譯 Jade 為 Html
- 壓縮圖片
- 打包壓縮 CSS 與 JS
- 瀏覽器自動重新整理
- .....

以上這些可能已經能省下許多 開發前、開發中、開發後 時特別需要留意花時間的動作

> 凡事需要做兩次以上的事物，都可以有自動化的價值

讓我們繼續往下看~~

## 安裝 Gulp 環境：

> gulp 為 Node.js 的套件，所以安裝 gulp 前請先安裝 Node.js

### 1\. 安裝 node

```
sudo apt-get install node
```

### 2\. 安裝 Npm（Node 模組套件管理器）

```
sudo apt-get install Npm
```

### 3\. 使用 Npm 全域安裝 Gulp

```
npm install -g gulp
```

以上環境建制完畢～～

## 建立 Gulp 專案：

### 1\. 建立一個專案且切換到此目錄底下 (此以 gulp-project 為專案名稱)

```
mkdir gulp-project

cd gulp-project
```

### 2\. Npm 初始化此專案 產生 package.json 的檔案

```
npm init
```

此時 Npm 詢問你一下資訊 如: 專案名稱 、 專案描述 、 作者 、 版本...等等之類有關專案敘述，你也可以不輸入直接 "enter" 按到底～

產出的 package.json 檔案內容如下：

```
{
  "name": "gulp-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
  "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

> 順便一提：<br>
> 接下來在此專案安裝任何 module 時，package.json 都會進行紀錄： 你可以把此專案下的 package.json 複製到其他專案，輸入下方指令今行安裝：

```
npm instll
```

### 3\. 在此專案下安裝 Gulp

```
npm install --save-dev gulp
```

### 4.專安目錄下建立 gulpfile.js 檔案

```
touch gulpfile.js
```

到此步驟已經算是完成了～再來將針對 gulpfile.js 的檔案進行 編譯，來運行我們需要 Gulp 來幫我們運作的事情

> 另外安裝完的 gulp 或是其他它使用 npm instll 安裝的模組，皆會被放置在 node_modules 目錄底下

## 第一個 gulp 程式

接下來我們直接編譯 gulpfile.js 在進行初步的了解

### 建立變數使用 require 引入 gulp plugin

```{.language-javascript}
var gulp = require('gulp');
```

### 使用 task(name,fn) 來定義任務名稱何執行任務的內容

```{.language-javascript}
gulp.task('default', function () {
    console.log('執行 default 的任務');
});

gulp.task('word', function () {
    console.log('執行 word 的任務');
});
```

儲存後～接著來執行看看～

### 執行指定任務

在專案目錄底下輸入：

```
gulp
```

執行結果；

```
[21:55:19] Using gulpfile /var/www/gulp-project/gulpfile.js
[21:55:19] Starting 'default'...
執行 default 的任務
[21:55:19] Finished 'default' after 95 μs
```

在專案目錄底下輸入：

```
gulp world
```

執行結果；

```
[22:00:08] Using gulpfile /var/www/gulp-project/gulpfile.js
[22:00:08] Starting 'word'...
執行 word 的任務
[22:00:08] Finished 'word' after 103 μs
```

> 上面實做結果可得知 Gulp 最 basic 的運行與使方式

> 另外你必須要知道：<br>
> .task('defalut',function()) 中的 'defalue' 的任務名稱指的就是 'Gulp' 本身<br>
> .task('word',function()) 而自己定義的任務名稱使用時前面必須加上 'gulp' 如 'gulp word'

## 使用 Gulp 相關模組來達成前端自動化

再來我們需要做的是就是來撰寫自己的 task 來執行我們想要進行自動化的功能，好運的是大部分的功能都已經被許多牛人和團隊寫成模組，釋出在網路上～如何調用和使用都有個別的說明文件～ 你可以透過 gulp 作為關鍵字去收尋你想自動完成的功能

> 在這裡我使用 gulp-sass 來把 sass 編譯為 css 為例：

### 安裝 gulp-sass

```
npm install gulp-sass --save-dev
```

### 建立變數使用 require 引入 gulp-sass plugin

```{.language-javascript}
var gulp = require('gulp');
var sass = require('gulp-sass'); //引入 gulp-sass plugin
```

### 建立 sass的任務

```{.language-javascript}
gulp.task('sass', function() {
    gulp.src('sass/style.scss')
    .pipe(sass())
    .pipe(gulp.dest('css'));
})
```

### 使用 gulp.src 來指定事件目錄或檔案

```{.language-javascript}
gulp.task('sass', function() {
    gulp.src('sass/style.scss') //指定目錄 sass底下的 style.scss檔案
})
```

### 使用 .pipe 進行串流

```{.language-javascript}
gulp.task('sass', function() {
    gulp.src('sass/style.scss')
    .pipe(sass()) //使用 pipe 將檔案 串接給 gulp-sass 進行編譯
})
```

### 使用 gulp.dest將處理過後的檔案匯出

```{.language-javascript}
gulp.task('sass', function() {
  gulp.src('sass/style.scss')
  .pipe(sass())
  .pipe(gulp.dest('css')); //將匯出的檔案輸出在 css 目錄底下
   })
```

### 完成後在目錄底下建立 sass 資料夾與底下 style.scss 進行編譯

#### sass/style.scss

```{.language-css}
body{
      # header{
              background:#000000;
              }
      }
```

執行 sass task 進行編譯
輸入指令

```
gulp sass
```
執行結果
```
  [22:45:31] Using gulpfile /var/www/gulp-project/gulpfile.js
  [22:45:31] Starting 'sass'...
  [22:45:31] Finished 'sass' after 11 ms
```

執行完後 style.scss 會被編譯成 style.css 至 css 的目錄底下

#### css/style.css

```{.language-css}
body # header{
              background:#000000;
              }
```

> 當然~ gulp-sass 的設定絕對不只這些，在此只先完成它俊根本的運作


## 結語：
先介紹到這裡，相信閱讀完後應該能對 Gulp 有初步的認識，也應該有稍微體驗到 Gulp 的強大和帶來的方便，後續會繼續在增加 Gulp 使用上的經驗和功能方面的文章，再與大家分享～

> 取之於網路，用之餘網路
