# Querying Registry Entries

```js
Get-Item -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run | Select-Object -ExpandProperty Property
```

# Recursive Search

```js
Get-ChildItem -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Recurse
```

```js
Get-ItemProperty -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

# Reg.exe

```js
reg query HKEY_LOCAL_MACHINE\SOFTWARE\7-Zip
```

# Searching for a specific value 

```js
REG QUERY HKCU /F "Password" /t REG_SZ /S /K
```

# create a malisous Registry Key

```js
New-Item -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\ -Name TestKey
```

```js
New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name "access" -PropertyType String -Value "C:\Users\htb-student\Downloads\payload.exe"
```

OR

```js
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce\TestKey" /v access /t REG_SZ /d "C:\Users\htb-student\Downloads\payload.exe"
```

