# Javascripts JSON.Stringify() 與 JSON.Pares()

## 什麼是 JSON :

JSON 是個以純文字為基底去儲存和傳送簡單結構資料，你可以透過特定的格式去儲存任何資料(字串,數字,陣列,物件)，也可以透過物件或陣列來傳送較複雜的資料。一旦建立了您的 JSON 資料，就可以非常簡單的跟其他程式溝通或交換資料，因為 JSON 就只是純文字個格式

## JSON.Stringify() 與 JSON.Pares() :

上述的 JSON 是一個型別（Type），是一個 Array Type。我們不能儲存或傳送「Type」，所以要將 Type 轉成字串（String）後，才能儲存或傳送。

例如，對電腦來說，這是一個物件（Object）：

```
{ "name": "James" }
```

這個物件轉成字串：

```
"{ \"name\": \"James\" }""
```

對電腦來說，這才是字串。所以，將 JSON 物件（Object）轉成字串後，才能儲存或傳送。這個動作就叫 JSON Stringify（字串化）。當然，字串化過的 JSON 字串，要使用時，也要解析（Parse）回物件。
