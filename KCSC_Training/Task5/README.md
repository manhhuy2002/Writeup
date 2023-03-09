


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


## DOM-based vulnerabilities

Kiến thức về Dom:
```
DOM (Document Object Model) là một mô hình lập trình cho phép các trang web được biểu diễn dưới dạng một cây phân cấp các đối tượng, trong đó mỗi đối tượng đại diện cho một phần của trang web, chẳng hạn như một thẻ HTML, một đoạn văn bản, một hình ảnh hoặc một form nhập liệu.

DOM bao gồm các thành phần sau:

Document Object: Đại diện cho toàn bộ trang web, bao gồm các phần tử, thuộc tính, văn bản và hình ảnh được hiển thị trên trang.

Element Object: Đại diện cho các phần tử HTML trên trang, ví dụ như thẻ <div>, <p>, <img>, v.v. Mỗi phần tử có các thuộc tính như id, class, style, v.v.

Attribute Object: Đại diện cho các thuộc tính của một phần tử HTML, chẳng hạn như src, href, alt, v.v.

Text Object: Đại diện cho các đoạn văn bản trong các phần tử HTML, ví dụ như nội dung của một thẻ <p>.

Event Object: Đại diện cho các sự kiện xảy ra trên trang web, chẳng hạn như khi người dùng nhấp chuột vào một phần tử, di chuyển chuột qua phần tử, v.v.

```

DOM-based vulnerabilities xảy ra khi trang web chứa js code mà attacker có thể control được giá trị là souce và chuyển nó vào vào 1 hàm nguy hiểm được gọi là sink.
Để hiểu rõ lỗi này trước tiên thì ta có thể hiểu code cũng được mà ở đây ta sẽ đi từ căn bản nhất là đi từ 'dòng chảy' của code thực hiện giữa source và sink:

Source: 
```
Trong taint flow của DOM, "source" là một giá trị đáng nghi của một trường nhập liệu trên trang web. Nó có thể là một chuỗi hoặc một giá trị khác được nhập vào từ bên ngoài, chẳng hạn như thông tin nhập từ người dùng hoặc các giá trị được truyền qua URL.

Khi giá trị này được đưa vào trang web, nó trở thành một "source" đáng nghi vì nó có thể chứa các ký tự đặc biệt hoặc các mã độc được chèn vào để tấn công trang web.

Từ đó, giá trị này sẽ được truyền dọc theo các thành phần khác nhau của trang web thông qua các đối tượng DOM, và nếu không kiểm soát được, nó có thể gây ra các lỗ hổng bảo mật.

Vì vậy, để bảo vệ trang web khỏi các cuộc tấn công tương tự, các dev cần kiểm soát và xử lý kỹ càng các "source" trong taint flow của DOM, bằng cách sử dụng các kỹ thuật như input validation, output encoding, và escape characters để đảm bảo an toàn cho trang web.
```
Sink:
```
sink là 1 đối tượng dom hoặc 1 hàm js có chứa sự nguy hiểm 
```


