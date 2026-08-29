 # see installed modules

```js
Get-Module
```

# list available modules not loaded

```js
Get-Module -ListAvailable
```

# importe Modules  

```js
Import-Module
```

# Checking Execution Policy State :

```js
Get-ExecutionPolicy
```

# Cmdlets and Functions From Within a Module

```js
Get-Command -Module PowerSploit
```

# Get an Object Properties/Methods

```js
get-member
```

# Invoke-Command

```js
nvoke-command -ComputerName ACADEMY-ICL-DC,LOCALHOST -ScriptBlock {Get-Service -Name 'windefend'}
```

