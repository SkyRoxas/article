# WordPress 學習筆記： 將 php 的 Variable 傳遞給 javascript 檔案

## 前言：

如標題，在做任何應用的開發，對工程師而言這種需求並不少見，而在 WordPress 開發中，更是常見，因為很多 WordPress 的 Variable 與 Method ， 都是經 php 開發後所提供

## 情境：

最典型的例子就是 [get_template_directory_uri( )](https://developer.wordpress.org/reference/functions/get_template_directory_uri/) 了 ：

這是 WordPress 用來回傳 **版型根目錄** 所提供的 Method，在 JavaScript 的開發中，尤其是在 Ajax 的應用，往往都會需要 **檔案目錄的路徑** 來協助提取我們要的資料

那如何將 [get_template_directory_uri( )](https://developer.wordpress.org/reference/functions/get_template_directory_uri/) return 的結果回傳給 JavaScript 呢 ？

## 方法一：Ajax

你可以使用 WordPress 提供的 Ajax 的方法 [wp ajax (action)](https://codex.wordpress.org/Plugin_API/Action_Reference/wp_ajax_(action))，來使用 Ajax 方式向 WordPress 的 php 檔案來提取

> 在此不對這方法做詳細的說明，因為真的沒必要阿～～～

## 方法二：全域變數

直接在 php 檔案的 header 裡加入：

```{.language-html}
<script>
  uri = <?php get_template_directory_uri()?>
<script>
```

恩～～

![](images/01.jpg)

這方法是我無意間 google 到的 ， 雖然不得不說這方法的確可行，而且也很簡單，容易理解，但在開發時可能會碰到一些你預期不到的問題

- 不是所有的 JavaScript 檔案都需要此變數
- 不易管理
- 檔案載入順序的問題

## 方法三：使用 WordPress 提供的方法

此需求如此常見，所以 WordPress 貼心的提供了一個方法：[wp_localize_script()](https://codex.wordpress.org/Function_Reference/wp_localize_script)，來解決此需求：

[wp_localize_script()](https://codex.wordpress.org/Function_Reference/wp_localize_script) 為 wp_enqueue_scripts 的 action 中提供的方法，其主要功能就是將 php 的 Variable 傳遞給以 [註冊的JavaScript腳本](https://bonze.tw/wordpress-%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98%EF%BC%9A-%E5%A6%82%E4%BD%95%E5%9C%A8-wordpress-%E4%B8%AD-%E5%BC%95%E5%85%A5-javascript-%E6%AA%94%E6%A1%88/)

### Method :

```(.language-php)
<?php wp_localize_script( $handle, $name, $data ); ?>
```

### Parameters :

$handle：

- 類型：String (必填)
- 說明：以註冊的腳本名稱

$name ：

- 類型：String (必填)
- 說明：傳遞給 JavaScript 的 Variable 名稱

$data ：

- 類型：Array (必填)
- 說明：傳遞給 JavaScript 的 Variable 的值

### Example:

```{.language-php}
<?php
  //註冊腳本
  wp_register_script( 'myscript', 'path/to/myscript.js' );

  //遞值
  wp_localize_script('myscript','url',array(get_template_directory_uri()))

  //引用腳本
  wp_enqueue_script( 'myscript' );
?>
```

> 除非特定需求，本人還是比較建議能用系統本身的方法來處理這項需求
