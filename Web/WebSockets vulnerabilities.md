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



