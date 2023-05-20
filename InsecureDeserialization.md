# Insecure Deserialization
## 成因
* 序列化，將物件轉成可儲存格式，如 python 的 pickle。
* 反序列化，將儲存的內容還原成物件。
* 不安全反序列化，攻擊者輸入不安全的序列化內容，來使伺服器執行不安全的內容。
* 不安全反序列化又稱，object injection。
## 影響
* 執行惡意程式，ex. RCE。
* 取得權限。
* 取得資料。
## 攻擊方法
### 識別序列化資料
* php
```
O:4:"User":2:{s:4:"name":s:6:"carlos"; s:10:"isLoggedIn":b:1;}
```
* java 使用 binary 儲存，可以尋找
```
ac ed (hex)
rO0 (base64)
```
### 更改序列化資料
```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:1;}
```
### 更改資料型態
```
O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}
```
## 防禦方法
* 應避免對使用者輸入進行反序列化。
* 在進行反序列化前，就要先進行檢查。
* 加入數位簽章，驗證資料。
---
###### tags: `web security`