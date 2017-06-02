# wordpress學習筆記：使用jQuery實做ajax無限捲軸

## 目的：
使用jQuery在 **WordPress** 中實做ajax無限捲軸

參考文章：[getting-loopy-ajax-powered-loops-with-jquery-and-wordpress--wp](https://code.tutsplus.com/articles/getting-loopy-ajax-powered-loops-with-jquery-and-wordpress--wp-23232)

## 一、前製準備：

- 在網站版型目錄下新增一個 .php 的 file （此以 loopHandler.php 為此 php 檔案做命名）
- 在網站版型目錄下新增一個 js 目錄以存放所需的 js 檔案
- 於 剛新增的 js 目錄下新增一個 .js 的 file （此以 ajaxLoop.js 為此 js 檔案做命名）

> 在 WordPress 中引入 javascript 檔案 需要在 **function.php** 中進行引入：

在引入 ajaxLoop.js 前我們要先引入 jQuery 以使用 jQuery 提供 ajax 的方法：

```{.language-php}
<?php

function add_jquery(){
  wp_enqueue_script('jquery', 'http://ajax.googleapis.com/ajax/libs/jquery/1.7/jquery.min.js');
}

add_action( 'wp_enqueue_scripts', 'add_jquery' );

?>
```

接著就引入 ajaxLoop.js 的檔案：

```{.language-php}
<?php

function add_custom_scripts() {
  wp_enqueue_script('ajaxLoop', get_template_directory_uri() . '/js/ajaxLoop.js');
}

add_action( 'wp_enqueue_scripts', 'add_custom_scripts' );

?>
```

## 二、使用 jQuery ajax 在 WordPress 完成資料傳輸：

## loopHandler.php：

> 先從我們要取得的資料進行下手

### Step 1：

引入 **wp-load.php** ：

**wp-load.php** 是在 **WordPress** 中相當重要的核心的一部分，在位於**網站根目錄中**，你必須引入它才可以使用我們經常在 **WordPress** 的 shortcode 與包含 **wp_query()** 等重要方法與參數

```{.language-php}
<?php  
  require_once('../../../wp-load.php');//路徑寫法僅供參考
?>
```

### Step 2：

使用 **wp_query()** 去向 **Wordpress** 我們要索取資料：

**wp_query()** 的參數詳細可以到他的[網站](https://codex.wordpress.org/Class_Reference/WP_Query)查詢，這邊只先把第一頁所有的 post 資料給撈出來

```{.language-php}
<?php
    require_once('../../../wp-load.php');

    //new 一個新的 WP_Query 查詢
    $the_query = new WP_Query(
      array(
        'post_type'                => 'post',
        'paged'                    => 1
        //'category_name' => 'l',
        // 'posts_per_page'           => $posts_per_page,
        // 'offset'                   => $offset + ($posts_per_page*$page),
        // 'order'                    => $order,
        // 'orderby'                  => $orderby,
        // 'post_status'              => $post_status,
        // 'ignore_sticky_posts'      => true,
      ));
  ?>
```

### Step 3：

使用 **wp_query()** 與 **Wordpress** shortcode 將我們要的架構打印出來：

```{.language-php}
<?php
    require_once('../../../wp-load.php');

    $the_query = new WP_Query(
      array(
        'post_type'                => 'post',
        'paged'                    => 1
        //'category_name' => '',
        // 'posts_per_page'           => $posts_per_page,
        // 'offset'                   => $offset + ($posts_per_page*$page),
        // 'order'                    => $order,
        // 'orderby'                  => $orderby,
        // 'post_status'              => $post_status,
        // 'ignore_sticky_posts'      => true,
      ));
?>

<!--打印想要顯示出的資料架構-->
<?php if($the_query->have_posts()) :?>
  <?php while($the_query->have_posts()) : $the_query->the_post(); ?>

      <div class ='article'>
          <div><?php the_title(); ?></div>
      </div>

  <?php endwhile; ?>
  <?php wp_reset_postdata();//reset wp_query() 查詢 ?>
<?php endif; ?>
```

### Step 4：

最後在加上一個變數 $page ：

$page 這個變數會在 ajaxLoop.js 檔案中從藉由 jquery ajax 的 data屬性來傳值，過來， 講白一點：就是 ajaxLoop.js 會將 **頁碼** 這個參數傳過來告訴 loopHandler.php 裡的 **wp_Query()**，說他要 **第幾頁** 的資料

```{.language-php}
<?php
    require_once('../../../wp-load.php');

    $page = (isset($_GET['pageNumber'])) ? $_GET['pageNumber'] : 0;
    //宣告 $page 變數： 如果有 get到 ajax data 傳的 pageNumber資料，$page 就 = pageNumber的資料，沒有了話就 ＝ 0

    $the_query = new WP_Query(
      array(
        'post_type'                => 'post',
        'paged'                    => $page //並將其帶入 WP_Query 的 paged 中
        //'category_name' => '',
        // 'posts_per_page'           => $posts_per_page,
        // 'offset'                   => $offset + ($posts_per_page*$page),
        // 'order'                    => $order,
        // 'orderby'                  => $orderby,
        // 'post_status'              => $post_status,
        // 'ignore_sticky_posts'      => true,
      ));
?>

<!--打印想要顯示出的資料架構-->
<?php if($the_query->have_posts()) :?>
  <?php while($the_query->have_posts()) : $the_query->the_post(); ?>

      <div class ='article'>
          <div><?php the_title(); ?></div>
      </div>

  <?php endwhile; ?>
  <?php wp_reset_postdata();//reset wp_query() 查詢 ?>
<?php endif; ?>
```

到這裡，我們要撈取的 loopHandler.php 資料已經設定的差不多了，接下來我們將從 loopHandler.php 換到 ajaxLoop.js

## ajaxLoop.js：

> jQuery ajax 請求資料

### Step 1：

建立變數與立即涵式：

```{.language-javascript}
jQuery(function ($) {

  var $page = 2  //顯示的頁碼
  var $element = $('.ajax-bottom') //觸發 ajax 事件的物件

})
```

### Step 2：

建立 ajax 事件：

建立 ajax 事件，向 loopHandler 請求所需要的資料，並藉由 ajax{data} 將參數遞給 loopHandler.php ， 告知他需要請求的 **頁碼**

```{.language-javascript}
jQuery(function ($) {

  var $page = 2
  var $element = $('.ajax-bottom')

  var load_posts = function () {
    $.ajax({
      type: 'GET',
      data: {pageNumber: page} //將 pageNumber資料傳給 loopHandler.php ,
      dataType: 'html',
      url: 'wp-content/themes/theme-name/loopHandler.php' // 路徑僅供參考,
      beforeSend: function () {
      },
      success: function (data) {
        console.log('success')
        console.log(data)
      },
      error: function (jqXHR, textStatus, errorThrown) {
        console(jqXHR + ' :: ' + textStatus + ' :: ' + errorThrown)
      }
    })
  }

  load_posts();
})
```

到這邊，如過你 console.log 的資料是如你預期的，那恭喜你 jQuery ajax 在 WordPress 完成資料傳輸，就已經告了一段落，你可以開始對 ajax 取得的 data ， 與 ajax success的事件進行任何操作，讓我們繼續往下看

## 三、實做 ajax 無限捲軸：

### Step 1：

在 ajax success 的事件中，進行操作：

將 ajax 取得資料加在觸發物件的前面 ： [.before()](http://api.jquery.com/before/)

```{.language-javascript}
jQuery(function ($) {

  var $page = 2
  var $element = $('.ajax-bottom')

  var load_posts = function () {
    $.ajax({
      type: 'GET',
      data: {pageNumber: page} //將 pageNumber資料傳給 loopHandler.php ,
      dataType: 'html',
      url: 'wp-content/themes/theme-name/loopHandler.php' // 路徑僅供參考,
      beforeSend: function () {
      },
      success: function (data) {
        $content.before(data) //將 ajax 取得資料加在觸發物件的前面
      },
      error: function (jqXHR, textStatus, errorThrown) {
        console(jqXHR + ' :: ' + textStatus + ' :: ' + errorThrown)
      }
    })
  }

  load_posts();
})
```

### Step 2：

綁定 scroll 事件：

這邊要注意的地方就是 $page++ 了，這段是這邊最重要的地方，因為總不能回來的資料都是第一頁吧，所以在這邊使用$page++，而 scroll event裡的判斷，折是根據座標位置判斷觸發物件是否在畫面內

```{.language-javascript}
jQuery(function ($) {

  var $page = 2
  var $element = $('.ajax-bottom')

  var load_posts = function () {
    $.ajax({
      type: 'GET',
      data: {pageNumber: page} //將 pageNumber資料傳給 loopHandler.php ,
      dataType: 'html',
      url: 'wp-content/themes/theme-name/loopHandler.php' // 路徑僅供參考,
      beforeSend: function () {
      },
      success: function (data) {
        $content.before(data) //將 ajax 取得資料加在觸發物件的前面
      },
      error: function (jqXHR, textStatus, errorThrown) {
        console(jqXHR + ' :: ' + textStatus + ' :: ' + errorThrown)
      }
    })
  }

// add scroll event
  $(document).ready(function () {
    $(window).scroll(function () {
      if ($(window).scrollTop() > $content.offset().top - $(window).height()) {
        $page++
        load_posts()
      }
    })
  })
})
```

### Step 3： 最後可以在 beforeSend 事件中，插入 loading 的圖片來騙一下，並在 success 事件中移掉：

```{.language-javascript}
jQuery(function ($) {

  var $page = 2
  var $element = $('.ajax-bottom')

  var load_posts = function () {
    $.ajax({
      type: 'GET',
      data: {pageNumber: page}  ,
      dataType: 'html',
      url: 'wp-content/themes/theme-name/loopHandler.php',
      beforeSend: function () {
        $content.after('<div id="temp_load" style="text-align:center">\
                            <img src="../images/ajax-loader.gif" />\
                            </div>'); //加入 loading.gif圖
      },
      success: function (data) {
        $("#temp_load").remove(); //成功仔入後移除 loading.gif圖
        $content.before(data)
      },
      error: function (jqXHR, textStatus, errorThrown) {
        console(jqXHR + ' :: ' + textStatus + ' :: ' + errorThrown)
      }
    })
  }

// add scroll event
  $(document).ready(function () {
    $(window).scroll(function () {
      if ($(window).scrollTop() > $content.offset().top - $(window).height()) {
        $page++
        load_posts()
      }
    })
  })
})
```

至於 fadein 的效果就憑前端的功力摟～

## 結語：

接下來我要來說一些，跟我一樣努力到這一部的你不愛聽的話了！

直接開門見山的說～我們在 loopHandler.php 裡引用的 **wp-include.php** ， 剛剛也說過他是 **WordPress** 核心的一部分，可是有沒有發現？ 我們現在用的工具就是 **Wordpress** 阿～～，為什麼要等到 **WordPress** 跑完之後在跑一遍 **WordPress** 的核心，而且我們上面的作法，會變成說每 ajax 一次，就會執行一次 **Wordpress的核心**，光到這邊就充滿了各種不合理，而且對網站效能的影響也是不可忽視的～～在開發時也會碰到很多非預期錯誤。

不過也不用氣餒（這邊是在說些安慰自己的話）， 至少藉由上述步驟，你也學到了一種方法，和一些 **Wordpress** 的觀念，尤其重要的是 **使用工具的經驗** ，盡量使用工具提供的方法和工具，通常你想到的事情，工具都應該要有提供對應的工具和方法才是合理的。

而在這篇教學的部份其實我們只要使用 **WordPress** 提供的 action ， [wp_ajax_(action)](https://codex.wordpress.org/Plugin_API/Action_Reference/wp_ajax_(action)) 就可以了～
