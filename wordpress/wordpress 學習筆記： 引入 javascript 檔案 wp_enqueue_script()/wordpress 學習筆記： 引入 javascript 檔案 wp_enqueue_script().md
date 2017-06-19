# WordPress 學習筆記： 如何在 WordPress 中 引入 JavaScript 檔案

## 前言 ：

如果你是有一般 Html 跟 JavaScript 基礎你應該都知道，我們在 Html 上開發 JavaScript 時之前 ， 除了直接在 **script** 標籤裡直接開發外，就直接用以下方式引入 JavaScript 的檔案：

```{.language-html}
<script src="myscripts.js"></script>
```

雖然使用這種方法在 WordPress 中也是行得通，不過你可能會碰到以下的問題：

- 檔案路徑的問題
- 因為 header.php 與 footer.php 重複使用 ， 導致 js 在不必要時候載入

## 使用 WordPress 內建方法 ：

WordPress 本身就有提供 引入 JavaScript 的 **Action** 與對應的 **Method**

Action :

- [wp_enqueue_scripts](https://codex.wordpress.org/Plugin_API/Action_Reference/wp_enqueue_scripts)

Method :

- [wp_register_script()](https://developer.wordpress.org/reference/functions/wp_register_script/)
- [wp_enqueue_scripts()](https://developer.wordpress.org/reference/functions/wp_enqueue_script/)

簡單來說，WordPress 它在引入 JavaScript 檔案時 ， 是在 **wp_enqueue_scripts** 這個 Action 下執行 ， 也就是名為 **wp_enqueue_scripts** 的動作中，會使用上方的 **Method** 來達到引入 JavaScript 檔案的功能

> 你可以藉由此[連結](https://codex.wordpress.org/zh-cn:%E6%8F%92%E4%BB%B6_API)來理解 WordPress action 的機制

## wp_register_script() :

> 註冊腳本 Register a new script

### Method :

```{.language-php}
<?php wp_register_script($handle,$src,$deps = array(),$ver = false,$in_footer = false) ?>
```

### Parameters ：

$handle：

- 類型：String(必填)
- 說明：腳本名稱

$src :

- 類型：String(必填)
- 說明：腳本路徑

$deps :

- 類型：Array
- 說明：此註冊腳本依賴的其他以註冊的腳本
- 預設值：array()

$ver ：

- 類型：(string|bool|null)
- 說明：此腳本可 WordPress 支援的版本號
- 預設值 ： false (現在套用 WordPress 的版本號)

$in_footer ：

- 類型：bool
- 說明：此腳本是否於 footer 載入
- 預設值 ： false (於 header 載入)

### Exemple :

```{.language-php}
wp_register_script('myscripts', get_template_directory_uri() . '/js/myscripts.js',array ('jquery', 'jquery-ui'),false,false);
```

> 不得不說， WordPress 在這部份真的非常的貼心，你可點這個[連結](https://developer.wordpress.org/reference/functions/wp_register_script/#core-registered-scripts)查看，它已經幫你註冊好許多我們在開發 JavaScript 常常需要的程式與套件，我們可以藉由 wp_register_script() 等方法直接套用引入，如：jQuery jQuery-ui 等等...

到目前為止，我們只是純在 WordPress 裡註冊自己的 JavaScript 檔而已，並還沒有做調用的動作

那該如何調用？ 我們繼續往下看


## wp_enqueue_script() :

> 將以註冊的 script 加入要執行script的隊列 Enqueue a script

### Method :

```{.language-php}
<?php wp_enqueue_script($handle,$src,$deps = array(),$ver = false,$in_footer = false) ?>
```

### Parameters ：

wp_enqueue_script() 與 wp_register_script() 的參數基本上都是一樣的，這邊就不在特別說明，只有一點要注意的是：

> wp_enqueue_script() 只有 $handle 這個參數是必填的

### Exemple :

將以註冊的 js file 加入執行 script 的隊列
```{.language-php}
wp_enqueue_script('myscripts');
```

或是以下方式：將以註冊或未註冊的 js file 加入執行 script 的隊列
```{.language-php}
wp_enqueue_script('myscripts', get_template_directory_uri() . '/js/myscripts.js',array ('jquery', 'jquery-ui'),false,false);
```

> 當然 wp_enqueue_scripts 底下提供的 Method 絕對不只這篇文章提的這兩個，後續有機會會在補充

## 在 functions.php 裡調用：

剛剛在上面幾個 example code 如果你是直接複製貼在 functios.php 檔案裡執行，是會有錯誤的，這牽扯到 WordPress 執行順序的問題，因為有點複雜本人目前也還沒完全理解，所以這部份先不多說明：

### 解法：
```{.language-php}

function add_custom_scripts()
{
  wp_enqueue_script('myscripts', get_template_directory_uri() . '/js/myscripts.js',array ('jquery', 'jquery-ui'),false,false);
}

add_action('wp_enqueue_scripts', 'add_custom_scripts');
```
### 說明 ：
剛剛在前面有提到 wp_register_script() 與 wp_enqueue_script() 必須是要在 wp_enqueue_script 的 action 底下執行，所以我們使用 add_action() 的方式，來確保這幾段 code 是可以在 wp_enqueue_scripts 底下裡執行的

## 另外一提：

### WordPress 新手可能會碰到的問題 :

如果你剛剛接觸 WordPress Theme 的開發，按上述的方法試，還是沒反應了話，你可能遺漏了下方的步驟 :

將下方 code 加入頁首
```{.language-php}
  <?php do_action('wp_head');?>
```

將下方 code 加入頁尾
```{.language-php}
  <?php do_action('wp_footer');?>
```
