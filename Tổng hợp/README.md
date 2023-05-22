## Tổng hợp kiến thức

## Leo thang căn bản trên linux
- Tryhackme: https://tryhackme.com/room/linprivesc

## Nmap, docker căn bản: 
- Tryhackme: 
     - https://tryhackme.com/room/furthernmap
     - https://tryhackme.com/room/easypeasyctf
     - https://tryhackme.com/room/brooklynninenine
     - https://tryhackme.com/room/introtodockerk8pdqk


## SQL injection

- Rootme: 
     - SQL injection - Authentification
     - SQL injection - String
     - SQL injection - Numerical
     - SQL injection - Time based
     - SQL injection - Blind
 - Portswigger: 
     Về căn bản là em làm hết lab sql injection trên portswigger rồi mà có out-of-band thì e chưa hiểu cách họ truy vấn lắm ạ.

     
## XSS - Cross-site scripting

- Rootme: 
     - XSS - Stored 1
     - XSS DOM Based - Introduction
     - XSS DOM Based - AngularJS
     - XSS DOM Based - Eval
     - XSS - Reflected
     - XSS - Stored 2
     - XSS DOM Based - Filters Bypass
     - XSS - Stored - filter bypass

- Portswigger: 
     Em còn các lab expert ạ.



## CSRF:
- Rootme: 
     - CSRF - 0 protection
     - CSRF - token bypass

- Portswigger:
     - CSRF vulnerability with no defenses
     - CSRF where token validation depends on request method
     - CSRF where token validation depends on token being present
     - CSRF where token is not tied to user session
     - CSRF where token is tied to non-session cookie
     - CSRF where token is duplicated in cookie
     - CSRF where Referer validation depends on header being present
     - CSRF with broken Referer validation
     
 (Em chưa làm dạng csrf với Samesite lax ạ)
     
## Server-side request forgery (SSRF)
    
- Rootme: 
     - SSRF - Server side request fogery

- Portswigger:
     - Basic SSRF against the local server
     - Basic SSRF against another back-end system
     - SSRF with blacklist-based input filter
     - SSRF with filter bypass via open redirection vulnerability
     - Blind SSRF with out-of-band detection
     - SSRF with whitelist-based input filter     

## Server-side template injection (SSTI)

- Rootme: 
     - Python - Server-side Template Injection Introduction
     - Java - Server-side Template Injection
     - Python - Blind SSTI Filters Bypass
- Portswigger: 
     - Basic server-side template injection
     - Basic server-side template injection (code context)
     - Server-side template injection using documentation
     - Server-side template injection in an unknown language with a documented exploit
     - Server-side template injection with information disclosure via user-supplied objects

## Uploadfile - path traversal - lfi - rfi

- Tryhackme: dogcat

Em đã làm hết các dạng cơ bản trên rootme và portswigger trừ lab: File upload - Polyglot và Web shell upload via race condition.



## Cross-origin resource sharing (CORS)

- Portswigger: 
     - CORS vulnerability with basic origin reflection
     - CORS vulnerability with trusted null origin
     - CORS vulnerability with trusted insecure protocols


## JWT

- Rootme: 
     - JWT - Introduction
     - JWT - Weak Secret
     - JWT - Public Key
- Portswigger: 
     - JWT authentication bypass via unverified signature
     - JWT authentication bypass via flawed signature verification
     - JWT authentication bypass via weak signing key
