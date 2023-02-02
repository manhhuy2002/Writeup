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
Đây là dạng reflected xss cơ bản. Dùng payload: 

``
<script>alert('xss')</script>

```

![xss1](https://github.com/manhhuy2002/hello-world/blob/main/xss/1.jpg)

## Lab 2: Reflected XSS into attribute with angle brackets HTML-encoded

```
This lab contains a reflected cross-site scripting vulnerability in the search blog functionality where angle brackets are HTML-encoded. To solve this lab, perform a cross-site scripting attack that injects an attribute and calls the alert function. 

```
Như đề bài, bài lab đã dùng html-encoded, thử truyền <script>alert(1)</script> vào thì thấy:

![](https://github.com/manhhuy2002/hello-world/blob/main/xss/2.jpg)

Nhưng có vẻ kh mã hóa dấu ", vì vậy ta có thể khai thác như sau:
Truyền vào ", để đóng giá trị value lại, ta kh thể đóng thẻ input được do đã bị mã hóa nên đến đây cần làm cho kích hoạt function alert bên trong thẻ input luôn bằng cách truyền vào thuộc tính onmouseover với giá trị là function alert:

```
" onmouseover = "alert(1)

```

![](https://github.com/manhhuy2002/hello-world/blob/main/xss/3.jpg)

## Lab 3: Reflected XSS into a JavaScript string with angle brackets HTML encoded

```
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded. The reflection occurs inside a JavaScript string. To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function. 
```
Ở bài lab thứ 3, thử truyền vào thanh tìm kiếm <script>alert(1)</script> rồi kiểm tra bằng burpsuite, thì thấy cứ truyền vào <script> hay thẻ <> thì sẽ bị encode:
 
![](https://github.com/manhhuy2002/hello-world/blob/main/xss/4.jpg)
 
 Do giá trị của Searchterms đang kh được bảo vệ đúng cách nên  mình khai thác bằng cách tận dụng hàm đóng giá trị searchterms và thực hiện hàm eval:

```
    <script>
      var searchTerms = ''; eval(alert(1))//';
      document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
  </script>
  
```
Mình truyền vào hàm ';eval(alert(1))// , để đóng giá trị var searchTerms = ''; đồng thời thực hiện hàm eval(alert(1)), và được kết quả:
  
![](https://github.com/manhhuy2002/hello-world/blob/main/xss/5.jpg)

## Lab 4: Reflected DOM XSS
  
  
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
