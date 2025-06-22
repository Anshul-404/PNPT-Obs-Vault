- The `.well-known` standard, defined in [RFC 8615](https://datatracker.ietf.org/doc/html/rfc8615), serves as a standardized directory within a website's root domain.
- This designated location, **typically accessible via the `/.well-known/` path** on a web server, centralizes a website's **critical metadata,** including configuration **files and information related to its ==services, protocols, and security mechanisms.**==
![[Pasted image 20250621060948.png]]

# Web Recon and .well-known
---
- In web recon, the `.well-known` URIs can be invaluable for d**==iscovering endpoints and configuration details that can be further tested during a penetration test==**. One particularly useful ==URI is `openid-configuration`.==
- The `openid-configuration` URI is part of the OpenID Connect Discovery protocol, an identity layer built on top of the OAuth 2.0 protocol.
- When a client application wants to use **OpenID Connect for authentication**, it can retrieve the OpenID Connect Provider's configuration by **accessing the `https://example.com/.well-known/openid-configuration` endpoint.**
- ![[Pasted image 20250621061205.png]]
The information obtained from the `openid-configuration` endpoint provides multiple exploration opportunities:

1. `Endpoint Discovery`:
    - `Authorization Endpoint`: Identifying the URL for **user authorization requests.**
    - `Token Endpoint`: Finding the URL where tokens are issued.
    - `Userinfo Endpoint`: Locating the **endpoint that provides user information.**
2. `JWKS URI`: The `jwks_uri` reveals the `JSON Web Key Set` (`JWKS`), detailing the cryptographic keys used by the server.
3. `Supported Scopes and Response Types`: Understanding which scopes and **response types are supported helps in mapping out the functionality and limitations of the OpenID Connect implementation.**
4. `Algorithm Details`: Information about supported signing algorithms can be crucial for understanding the security measures in place.