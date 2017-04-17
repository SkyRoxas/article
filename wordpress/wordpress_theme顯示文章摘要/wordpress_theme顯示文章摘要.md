# wordpress theme顯示文章摘要

## excerpt (摘要)

```
<?php the_excerpt(); ?>
```

使用方式如同

```
//文章標題
<?php the_title?>

//文章內文
<?php the_content?>
```

> 另外如果沒有使用 wordpress 預設的 **文章摘要** 區塊，則會直接擷取文章內容的文字使用

## 修改文章摘要後綴文字

wordpress the_excerpt() 原生摘要後綴為 **[...]** 的方式顯示，如果要更改後綴文字方式：

### 在 function.php 檔案裡建立一個 function

```
<?php

function excerpt_more-example(){
      return "read_more";
}

?>
```

### 在使用 wordpress add_filter()方法去做 hook

> **hook :** excerpt_more

```
<?php

add_filter( 'excerpt_more', 'excerpt_more-example' );

?>
```

## 修改文章摘要長度

### 在 function.php 檔案裡建立一個 function

```
<?php

function excerpt_length-example(){
      return 20;
}

?>
```

### 在使用 wordpress add_filter()方法去做 hook

> **hook :** excerpt_length

```
<?php

add_filter( 'excerpt_length', 'excerpt_more-example' );

?>
```
