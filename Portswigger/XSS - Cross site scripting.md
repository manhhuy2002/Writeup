## XSS - Cross site scripting

## Lý thuyết 

Đọc ở đây:

> https://www.invicti.com/learn/cross-site-scripting-xss/

> https://portswigger.net/web-security/cross-site-scripting


Thực hành: 


## [Lab: Reflected XSS into HTML context with nothing encoded](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)

```
This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.

To solve the lab, perform a cross-site scripting attack that calls the alert function.

```

Giao diện của bài: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/bab08d43-21a7-4322-8937-cdea2ed825c1)


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
  
Khi ta nhập bất kì vào thanh search, 1 GET request sẽ được gửi thông qua /search-results?search=

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/15aa4793-94c9-49f4-84e4-d0fa47e9cf4f)

Tuy vậy thì trước đó, nó đã được xử lí qua file searchResult.js:
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/d5617f55-8769-4af4-9d9a-cce6df5c5176)

```

function search(path) {
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
            eval('var searchResultsObj = ' + this.responseText);
            displaySearchResults(searchResultsObj);
        }
    };
    xhr.open("GET", path + window.location.search);
    xhr.send();

    function displaySearchResults(searchResultsObj) {
        var blogHeader = document.getElementsByClassName("blog-header")[0];
        var blogList = document.getElementsByClassName("blog-list")[0];
        var searchTerm = searchResultsObj.searchTerm
        var searchResults = searchResultsObj.results

        var h1 = document.createElement("h1");
        h1.innerText = searchResults.length + " search results for '" + searchTerm + "'";
        blogHeader.appendChild(h1);
        var hr = document.createElement("hr");
        blogHeader.appendChild(hr)

        for (var i = 0; i < searchResults.length; ++i)
        {
            var searchResult = searchResults[i];
            if (searchResult.id) {
                var blogLink = document.createElement("a");
                blogLink.setAttribute("href", "/post?postId=" + searchResult.id);

                if (searchResult.headerImage) {
                    var headerImage = document.createElement("img");
                    headerImage.setAttribute("src", "/image/" + searchResult.headerImage);
                    blogLink.appendChild(headerImage);
                }

                blogList.appendChild(blogLink);
            }

            blogList.innerHTML += "<br/>";

            if (searchResult.title) {
                var title = document.createElement("h2");
                title.innerText = searchResult.title;
                blogList.appendChild(title);
            }

            if (searchResult.summary) {
                var summary = document.createElement("p");
                summary.innerText = searchResult.summary;
                blogList.appendChild(summary);
            }

            if (searchResult.id) {
                var viewPostButton = document.createElement("a");
                viewPostButton.setAttribute("class", "button is-small");
                viewPostButton.setAttribute("href", "/post?postId=" + searchResult.id);
                viewPostButton.innerText = "View post";
            }
        }

        var linkback = document.createElement("div");
        linkback.setAttribute("class", "is-linkback");
        var backToBlog = document.createElement("a");
        backToBlog.setAttribute("href", "/");
        backToBlog.innerText = "Back to Blog";
        linkback.appendChild(backToBlog);
        blogList.appendChild(linkback);
    }
}

```
Ta để ý hàm eval, nó sẽ là nơi để ta thực thi xss.
Ở đây ta có thể dùng \ để escape JSON response và thực thi xss
                                                 
> "\-alert(1)}\\           
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/e51b2120-3647-4314-9022-afcc96e133ad)

  
  
## [Lab: Reflected XSS into HTML context with most tags and attributes blocked](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-html-context-with-most-tags-and-attributes-blocked)
  
```
This lab contains a reflected XSS vulnerability in the search functionality but uses a web application firewall (WAF) to protect against common XSS vectors.

To solve the lab, perform a cross-site scripting attack that bypasses the WAF and calls the print() function
                                                 
```
  
  
Như tên bài tag và các attributes bị blocked khi nhập vào thanh search: 
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/13b54cba-61e1-48c3-a5fa-76a98439089c)

Tuy vậy vẫn còn vài tag chừa lại:
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/554dc529-4924-4d43-842d-dba62e605db8)
  
Ta sẽ dùng thẻ body, tiếp tục tìm attribute còn dùng được: 
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/11e376ba-532a-41bc-b2e9-cf42fb8d2584)
  
Ở đây ta sẽ dùng hàm onbeforeinput, khi thực thi người dùng chỉ cần nhập vào thanh search là nó sẽ tự động thực thi alert trước:
  
> <body onbeforeinput=alert(1)>

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/242780f9-d7ba-4d76-b7cd-269e39c853dd)

Tuy vậy bài muốn ta thực thi print(), thay alert(1) bằng hàm print là được:
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/1ef1b6b3-65d0-4975-9aa4-c44d1dc97099)

  
Việc còn lại là gửi đến cho nạn nhân, tuy nhiên thì onbeforeinput ở đây sẽ không được thực thi tự động mà phải có hành động với việc nhập vào thanh search, nên ta sẽ đổi sang th onresize và thực thi tự động với iframe bằng cách thêm attribute onload: 
  
