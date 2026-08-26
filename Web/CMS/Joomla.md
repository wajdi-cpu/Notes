# Discovery/Footprinting

```sh
curl -s http://dev.inlanefreight.local/ | grep Joomla
```

```sh
curl -s http://dev.inlanefreight.local/README.txt | head -n 5
```

# Enumeration

## version enum

```sh
curl http://app.inlanefreight.local/administrator/manifests/files/joomla.xml | grep version
```

## Automated enum

```sh
 droopescan scan joomla --url http://dev.inlanefreight.local/
```

```sh
python2 joomlascan.py -u http://dev.inlanefreight.local
```

# login brute-forcing

- The administrator login portal is located at `http://example.com/administrator/index.php`

```sh
sudo python3 joomla-brute.py -u http://dev.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin
```

# Attacking Joomla

## Abusing Built-In Functionality

- admin interface after login

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/113/joomla_admin.png)

- we can click on `Templates` on the bottom left under `Configuration` to pull up the templates menu

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/113/joomla_templates.png)

- we can click on a template name. Let's choose `protostar` under the `Template` column header. This will bring us to the `Templates: Customise` page.

![l](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/113/joomla_customise.png)

- then we can change any file 


