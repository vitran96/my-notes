[[JSON]] Web Token or JWT
A way to authenticate server
It usually use for authentication between service

Notes:
- My findings after using it is that it is not really a good tool for session management.
- It also seems to have problem and bad for security.

# JWT body

# Secure JWT
[[PASETO]] is a recommended alternative.
[[OpenConnect]] and [[OAuth]] are also good replacement.

# [[Java]] package
https://github.com/auth0/java-jwt
https://github.com/jwtk/jjwt#installation

| Package | Git | Maven view |
| --- | --- | --- |
| auth0-jwt | https://github.com/auth0/java-jwt | https://mvnrepository.com/artifact/com.auth0/java-jwt |
| jjwt | https://github.com/jwtk/jjwt#installation | https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt |