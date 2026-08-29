## Enumeration

```sh
sudo nmap -sC -sV -p 21 192.168.2.142 --script "*ftp*" 
```

-  Anonymous Authentication

```sh
ftp 192.168.2.142    
```

## Specifics Attacks on FTP

#### Brute Forcing

```sh
medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp 
```

#### FTP Bounce Attack

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/ftp_bounce_attack.png)

```sh
nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2
```

