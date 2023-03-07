# Csrf - Access control vulnerabilities - Authentication - Jwt - OAuth authentication

* [Portswigger - Cross-site request fogery](#pcsrf)
  - [1. CSRF vulnerability with no defenses](#pcsrf1)
  - [2. CSRF where token validation depends on request method](#pcsrf2)
  - [3. CSRF where token validation depends on token being present](#pcsrf3)
  - [4. CSRF where token is not tied to user session](#pcsrf4)
  - [5. CSRF where token is tied to non-session cookie](#pcsrf5)
  - [6. CSRF where token is duplicated in cookie](#pcsrf6)
  - [7. SameSite Lax bypass via method override](#pcsrf7)
  - [8. SameSite Strict bypass via client-side redirect](#pcsrf8)
  - [9. SameSite Strict bypass via sibling domain](#pcsrf9)
  - [10. SameSite Lax bypass via cookie refresh](#pcsrf10)
  - [11. CSRF where Referer validation depends on header being present](#pcsrf11)
  - [12. CSRF with broken Referer validation]()
  
  
