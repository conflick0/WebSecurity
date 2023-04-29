# SOP & CORS
## SOP 同源政策
domain-a 的資源 domain-b 無法存取。
### 同源定義
scheme、domain、port 要相同，才算同源。
* scheme: 都是 https 或 http。
* domain: 都是 domain-a.com。
* port: port 號相同，ex. 都是 80。
### 存取規定
* 允許跨來源嵌入(embed): 嵌入 `<script>`、`<link>`
* 允許跨來源寫入(writes): `<form>`表單，可以從 domain-b 發給 domain-a。
* 不允許跨來源讀取(reads): domain-a 不能讀取 domain-b 的 cookie、XMLHttpRequest，Fetch API 也都無法被讀取，會回報錯誤。
## CORS 跨來源資源共用
允許跨來源存取。domain-b 可以存取 domain-a 資源。
### 範例
* domain-b 向 domain-a 發起，請求資源。
```
GET /data/
Host: a.com
Origin: https://b.com
```
* domain-a 回應。 
```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://b.com
```
* 客戶端瀏覽器，會檢查回應中 `Access-Control-Allow-Origin` 欄位，如果與請求中 `Origin` 欄位相同，則會允取程式進行存取。
* `Access-Control-Allow-Origin: *`: 允許任何來源。
## 參考
* [簡單弄懂同源政策 (Same Origin Policy) 與跨網域 (CORS)](https://medium.com/starbugs/%E5%BC%84%E6%87%82%E5%90%8C%E6%BA%90%E6%94%BF%E7%AD%96-same-origin-policy-%E8%88%87%E8%B7%A8%E7%B6%B2%E5%9F%9F-cors-e2e5c1a53a19)
---
###### tags: `web security`