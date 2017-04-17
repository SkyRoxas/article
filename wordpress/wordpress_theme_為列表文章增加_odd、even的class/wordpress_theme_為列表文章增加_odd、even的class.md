# WordPress 學習筆記 ：文章列表加入'even'與'odd'的class

## 目的：

為文章列表項目以基數偶數區分加入 odd 與 even 的 class 已達到更客製化的前端效果

## 作法：

## 1、使用函數：

### WP_Query 裡的 $current_post 屬性：

#### 取於wordpress官方屬性說明：

> (available during The Loop) Index of the post currently being displayed.

直接翻譯來說就是顯示於迴圈內的文章索引，從 0 開始 1、2、3、4、5、6、7.....照順序排列

## 2、於 have_posts() 迴圈裡使用 $current_post 屬性進行運算

### 於 have_posts()迴圈內：

```
<?php if(have_posts()) : ?>
<?php while(have_posts()) : the_post(); ?>

  //codding for this place

<?php endwhile; ?>
<?php endif; ?>
```

### 使用 $current_post 屬性進行運算

```
<?php
//建立變數
$queyClass;

//使用 current_post 取與2相除的餘數，以判斷奇數與偶數
if ($wp_query->current_post % 2 == 0):
    //odd class
    $queyClass = 'odd';
else:
    //even class
    $queyClass = 'even';
endif

?>
```

## 3、將變數帶入與指定元素的 class

### 完整 codding 如下所示：

```
<?php if(have_posts()) : ?>
<?php while(have_posts()) : the_post(); ?>

  $queyClass;

  if ($wp_query->current_post % 2 == 0):
      //odd class
      $queyClass = 'odd';
  else:
      //even class
    $ queyClass = 'even';
    endif
  ?>

<div class ="item-list <?php echo $queyClass?>">
  <div class ="article-field title">
    <h1><?php the_title(); ?></h1>
  </div>
</div>

<?php endwhile; ?>
<?php endif; ?>
```

## 另外一提：

此作法只要自建的變數於迴圈內可以給予迴圈內任何的 html 元素指定的 class 名稱
