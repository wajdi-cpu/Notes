## Routing Table

```sh
netstat -r
```

# Dynamic Port Forwarding with SSH and SOCKS Tunneling

## SSH Local Port Forwarding

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/158/11.png)

#### Executing the Local Port Forward

```sh
 ssh -L 1234:localhost:3306 ubuntu@10.129.202.64
```

#### Forwarding Multiple Ports

```sh
ssh -L 1234:localhost:3306 -L 8080:localhost:80 ubuntu@10.129.202.64
```

## Setting up to Pivot

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/158/22.png)

#### Enabling Dynamic Port Forwarding with SSH

```sh
ssh -D 9050 ubuntu@10.129.202.64
```

#### Checking /etc/proxychains.conf

```sh
tail -4 /etc/proxychains.conf
```

#### Using Nmap with Proxychains

```sh
proxychains nmap -v -sn 172.16.5.1-200
```

# Remote/Reverse Port Forwarding with SSH

