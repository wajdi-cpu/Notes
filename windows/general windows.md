
#  Get-WmiObject 

```js
PS C:\htb> Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber
```

# Get-ComputerInfo

```js
PS C:\Users\htb-student> Get-ComputerInfo | Select WindowsProductName, WindowsVersion, OsBuildNumber
```

# icacls 

```js
C:\htb> icacls c:\windows 
```

# display shares in windows 

```js
C:\Users\htb-student> net share 
```

# desplay running services 

```js
Get-Service | ? {$_.Status -eq "Running"} | select -First 2 |fl
```

# Examining services using sc

```js
C:\WINDOWS\system32> sc qc wuauserv
```

# service permissions

```js
PS C:\Users\htb-student> Get-ACL -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List 
```

# get the execution policy

```js
PS C:\htb> Get-ExecutionPolicy -List
```

