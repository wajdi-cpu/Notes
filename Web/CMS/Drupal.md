# Discovery/Footprinting

```sh
curl -s http://drupal.inlanefreight.local | grep Drupal
```

# Enumeration

```sh
curl -s http://drupal-acc.inlanefreight.local/CHANGELOG.txt | grep -m2 ""
```

```sh
droopescan scan drupal -u http://drupal.inlanefreight.local
```

