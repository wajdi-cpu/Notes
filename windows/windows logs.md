# default logs folder 

```js
ls C:\Windows\System32\winevt\logs
```

# Enumerating Log Sources

```js
wevtutil el
```

# Gathering Log Information

```js
wevtutil gl "Windows PowerShell"
```

# Gathering log metadata 

```js
wevtutil gli "Windows PowerShell"
```

# Interacting with the Windows Event Log

```js
Get-WinEvent -ListLog *
```

