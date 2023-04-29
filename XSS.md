# XSS
## 成因
* 網站未驗證使用者輸入，使攻擊者可以插入惡意程式碼，並於使用者前端執行。
## 影響
* 藉由惡意程式取得使用者資訊(ex. cookie)。
## 攻擊方法
### 反射型
* 於請求中插入惡意程式，直接跟著回應回傳。(ex. 搜尋結果)
```
https://insecure-website.com/search?item=<script>alert(1)</script>
```
```
<p>search for: <script>alert(1)</script></p>
```
### 儲存型
* 惡意程式儲存至伺服器資料庫中，伺服器從資料庫取出惡意程式，惡意程式回傳到前端。(ex. 留言板)
```
POST
...
message=<script>alert(1)</script>
```
```
<p>message:<script>alert(1)</script></p>
```
### DOM型
* 來自客戶端程式碼，客戶端的程式碼將輸入直接寫入網頁中。(ex. 搜尋結果)
```
var search = document.getElementById('search').value;
var results = document.getElementById('results');
results.innerHTML = 'You searched for: ' + search;
```
## 防禦方法
* 阻止攻擊者插入惡意程式: 驗證輸入，對輸入進行編碼。
* 阻止惡意程式碼被執行: 設定 CSP(Content Security Policy)，僅能同源的 js 才能執行。
* 降低 XSS 攻擊之損害: 
    * 避免攻擊者用受害者的身份登入: 避免被偷 cookie，設定 `HTTP Only` 使 js 無法取得 cookie、設定 `Secure` 使 cookie 只能在 https 下傳遞。
    * 避免攻擊者透過 XSS 進行比較重要的操作: 重要操作需要多因素驗證。

## 參考
* [資安這條路 10 - [跨站腳本漏洞] Store XSS , Relate XSS , DOM XSS](https://ithelp.ithome.com.tw/articles/10243967)
* [淺談 XSS 攻擊與防禦的各個環節](https://tech-blog.cymetrics.io/posts/huli/xss-attack-and-defense/)
* [portswigger-Cross-site scripting](https://portswigger.net/web-security/cross-site-scripting)
---
###### tags: `web security`