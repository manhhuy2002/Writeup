# Trần Mạnh Huy  SSTI-Server side template injection

* [Lab 1: Basic server-side template injection](#lab-1-basic-server-side-template-injection)
* [Lab 2: Basic server-side template injection (code context)](#lab-2-basic-server-side-template-injection-code-context)

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

Vậy là đang ở trong thư mục /home/carlos . Để hoàn thành thì ls thử được file **morale.txt** . Việc còn lại để hoàn thành bài lab chỉ cần delete file là xong.
Dùng payload: ***<%= system('rm morale.txt) %>***

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab1_04.jpg)

## Lab 2: Basic server-side template injection (code context)
