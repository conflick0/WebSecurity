# SSRF
## 成因
* 服務器端請求偽造 (SSRF)，攻擊者使服務器端應用程序向非預期位置發出請求。
## 影響
* 可能會導致服務器與僅限組織內部之服務建立連接。 
* 可能會強制服務器連接到任意外部系統。
## 攻擊方法
### 向自己發起請求
* 攻擊者使服務器本身向自己發起請求，像是請求 `localhost` 、 `127.0.0.1`。
* 應用程式會以這種方式運行，並且信任來自本地端的請求，原因包含:
    * access control 可能只有針對從向服務器發起的請求做檢查，而不會檢查服務本身發起的請求。
    * 出於災害復原的目的，應用程式可能允許來自本地的任何用戶在不登錄的情況下進行管理訪問，以方便管理者可以直接進行復原。
    * 管理界面可能正在監聽與主應用程式不同的端口號，因此無法直接使用用戶方式訪問。
```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://localhost/admin
```
### 向其他伺服器發起請求
* 攻擊者可以透過服務器向其他服務器進行請求，進一步取得敏感資訊或是其他攻擊。
```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://192.168.0.68/admin
```
### Bypassing
#### blacklist-based input filters
* 應用程式可能設定 127.0.0.1 、 localhost 為黑名單，但我們可以透過下面 cheatsheet 來繞過檢查。
    * [w181496/Web-CTF-Cheatsheet](https://github.com/w181496/Web-CTF-Cheatsheet#ssrf)
    * [0xn3va/ssrf](https://0xn3va.gitbook.io/cheat-sheets/web-application/server-side-request-forgery)
#### whitelist-based input filters
* 應用程式只允許匹配、以或包含允許值的白名單的輸入。在這種情況下，有時可以通過利用 URL 解析中的不一致來繞過。
* 透過 `@`。
```
https://expected-host@evil-host
```
* 透過 `#`。
```
https://evil-host#expected-host
```
* 透過 DNS 命名層次結構。
```
https://expected-host.evil-host
```
* URL-encode。
#### open redirection
* 有時候 API 可能提供開放重新導向路徑，我們可以藉此來繞過檢查。
```
/product/nextProduct?currentProductId=6&path=http://evil-user.net
```
## 防禦方法
* 權限設定。
* 請求 Token 驗證。
---
###### tags: `web security`