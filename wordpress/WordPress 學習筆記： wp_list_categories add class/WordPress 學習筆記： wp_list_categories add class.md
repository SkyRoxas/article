# WordPress 學習筆記： wp_list_categories add class

## 情境 ：

**分類列表** 在 Web 中非常的常見，而 WordPress 在針對 **分類列表** 處理上 ， 提供了一個方法叫做 [wp_list_categories（）](https://developer.wordpress.org/reference/functions/wp_list_categories/) ， 這也是我在 WordPress 中第一次製作 **分類列表** Google 關鍵字 **wordpress categories list** ，第一筆就找到的，[wp_list_categories（）](https://developer.wordpress.org/reference/functions/wp_list_categories/) 這個方法在使用上十分的容易，也提了許多的屬性 （例如：層級、排序、還有分類文章總數...等等）

不過我很快就遇到了問題......

## 問題 ：

在製作 WordPress Theme 時 ，個人都是搭配 **BootStrap** 進行前端的開發，所以時常需要直接針對特定的 **Html 元素** 加入特定的 **Class**，一方面也藉由此方法來維持 code 的一致性 與整潔性。

可是 [wp_list_categories（）](https://developer.wordpress.org/reference/functions/wp_list_categories/)並沒有提供這方面的功能，它會直接產生出它已經預設好的 Html 結構

> 在此並沒有說 [wp_list_categories（）](https://developer.wordpress.org/reference/functions/wp_list_categories/) 不好用，只是單純沒有我想要的功能而已

## 解決方法 ：

### 1、 jQuery :

直接透過 `addClass()` ， 等方法直接去針對 `wp_list_categories（）` 產生後的元素進行操作

Example :

```{.language-javascript}
jQuery(document).ready(function($) {
    $('li.cat-item:has(ul.children)').addClass('i-have-kids');
});
```

### 2、 get_categories () :

[get_categories( )](https://developer.wordpress.org/reference/functions/get_categories/) 是 WordPress 提供的方法，專門回傳查詢後的分類列表的物件

> Retrieve list of category objects.

當然 `get_categories()` 也跟 `wp_list_categories()` ，有著幾乎相同的屬性與查詢方法，其實你去解析下 `wp_list_categories()` 的原始碼，你就會發現它裡面也是有使用 `get_categories()` 的方法的

再來，當我們可以使用 `get_categories()` 取得到對應的 `category objects` 時，你可以直接用 PHP 提供的方法 [foreach](http://php.net/manual/en/control-structures.foreach.php)，直接針對物件的操作，來吐出我們需要的架構

Example :

```{.language-php}
  <?php
          // query category
          $categories = get_categories( array(
            'orderby' => 'name',
            'parent' => 0
          ));
          foreach ( $categories as $category ) {
            echo '<li>';
              echo '<a class ="categories-link" href="' . get_category_link( $category->term_id ) . '" >' . $category->name . '</a>';
              echo '<ul>';
                  // query sub category
                  $sub_categories = get_categories(array(
                    'orderby' => 'name',
                    'parent' => $category->term_id,
                  ));

                  foreach ( $sub_categories as $sub_category ) {
                      echo '<li class ="categories-item">
                              <a class ="categories-link" href ="' . get_category_link( $sub_category->term_id ) . '">'.$sub_category->name.'</a>
                            </li>';
                    }
              echo "</ul>";
            echo "</li>";
          }
    ?>
```

## 結語：

第 2 種方法可以讓我們自訂我們自己想要的架構，方便前端可以進行客製與加入自己想要的 **class**

也是個人比較推薦的解決發法，畢竟也比較接近使用 WordPress 的原生工具，比較不建議直接使用 jQuery 的方式進行修改

原因如下：

* document 載入順序的問題
* jQuery 執行速度會在 doucment 載入之後，可能造成今入網頁時會跑板
* 增加了執行 jQuery code 的效能
* 增加了要多維護的
* 真的很能保證 WordPress 更新後，`wp_list_categories（）`產出的架構不會換阿
