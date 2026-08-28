## General LDAP

- LDAP works by using a `client-server architecture`. A client sends an LDAP request to a server

`LDAP requests` are `messages` that clients send to servers to `perform operations` on data stored in a directory service. An LDAP request is comprised of several components:

1. `Session connection`: The client connects to the server via an LDAP port (usually 389 or 636).
2. `Request type`: The client specifies the operation it wants to perform, such as `bind`, `search`, etc.
3. `Request parameters`: The client provides additional information for the request, such as the `distinguished name` (DN) of the entry to be accessed or modified, the scope and filter of the search query, the attributes and values to be added or changed, etc.
4. `Request ID`: The client assigns a unique identifier for each request to match it with the corresponding response from the server.

response message that includes several components:

1. `Response type`: The server indicates the operation that was performed in response to the request.
2. `Result code`: The server indicates whether or not the operation was successful and why.
3. `Matched DN:` If applicable, the server returns the DN of the closest existing entry that matches the request.
4. `Referral`: The server returns a URL of another server that may have more information about the request, if applicable.
5. `Response data`: The server returns any additional data related to the response, such as the attributes and values of an entry that was searched or modified.
## ldapsearch

```sh
ldapsearch -H ldap://ldap.example.com:389 -D "cn=admin,dc=example,dc=com" -w secret123 -b "ou=people,dc=example,dc=com" "(mail=john.doe@example.com)"
```

## LDAP Injection

- For testing for LDAP injection we use `special characters or operators` that can change the query's meaning 

|Input|Description|
|---|---|
|`*`|An asterisk `*` can `match any number of characters`.|
|`( )`|Parentheses `( )` can `group expressions`.|
|`\|`|A vertical bar `\|` can perform `logical OR`.|
|`&`|An ampersand `&` can perform `logical AND`.|
|`(cn=*)`|Input values that try to bypass authentication or authorisation checks by injecting conditions that `always evaluate to true` can be used. For example, `(cn=*)` or `(objectClass=*)` can be used as input values for a username or password fields.|

### Exemple

- suppose an application uses the following LDAP query to authenticate users:

```sh
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```

- If an attacker injects the `*` character into the `$username` field, the LDAP query will match any user account with the password `dummy`. This would allow the attacker to gain access to the application using any password, as shown below:If an attacker injects the `*` character into the `$username` field, the LDAP query will match any user account with the password `dummy`. This would allow the attacker to gain access to the application using any password

```sh
$username = "*";
$password = "dummy";
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```