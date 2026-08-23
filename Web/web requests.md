# Getting the contetnt of a page 

```js
Akilesthedark@htb[/htb]$ curl http://info.cern.ch/
```

# download a page or a file

```js
Akilesthedark@htb[/htb]$ curl -O http://info.cern.ch/index.html
```

# cURL for HTTPS

```js
Akilesthedark@htb[/htb]$ curl -k https://www.inlanefreight.com
```

# head request 

```js
Akilesthedark@htb[/htb]$ curl -I https://www.inlanefreight.com
```

# set the user agent 

```js
Akilesthedark@htb[/htb]$ curl https://www.inlanefreight.com -A 'Mozilla/5.0'
```

# Post request 

```js
Akilesthedark@htb[/htb]$ curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london -d '{"city_name":"New_HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
```

