## DNS Zone Transfer

- A DNS zone is a portion of the DNS namespace that a specific organization or administrator manages.
#### DIG - AXFR Zone Transfer

```sh
dig AXFR @ns1.inlanefreight.htb inlanefreight.htb
```

```sh
 fierce --domain zonetransfer.me
```

## Domain Takeovers & Subdomain Enumeration

- Domain takeover is also possible with subdomains called `subdomain takeover`
#### Subdomain Enumeration

- Before performing a subdomain takeover, we should enumerate subdomains for a target domain using tools like [Subfinder](https://github.com/projectdiscovery/subfinder).

```sh
subfinder -d inlanefreight.com -v
```

- An excellent alternative is a tool called [Subbrute](https://github.com/TheRook/subbrute). This tool allows us to use self-defined resolvers and perform pure DNS brute-forcing attacks during internal penetration tests on hosts that do not have Internet access.

```sh
echo "ns1.inlanefreight.com" > ./resolvers.txt
```

```sh
subbrute.py inlanefreight.com -s ./names.txt -r ./resolvers.txt
```

- The `support` subdomain has an alias record pointing to an AWS S3 bucket. However, the URL `https://support.inlanefreight.com` shows a `NoSuchBucket` error indicating that the subdomain is potentially vulnerable to a subdomain takeover.

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/s3.png)


#### Subdomain takeover identification

```sh
subfinder -d target.com -silent | httpx -silent | nuclei -t nuclei-templates/http/takeovers/ -o takeover_results.txt
```

## DNS Spoofing

#### Local DNS Cache Poisoning

- To exploit the DNS cache poisoning via `Ettercap`, we should first edit the `/etc/ettercap/etter.dns`

```sh
inlanefreight.com      A   192.168.225.110 <- fake IP
*.inlanefreight.com    A   192.168.225.110 <- fake IP
```

- Next, start the `Ettercap` tool and scan for live hosts within the network by navigating to `Hosts > Scan for Hosts`. Once completed, add the target IP address (e.g., `192.168.152.129`) to Target1 and add a default gateway IP (e.g., `192.168.152.2`) to Target2.

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/target.png)

- Activate `dns_spoof` attack by navigating to `Plugins > Manage Plugins`. This sends the target machine with fake DNS responses that will resolve `inlanefreight.com` to IP address `192.168.225.110`

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/etter_plug.png)


- After a successful DNS spoof attack, if a victim user coming from the target machine `192.168.152.129` visits the `inlanefreight.com` domain on a web browser, they will be redirected to a `Fake page` that is hosted on IP address `192.168.225.110`

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/etter_site.png)

