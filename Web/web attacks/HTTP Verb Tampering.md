# Bypassing Basic Authentication

- The first type of HTTP Verb Tampering vulnerability is mainly caused by `Insecure Web Server Configurations`
- `Reset` button seems to be restricted for authenticated users only

![l](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_verb_tampering_add.jpg)

- As we do not have any credentials, we will get a `401 Unauthorized` page:

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_verb_tampering_unauthorized.jpg)


- So, either the `/admin` directory is restricted to authenticated users only, or only the `/admin/reset.php` page is

- to see the accepted methods

```sh
curl -i -X OPTIONS http://SERVER_IP:PORT/

HTTP/1.1 200 OK
Date: 
Server: Apache/2.4.41 (Ubuntu)
Allow: POST,OPTIONS,HEAD,GET
Content-Length: 0
Content-Type: httpd/unix-directory
```

- Once we change `POST` to `HEAD` and forward the request, we will see that we no longer get a login prompt or a `401 Unauthorized` page and get an empty output instead, as expected with a `HEAD` request. If we go back to the `File Manager` web application, we will see that all files have indeed been deleted, meaning that we successfully triggered the `Reset` functionality without having admin access or any credentials

![d](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_verb_tampering_after_reset.jpg)

# Bypassing Security Filters

- The other and more common type of HTTP Verb Tampering vulnerability is caused by `Insecure Coding` errors made during the development of the web application
- In the `File Manager` web application, if we try to create a new file name with special characters in its name (e.g. `test;`), we get the following message:

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_verb_malicious_request.jpg)


- changing the methode to a POST request by clicking on `Change Request Method` in the burp repeater

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_verb_tampering_GET_request.png)

- This time, we did not get the `Malicious Request Denied!` message, and our file was successfully created

- trying to exploit a  command injection we will use the following file name in our attack (`file1; touch file2;` )

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_verb_tampering_filter_bypass_request.png)

- we successfully bypassed the filter through an HTTP Verb Tampering vulnerability and achieved command injection

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_verb_tampering_after_filter_bypass.jpg)