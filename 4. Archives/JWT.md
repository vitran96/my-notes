---
para: archive
was: resource
---
[[JSON]] Web Token (JWT, RFC 7519): compact, URL-safe token carrying claims; signed (JWS) and optionally encrypted (JWE). Used as bearer/ID tokens and for service-to-service auth.

Notes:
- My findings after using it is that it is not really a good tool for session management.
- It also seems to have problem and bad for security.

# JWT body

# Secure JWT
[[PASETO]] is a recommended alternative.
[[OAuth]] 2.0 and OpenID Connect are protocols that issue tokens (OIDC ID tokens are JWTs), not replacements for JWT.

# [[Java]] package
https://github.com/auth0/java-jwt
https://github.com/jwtk/jjwt#installation

| Package | Git | Maven view |
| --- | --- | --- |
| auth0-jwt | https://github.com/auth0/java-jwt | https://mvnrepository.com/artifact/com.auth0/java-jwt |
| jjwt | https://github.com/jwtk/jjwt#installation | https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt |