# Get the system info 

```js
C:\htb> systeminfo
```

# list all the network interfaces 

```js
C:\Users\htb\> ipconfig /all | more
```


# ARP to Find Additional Hosts

```js
C:\htb> arp /a 
```

# to get the privs of the current user 

```js
c:\htb> whoami /priv
```

# Investigating Groups

```js
C:\htb> whoami /groups
```

# get all the informations 

```js
whoami /all
```

# display a list of all users on a host

```js
C:\htb> net user 
```

# display a list of all groups on a host

```js
C:\htb> net group net group 
 
```

```js
C:\htb>net localgroup 
```

# Exploring Resources smb on the Network

```js
C:\htb> net share
```

```js
c:\htb> net view
```

# Important Environment Variables 

| Variable Name         | Description                                                                                                                                                                                                                                                                               |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `%PATH%`              | Specifies a set of directories(locations) where executable programs are located.                                                                                                                                                                                                          |
| `%OS%`                | The current operating system on the user's workstation.                                                                                                                                                                                                                                   |
| `%SYSTEMROOT%`        | Expands to `C:\Windows`. A system-defined read-only variable containing the Windows system folder. Anything Windows considers important to its core functionality is found here, including important data, core system binaries, and configuration files.                                 |
| `%LOGONSERVER%`       | Provides us with the login server for the currently active user followed by the machine's hostname. We can use this information to know if a machine is joined to a domain or workgroup.                                                                                                  |
| `%USERPROFILE%`       | Provides us with the location of the currently active user's home directory. Expands to `C:\Users\{username}`.                                                                                                                                                                            |
| `%ProgramFiles%`      | Equivalent of `C:\Program Files`. This location is where all the programs are installed on an `x64` based system.                                                                                                                                                                         |
| `%ProgramFiles(x86)%` | Equivalent of `C:\Program Files (x86)`. This location is where all 32-bit programs running under `WOW64` are installed. Note that this variable is only accessible on a 64-bit host. It can be used to indicate what kind of host we are interacting with. (`x86` vs. `x64` architecture) |
# Query Services

```js
C:\htb> tasklist /svc 
```

# list all of the current running services

```js
C:\htb> net start  
```
# Querry Scheduled Tasks

```js
C:\htb> SCHTASKS /Query /V /FO list
```

# New Task Creation

```js
C:\htb> schtasks /create /sc ONSTART /tn "My Secret Task" /t
```

