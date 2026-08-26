# WordPress Structure

## wordpress file structure

```sh
.
├── index.php
├── license.txt
├── readme.html
├── wp-activate.php
├── wp-admin
├── wp-blog-header.php
├── wp-comments-post.php
├── wp-config.php
├── wp-config-sample.php
├── wp-content
├── wp-cron.php
├── wp-includes
├── wp-links-opml.php
├── wp-load.php
├── wp-login.php
├── wp-mail.php
├── wp-settings.php
├── wp-signup.php
├── wp-trackback.php
└── xmlrpc.php
```

## Key WordPress Directories

```sh
├── plugins
└── themes
```

```sh
/wp-includes
.
├── <SNIP>
├── theme.php
├── update.php
├── user.php
├── vars.php
├── version.php
├── widgets
├── widgets.php
├── wlwmanifest.xml
├── wp-db.php
└── wp-diff.php
```

# WordPress User Roles

| Role          | Description                                                                                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Administrator | This user has access to administrative features within the website. This includes adding and deleting users and posts, as well as editing source code. |
| Editor        | An editor can publish and manage posts, including the posts of other users.                                                                            |
| Author        | Authors can publish and manage their own posts.                                                                                                        |
| Contributor   | These users can write and manage their own posts but cannot publish them.                                                                              |
| Subscriber    | These are normal users who can browse posts and edit their profiles.                                                                                   |

# WordPress Core Version Enumeration

```sh
curl -s -X GET http://blog.inlanefreight.com | grep '<meta name="generator"'
```

# Plugins and Themes Enumeration

## Plugins

- passive enum

```sh
 curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'wp-content/plugins/*' | cut -d"'" -f2
```

- active enum 

```sh
 curl -I -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta
```

## themes

```sh
curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'themes' | cut -d"'" -f2
```


# Directory Indexing

- If we browse to the plugins directory, we can see that we still have access to the `Mail Masta` plugin even it is deactivated .

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/17/plugin-mailmasta2.png)

```sh
 curl -s -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta/ | html2text
```


# User Enumeration

```sh
curl http://blog.inlanefreight.com/wp-json/wp/v2/users | jq
```

# Login

```sh
 curl -X POST -d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>CORRECT-PASSWORD</value></param></params></methodCall>" http://blog.inlanefreight.com/xmlrpc.php
```

# Methods identification 

```sh
 curl -X POST http://154.57.164.75:32765/xmlrpc.php -d '<?xml version="1.0"?><methodCall><methodName>system.listMethods</methodName><params></params></methodCall>'
```

# WPScan

## Enumerating a Website with WPScan

```sh
wpscan --url http://blog.inlanefreight.com --enumerate --api-token Kffr4fdJzy9qVcTk<SNIP>
```

## WordPress User Bruteforce

```sh
wpscan --password-attack xmlrpc -t 20 -U admin, david -P passwords.txt --url http://blog.inlanefreight.com
```

