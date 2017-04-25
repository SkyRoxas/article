# wordpress 學習筆記 : Wordpress REST API (一) : Handbook

## REST API Handbook：

WordPress REST API 提供了 wordpress 網站資料數據的 API ， 使用 Json 的格式與開發者產生 '接收'與"發送"的互動（sending and receiving），This enables developers to create, read and update WordPress content from client-side JavaScript or from external applications, even those written in languages beyond PHP

## Why use the WordPress REST API :

- 任何支援 HTTP requests 與 json 格式的語言都可以藉由 REST API 與 wordpress 做互動 (Node.js to Java and beyond)。
- 也可以取代 wordpress core 裡的 admin-ajax API 以達到更好的結果
- you can more easily structure the way you want to get data into and out of WordPress

## Key Concepts：

> 為了能更清處理解 REST API，我們分開說明的一些 "關鍵概念" 與 "術語"

- Routes/Endpoints
- Requests
- Responses
- Schema
- Controller Classes

## Routes & Endpoints :

在 REST API 中，他的 route 有著 context 的關聯，會根據不同的 URL 產生不同的 HTTP methods，而 個別的 HTTP methods 與所對應 route 的組合 ，我們稱呼它為 Endpoints (端點)

> The mapping of an individual HTTP method to a route is known as an "endpoint".

舉例來說：

假設我們發了一個 GET request 到 <http://bonze.tw/wp-json/> ， 會得到一個 json 的 responses ， 告訴我們什麼 Route 是可用的，且哪條 Route 上哪個 Endpoints 是可行的。 而 /wp-json/本身就是個 Route，當 GET request 發生時，將會產生對應的 Endpoints ， 去顯示 REST API 產生的結果索引。

> /wp-json/ is a route itself and when a GET request is made it matches to the endpoint that displays what is known as the index for the WordPress REST API

後續說明，將學習如何 register 自己的 Route 與 Endpoints

## Requests ：

在 WordPress REST API 基礎架構中有一個主要的類別為： **WP_REST_Request** ， 此類別用來存取當前 Requests 的資訊。

Requests 可藉由 submitted remotely via HTTP 或 wordpress 內部 PHP 的編寫，當對 registered route 發出 HTTP request，將自動生成 WP_REST_Request objects，將 針對 request 中的特定資料，進行判斷且透過 API 取出，There are a lot of neat things you can do using the request class。

## Responses :

當從 API 取得 Responses 的資料 ， **WP_REST_Response** 的類別提供了從 Endpoints response data 互動的方法，可以返回所需的數據，它們也可以用來返回 errors

## Schema ：

每一個 Endpoints Requests 提供的資料結構都有些許的不同，而這些些許的不同已經被定義在 API Schema之中，提供結構化的數據與所有屬性以及可以接受的輸入參數的綜合列表，也提供了一定的安全性，因為它能讓我們段驗證後的 HTTP required 給 API

## Controller Classes :

WordPress REST API 有許多的 moving parts 是必須一起 work 的，Controller classes，將所有的 moving parts 放在一起 ， 使用 controller class 可使你方便管理 registration of routes & endpoints, handle requests, utilize schema, and generate API responses
