# General infos

```sh
whois inlanefreight.com
```

```sh
whatweb example.com
```

# nuclie 

- Easy Mode

```sh
nuclei -u https://my.target.site
```

- Automatic Selection of templates

```sh
nuclei -u https:// my.target.site -as
```

- Specific Templates By Folder

```sh
nuclei -u https://my.target.site -t file/logs -t exposures/files -t cves/2021
```

- Select Templates By Severity

```sh
nuclei -u https://jira.targetdomain.site -s critical,high,medium,low,info
```

- Rate Limiting requests

```sh
nuclei -u https://my.target.site/ -rl 3 
```

- Rate Limiting templates

```sh
nuclei -u https://my.target.site/ -c 3 
```
# DNS Tools

| Tool                         | Key Features                                                                                            | Use Cases                                                                                                                               |
| ---------------------------- | ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `dig`                        | Versatile DNS lookup tool that supports various query types (A, MX, NS, TXT, etc.) and detailed output. | Manual DNS queries, zone transfers (if allowed), troubleshooting DNS issues, and in-depth analysis of DNS records.                      |
| `nslookup`                   | Simpler DNS lookup tool, primarily for A, AAAA, and MX records.                                         | Basic DNS queries, quick checks of domain resolution and mail server records.                                                           |
| `host`                       | Streamlined DNS lookup tool with concise output.                                                        | Quick checks of A, AAAA, and MX records.                                                                                                |
| `dnsenum`                    | Automated DNS enumeration tool, dictionary attacks, brute-forcing, zone transfers (if allowed).         | Discovering subdomains and gathering DNS information efficiently.                                                                       |
| `fierce`                     | DNS reconnaissance and subdomain enumeration tool with recursive search and wildcard detection.         | User-friendly interface for DNS reconnaissance, identifying subdomains and potential targets.                                           |
| `dnsrecon`                   | Combines multiple DNS reconnaissance techniques and supports various output formats.                    | Comprehensive DNS enumeration, identifying subdomains, and gathering DNS records for further analysis.                                  |
| `theHarvester`               | OSINT tool that gathers information from various sources, including DNS records (email addresses).      | Collecting email addresses, employee information, and other data associated with a domain from multiple sources.                        |
| `Online DNS Lookup Services` | User-friendly interfaces for performing DNS lookups.                                                    | Quick and easy DNS lookups, convenient when command-line tools are not available, checking for domain availability or basic information |


- default  record lookup

```sh
dig domain.com
```

- Retrieves the IPv4 address

```sh
dig domain.com A
```

- Identifies the authoritative name servers for the domain

```sh
dig domain.com NS
```

- Retrieves any TXT records

```sh
dig domain.com TXT
```

- Specifies a specific name server to query

```sh
dig @1.1.1.1 domain.com
```

- Performs a reverse lookup

```sh
dig -x 192.168.1.1
```

- Retrieves all available DNS records

```sh
dig domain.com ANY
```

# Subdomain enum with subfinder

- Basic enumeration

```sh
subfinder -d target.com
```

- Multiple domains at once

```sh
subfinder -dL domains.txt
```

- Only show resolved/alive subdomains (with IPs)

```sh
subfinder -d target.com -oI
```

- Silent mode

```sh
subfinder -d target.com -silent
```

- Recursive Discovery

```sh
subfinder -d target.com -recursive
```

- Increase threads/speed

```sh
subfinder -d target.com -t 100
```

- filter by pattern

```sh
subfinder -d target.com -m "api,dev,staging"
```

- Chained full recon pipeline

```sh
subfinder -d target.com -silent | dnsx -silent | httpx -silent -sc -title -o recon_results.txt
```
# Exploiting Zone Transfers

