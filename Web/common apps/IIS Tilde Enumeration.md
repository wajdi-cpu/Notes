
## enumeration

- IIS tilde enumeration (aka the "8.3 shortname disclosure" vulnerability) is a well-known technique against older Microsoft IIS/Windows servers that exploits how Windows generates legacy 8.3 short filenames alongside long filenames on NTFS

- The enumeration process starts by sending requests with various characters following the tilde

```sh
http://example.com/~a
http://example.com/~b
http://example.com/~c
...
http://example.com/~s -> 200 OK
```


- This revels a directory with a short name beginning with "s"

## Tilde Enumeration using IIS ShortName Scanner

- Tool link : [IIS-ShortName-Scanner](https://github.com/irsdl/IIS-ShortName-Scanner)

```sh
java -jar iis_shortname_scanner.jar 0 5 http://10.129.204.231/
```

## Generate Wordlist to get the real file

- the target does not permit `GET` access to `http://10.129.204.231/TRANSF~1.ASP` so we have to create ower wordlist to brute-force

```sh
egrep -r ^transf /usr/share/wordlists/* | sed 's/^[^:]*://' > /tmp/list.txt
```

