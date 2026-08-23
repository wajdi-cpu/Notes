
# help for a command

```js
Get-Help <powershell-command>
```

# get the path we we are 

```js
Get-Location
```
# find command aliases

```js
PS C:\htb> Get-Command 
```

## with verbosity 

```js
Get-Command -verb get
```

## name filter 

```js
PS C:\htb> Get-Command -noun windows*
```

# commands history 

```js
PS C:\htb> Get-History
```

# to view all history not just for the session :

```js
PS C:\htb> get-content C:\Users\DLarusso\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

# getting local groups 

```js
PS C:\htb> get-localgroup
```

# getting local users 

```js
PS C:\htb> Get-LocalUser
```