```sh
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

# crt.sh lookup

```sh
curl -s "https://crt.sh/?q=example.com&output=json" | jq
```

# Fingerprinting

- banner grapping 

```sh
curl -I inlanefreight.com
```

- firewall identification

```sh
wafw00f inlanefreight.com
```

- web scanning 

```sh
nikto -h inlanefreight.com -Tuning b
```

# Well-Known URIs

| URI Suffix                     | Description                                                                                           | Status      | Reference                                                                                                                                                                          |
| ------------------------------ | ----------------------------------------------------------------------------------------------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `security.txt`                 | Contains contact information for security researchers to report vulnerabilities.                      | Permanent   | RFC 9116                                                                                                                                                                           |
| `/.well-known/change-password` | Provides a standard URL for directing users to a password change page.                                | Provisional | [https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri](https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri) |
| `openid-configuration`         | Defines configuration details for OpenID Connect, an identity layer on top of the OAuth 2.0 protocol. | Permanent   | [http://openid.net/specs/openid-connect-discovery-1_0.html](http://openid.net/specs/openid-connect-discovery-1_0.html)                                                             |
| `assetlinks.json`              | Used for verifying ownership of digital assets (e.g., apps) associated with a domain.                 | Permanent   | [https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md](https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md)         |
| `mta-sts.txt`                  | Specifies the policy for SMTP MTA Strict Transport Security (MTA-STS) to enhance email security.      | Permanent   | RFC 8461                                                                                                                                                                           |

# Creepy Crawlies

- using reconspider

```sh
python3 ReconSpider.py http://inlanefreight.com
```

- using spiderfoot

```sh
 python3 ./sf.py -l 127.0.0.1:5001
```

# Search Engine Discovery

- search operators 

|Operator|Operator Description|Example|Example Description|
|:--|:--|:--|:--|
|`site:`|Limits results to a specific website or domain.|`site:example.com`|Find all publicly accessible pages on example.com.|
|`inurl:`|Finds pages with a specific term in the URL.|`inurl:login`|Search for login pages on any website.|
|`filetype:`|Searches for files of a particular type.|`filetype:pdf`|Find downloadable PDF documents.|
|`intitle:`|Finds pages with a specific term in the title.|`intitle:"confidential report"`|Look for documents titled "confidential report" or similar variations.|
|`intext:` or `inbody:`|Searches for a term within the body text of pages.|`intext:"password reset"`|Identify webpages containing the term “password reset”.|
|`cache:`|Displays the cached version of a webpage (if available).|`cache:example.com`|View the cached version of example.com to see its previous content.|
|`link:`|Finds pages that link to a specific webpage.|`link:example.com`|Identify websites linking to example.com.|
|`related:`|Finds websites related to a specific webpage.|`related:example.com`|Discover websites similar to example.com.|
|`info:`|Provides a summary of information about a webpage.|`info:example.com`|Get basic details about example.com, such as its title and description.|
|`define:`|Provides definitions of a word or phrase.|`define:phishing`|Get a definition of "phishing" from various sources.|
|`numrange:`|Searches for numbers within a specific range.|`site:example.com numrange:1000-2000`|Find pages on example.com containing numbers between 1000 and 2000.|
|`allintext:`|Finds pages containing all specified words in the body text.|`allintext:admin password reset`|Search for pages containing both "admin" and "password reset" in the body text.|
|`allinurl:`|Finds pages containing all specified words in the URL.|`allinurl:admin panel`|Look for pages with "admin" and "panel" in the URL.|
|`allintitle:`|Finds pages containing all specified words in the title.|`allintitle:confidential report 2023`|Search for pages with "confidential," "report," and "2023" in the title.|
|`AND`|Narrows results by requiring all terms to be present.|`site:example.com AND (inurl:admin OR inurl:login)`|Find admin or login pages specifically on example.com.|
|`OR`|Broadens results by including pages with any of the terms.|`"linux" OR "ubuntu" OR "debian"`|Search for webpages mentioning Linux, Ubuntu, or Debian.|
|`NOT`|Excludes results containing the specified term.|`site:bank.com NOT inurl:login`|Find pages on bank.com excluding login pages.|
|`*` (wildcard)|Represents any character or word.|`site:socialnetwork.com filetype:pdf user* manual`|Search for user manuals (user guide, user handbook) in PDF format on socialnetwork.com.|
|`..` (range search)|Finds results within a specified numerical range.|`site:ecommerce.com "price" 100..500`|Look for products priced between 100 and 500 on an e-commerce website.|
|`" "` (quotation marks)|Searches for exact phrases.|`"information security policy"`|Find documents mentioning the exact phrase "information security policy".|
|`-` (minus sign)|Excludes terms from the search results.|`site:news.com -inurl:sports`|Search for news articles on news.com excluding sports-related content.|
- we can also use https://www.exploit-db.com/google-hacking-database

# wayback machine

site : https://web.archive.org/

# Reconnaissance Frameworks

```sh
python3 finalrecon.py --headers --whois --url http://inlanefreight.com
```

# web extentions

- wappalysar : https://www.wappalyzer.com/
- EndPointer : https://chromewebstore.google.com/detail/endpointer/ppliilneafplhagjhhphcjmjdmbjagcp?pli=1

	