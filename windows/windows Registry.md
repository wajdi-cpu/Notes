# Querying Registry Entries

```js
PS C:\htb> Get-Item -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run | Select-Object -ExpandProperty Property
```

# Recursive Search

```js
PS C:\htb> Get-ChildItem -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Recurse
```

```js
PS C:\htb> Get-ItemProperty -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

# Reg.exe

```js
PS C:\htb> reg query HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip
```

# Searching for a specific value 

```js
PS C:\htb> REG QUERY HKCU /F "Password" /t REG_SZ /S /K
```

# create a malisous Registry Key

```js
PS C:\htb> New-Item -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\ -Name TestKey
```

```js
PS C:\htb> New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name "access" -PropertyType String -Value "C:\Users\htb-student\Downloads\payload.exe"
```

OR

```js
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce\TestKey" /v access /t REG_SZ /d "C:\Users\htb-student\Downloads\payload.exe"
```

