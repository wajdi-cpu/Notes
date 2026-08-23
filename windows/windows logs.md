# default logs folder 

```js
PS C:\htb> ls C:\Windows\System32\winevt\logs
```

# Enumerating Log Sources

```js
C:\htb> wevtutil el
```

# Gathering Log Information

```js
C:\htb> wevtutil gl "Windows PowerShell"
```

# Gathering log metadata 

```js
C:\htb> wevtutil gli "Windows PowerShell"
```

# Interacting with the Windows Event Log

```js
PS C:\htb> Get-WinEvent -ListLog *
```