> <iframe src="https://0aef000703d6e364806f0d8d00e00027.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
  
  
## [Reflected XSS into HTML context with all tags blocked except custom ones](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-html-context-with-all-standard-tags-blocked)
  
```
  
This lab blocks all HTML tags except custom ones.

To solve the lab, perform a cross-site scripting attack that injects a custom tag and automatically alerts document.cookie.
  
```
Như tiêu đề, tất cả các thẻ đều bị block ngoài các custom tag, dựa vào gợi ý ta có thể dùng script sau trên hacktricks:
  
> /?search=<xss+id%3dx+onfocus%3dalert(document.cookie)+tabindex%3d1>#x
  
Trong đó thì ta sử dụng 2 thuộc tính là id và onfocus.  Khi trang web tải mã HTML này, nó sẽ tạo ra một đối tượng <xss> và trang web sẽ focus vào đối tượng này bằng cách sử dụng dấu # trong URL và sau đó nó sẽ thực thi đoạn mã JavaScript được đính kèm trong thuộc tính onfocus
  
> <xss id=x onfocus=alert(document.cookie) tabindex=1>#x

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/0054fea8-0088-4b48-93fd-a2b56024216e)
  
## [Reflected XSS with some SVG markup allowed](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-some-svg-markup-allowed)

```
This lab has a simple reflected XSS vulnerability. The site is blocking common tags but misses some SVG tags and events.

To solve the lab, perform a cross-site scripting attack that calls the alert() function.

```
  
  
## [Lab: Reflected XSS into a JavaScript string with single quote and backslash escaped](https://0aab000203bfdba08116981e007d001e.web-security-academy.net/)
  
```
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality. The reflection occurs inside a JavaScript string with single quotes and backslashes escaped.

To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function.

```
  
Ở bài này input ta nhập vào sẽ được reflect trong 1 khối script, và kí tự ' sẽ bị chuyển thành **\'**: 
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/ad909fd8-0724-4459-918c-b0c826c4eb6e)

  
Để thực thi xss ta cần thoát ra khỏi khối script này, vì nó chỉ escape ' và / nên ta có thể thực thi xss bằng cách đóng </script>
  
> '</script><script>alert(1)</script>
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/d2a22ca0-e502-4a98-acc0-a87b94ccc227)
  
  
## [Lab: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-string-angle-brackets-double-quotes-encoded-single-quotes-escaped)
  
```
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets and double are HTML encoded and single quotes are escaped.

To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function.
  
```
  
Bài này thì ngoài việc ' bị escape ra thì dấu " và <> sẽ bị encode, tức ta sẽ kh thể thực thi như bài trên: 
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/37d4063a-291d-4257-bfaa-817df6400ae3)

Để thực thi xss thì ở đây ta sẽ dùng hàm eval để break đoạn mã và thực thi bên trong nó mà không động gì đến các kí tự bị escape và encode: 
> /?search=';%0Aeval(alert(1));//
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/5bddca7c-18d9-4173-a4ba-a2581f3e204b)

  

## [Lab: Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-template-literal-angle-brackets-single-double-quotes-backslash-backticks-escaped)
  
```
This lab contains a reflected cross-site scripting vulnerability in the search blog functionality. The reflection occurs inside a template string with angle brackets, single, and double quotes HTML encoded, and backticks escaped. To solve this lab, perform a cross-site scripting attack that calls the alert function inside the template string.
  
```
  
Các kí tự ', ", ~,<> đã bị Unicode-escaped, ý tưởng thì ta vẫn có thể dùng eval() hoặc $ thần chưởng trong js để thực thi ${} mà không hề đụng tới đoạn unicode kia,  nhưng để ý kĩ ở đề bài thì biến message ở đây được tạo ra với giá trị là một template literal, chú ý `` ở 2 đầu, vị vậy ta chỉ thực thi được ${}: 
> /?search=1%0A${alert(1)}
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/6c0aa62e-d8db-4076-9ba9-a742ebf77b8a)

  
  
## [Lab: Reflected XSS in a JavaScript URL with some characters blocked](https://portswigger.net/web-security/cross-site-scripting/contexts/lab-javascript-url-some-characters-blocked)
  
```
This lab reflects your input in a JavaScript URL, but all is not as it seems. This initially seems like a trivial challenge; however, the application is blocking some characters in an attempt to prevent XSS attacks.

To solve the lab, perform a cross-site scripting attack that calls the alert function with the string 1337 contained somewhere in the alert message.
  
```

> Referecnce: https://portswigger.net/research/xss-without-parentheses-and-semi-colons
  
Giao diện của bài: 
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/7ba4714e-f8d4-40a8-89c0-8a3061b9cb16)

