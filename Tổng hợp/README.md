

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

Em đã làm hết các dạng cơ bản trên rootme và portswigger trừ lab: File upload - Polyglot
