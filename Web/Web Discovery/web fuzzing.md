# directories fuzzing wordlists

```js
`Discovery/Web-Content/common.txt`
```

```js
`Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt`
```

```js
`Discovery/Web-Content/raft-large-directories.txt`
```

```js
`Discovery/Web-Content/big.txt`
```

- biggest wordlist :

```js
Discovery/Web-Content/DirBuster-2007_directory-list-2.3-big.txt
```

# Directory Fuzzing 

```js
ffuf -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt -u http://IP:PORT/FUZZ -recursion
```

```js
dirsearch -u http://154.57.164.73:30879/recursive_fuzz/  -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt -r
```

```js
gobuster dir -u http://154.57.164.73:30879/recursive_fuzz -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt 
```

# Parameter Fuzzing

- GET :

```js
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u "http://IP:PORT/get.php?x=FUZZ" -fc 404
```

- POST :

```js
ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -mc 200 -v
```

# Vhost Fuzzing

```js
gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/Web-Content/common.txt --append-domain
```

```js
ffuf -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H "Host: FUZZ.inlanefreight.htb"
```

# Dns Fuzzing 

```js
gobuster dns --domain inlanefreight.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

# API Fuzzing

```js
api_fuzzer http://IP:PORT
```

```js
dirsearch -u http://154.57.164.82:31068 -w /usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt
```

