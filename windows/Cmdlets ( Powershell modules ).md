 # see installed modules

```js
PS C:\htb> Get-Module
```

# list available modules not loaded

```js
PS C:\htb> Get-Module -ListAvailable
```

# importe Modules  

```js
PS C:\Users\htb-student> Import-Module
```

# Checking Execution Policy State :

```js
PS C:\htb> Get-ExecutionPolicy
```

# Cmdlets and Functions From Within a Module

```js
PS C:\htb> Get-Command -Module PowerSploit
```

# Get an Object Properties/Methods

```js
PS C:\htb> get-member
```

# Invoke-Command

```js
PS C:\htb> invoke-command -ComputerName ACADEMY-ICL-DC,LOCALHOST -ScriptBlock {Get-Service -Name 'windefend'}
```

