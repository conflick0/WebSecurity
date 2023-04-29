# SQLi
## 成因
* 程式漏洞，使攻擊者可以插入惡意SQL指令。
## 影響
* 查詢隱藏數據
* 影響應用程式邏輯
## 攻擊方法
### UNION 攻擊
* 查詢不同的資料庫表之資料。(ex. 使用者帳號密碼)
### Bind SQLi
* 有時候 SQLi 不一定會回傳頁面，藉由以下方法來進行確認攻擊結果
#### 觸發條件回應
* 網站透過 cookie 來檢查使用者狀況(有SQLi漏洞)，正常情況會在網頁上呈現 Welcome back。
* 透過下列條件，如果條件為 True (‘1’=‘1’)，網站會回應 Welcome back，反之 False (‘1’=‘2’) 則不會回應 Welcome back。由此可判斷 True 或 False。
```
...xyz' AND '1'='1
...xyz' AND '1'='2
```
#### 觸發 SQL 錯誤
* 透過 SELECT CASE WHEN (條件)，來決定是否觸發 1/0 ，來觸發SQL錯誤，進一步得知注入的結果是 True 或 False。
```
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```
#### 觸發時間延遲
* 透過 SELECT CASE WHEN (條件)，來決定是否觸發延遲 (pg_sleep(10)) ，來觸發回應時間延遲，進一步得知注入的結果是 True 或 False。
```
xyz'%3b SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END
```
## 防禦方法
* 使用參數化查詢，非直接字串相接。
---
###### tags: `web security`