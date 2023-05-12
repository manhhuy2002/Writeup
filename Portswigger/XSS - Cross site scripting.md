## XSS - Cross site scripting

## Lý thuyết 

Đọc ở đây:

> https://www.invicti.com/learn/cross-site-scripting-xss/

> https://portswigger.net/web-security/cross-site-scripting


Thực hành: 


## [Lab: Reflected XSS into HTML context with nothing encoded](Reflected XSS into HTML context with nothing encoded)

```
This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.

To solve the lab, perform a cross-site scripting attack that calls the alert function.

```

Giao diện của bài: 

![image](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)

Nó có chức năng search, và input ta nhập vào được reflect luôn trong response, cũng như tên bài, không có gì được filter hay encoded ở đây cả, ta thực thi script xss:

> <script>alert(1)</script> 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/a56e9f9a-36a6-468c-8a23-2ca577d21ee6)


## [Reflected XSS into attribute with angle brackets HTML-encoded](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-attribute-angle-brackets-html-encoded)

```

This lab contains a reflected cross-site scripting vulnerability in the search blog functionality where angle brackets are HTML-encoded. To solve this lab, perform a cross-site scripting attack that injects an attribute and calls the alert function.

```

Bài này vẫn như bài trước nhưng dấu <> đã bị encode khi thực thi xss. Tuy vậy để ý trong input, ta có thể thêm event attribute để bypass

> " onmouseover="alert(1)

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/6c096d2f-c2e6-4c0b-84a2-a7db6243f149)


## [Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-string-angle-brackets-html-encoded)

```

This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded. The reflection occurs inside a JavaScript string. To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function.

```

Nhập bất kì thanh search, kiểm tra phần source: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/3514a77d-7a88-4fb2-b46c-0270ecb88b30)

Ý tưởng là bypass nhập alert(1) vì nó nằm sẵn trong <script> rồi: 
  
> ';alert(1);'abcd
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/a803a695-6e37-4e78-860f-f91148cd24f1)

  
## [Reflected DOM XSS](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-dom-xss-reflected)
  
```
This lab demonstrates a reflected DOM vulnerability. Reflected DOM vulnerabilities occur when the server-side application processes data from a request and echoes the data in the response. A script on the page then processes the reflected data in an unsafe way, ultimately writing it to a dangerous sink.

To solve this lab, create an injection that calls the alert() function.
  
```
  

