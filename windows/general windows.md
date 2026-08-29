
#  Get-WmiObject 

```js
Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber
```

# Get-ComputerInfo

```js
Get-ComputerInfo | Select WindowsProductName, WindowsVersion, OsBuildNumber
```

# icacls 

```js
icacls c:\windows 
```

# display shares in windows 

```js
net share 
```

# desplay running services 

```js
Get-Service | ? {$_.Status -eq "Running"} | select -First 2 |fl
```

# Examining services using sc

```js
sc qc wuauserv
```

# service permissions

```js
Get-ACL -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List 
```

# get the execution policy

```js
Get-ExecutionPolicy -List
```

