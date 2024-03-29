# JerseyCTF - Writeup web challenge: 
* [1. put-the-cookie-down](#1)
* [2. look-im-hacking](#2)
* [3. i-got-the-keys](#3)
* [4. Timeless](#4)
* [5. ninja-jackers](#5)
* [6. Poisoned](#6)
* [7. xss-terminator](#7)
* [8. avenge-my-password](#8)

# Hack the box: 

* [1. Templated](#htb1)
* [2. Neonify](#htb2)
* [3. LoveTok](#htb3)

## [1. put-the-cookie-down](https://jerseyctf-put-the-cookie-down.chals.io/)<a name="1"></a>

Ctrl Shilft I ta được:

![image](https://user-images.githubusercontent.com/104350480/232238888-c18e6771-1095-41a5-bcb5-cc02dc7a582f.png)

> FLag: I_WILL_BE_BACK_FOR_MORE_C00KI3S!

### [2. look-im-hacking](https://www.jerseyctf.online/)<a name="2"></a>

![image](https://user-images.githubusercontent.com/104350480/232239086-e3e3e07f-93ce-435e-84d4-d167a3eea92c.png)

Ctrl U có: 

![image](https://user-images.githubusercontent.com/104350480/232239104-5a13832c-bf05-4fc5-bb3a-fbe2a459007e.png)

Flag: 

![image](https://user-images.githubusercontent.com/104350480/232239134-7e9f5b12-2d44-4b81-9444-9906f365ed4f.png)

## [3. i-got-the-keys](https://ctf.jerseyctf.com/challenges#i-got-the-keys-35)<a name="3"></a>

![image](https://user-images.githubusercontent.com/104350480/232287618-3e4cf226-d0da-4011-af37-fbc2f81adb47.png)

Ctrl U ta được: 

![image](https://user-images.githubusercontent.com/104350480/232287630-3844f786-744d-439e-85ef-3c5a8e9913e1.png)

Ta có endpoint test, với điều kiện phải có headers: {
        'authorization_key': 'GdERHpBh3x'
      }
 Nếu không ta sẽ có lỗi như sau:
 
 ![image](https://user-images.githubusercontent.com/104350480/232289946-0766ef4e-a61d-4d9f-a908-80ec27c179dc.png)

Ném qua burpsuite thêm authorization_key: GdERHpBh3x vào header ta sẽ được: 

![image](https://user-images.githubusercontent.com/104350480/232290166-e86b8b2e-4a16-410f-a254-6f5eac7b04f0.png)

Nhưng như vậy thì cũng kh khai thác được gì, ở đây fuff thử trang web xem có đường dẫn nào khác không: 

![image](https://user-images.githubusercontent.com/104350480/232290289-d295c1fc-683c-49ab-8ea1-bb6c552bfbce.png)

Có /flag trả về với lỗi 500, nghĩa là có thể nó bị khóa truy cập như việc thiếu Authorization_key: GdERHpBh3x ở header:

Thay vào burpsuite ta get được flag trả về:

![image](https://user-images.githubusercontent.com/104350480/232290412-4284d9d9-487a-4be8-a20f-d276b88dfdef.png)

> Flag: jctf{*MAJ0R-K3Y-AL3RT*}

## [4. Timeless](http://137.184.135.40:3000/)<a name="4"></a>

![image](https://user-images.githubusercontent.com/104350480/232410378-4ebe5bf4-2664-403a-b70f-fe6b15e14312.png)

Giao diện của bài:

![image](https://user-images.githubusercontent.com/104350480/232410122-74be4254-da53-4810-8bd3-c4a3190dfaef.png)

Thử sql injection đơn giản xem có bypass authen được không thì trả về lỗi: 

![image](https://user-images.githubusercontent.com/104350480/232410500-cb54bc5f-50c0-4cc5-bf07-0c405ed5fae0.png)

Tiếp tục thử ở password: 
```
username: admin
password: ' OR 1=1 -- -

```
Ta bypass được luôn, bài kh filter ở phần password: 

![image](https://user-images.githubusercontent.com/104350480/232410660-ad14eb2b-4fef-4234-80c2-d7e68db9a9c9.png)

> Flag: jctf{LOVE_ALL_TRUST_A_FEW_&_DO_WRONG_TO_NONE}


## [5. ninja-jackers](https://jerseyctf-ninja-jackers.chals.io/)<a name="5"></a>

![image](https://user-images.githubusercontent.com/104350480/232291689-ff7fee7f-1c66-4b00-a5b8-643e9cf8e296.png)

Truy cập vào trang ta được:

![image](https://user-images.githubusercontent.com/104350480/232291763-0fbb7bec-637b-4342-a0ae-fde96c345f2a.png)

Sang phần /contact: 

![image](https://user-images.githubusercontent.com/104350480/232291697-e28e682b-db0d-4472-8e56-869a9b0d73bc.png)

Ta thấy thông báo /contact được trả về, nghĩ đến khả năng đây là ssti, ta thử \{{7\*7}} xem và được: 

![image](https://user-images.githubusercontent.com/104350480/232292156-30cbba98-3c19-4acf-ba8d-adba1b8cd3b8.png)

Vậy là nó dính SSTI rồi, giờ ta thử tiếp để xem đây là template gì, thử {{7\*'7'}}:

![image](https://user-images.githubusercontent.com/104350480/232292404-8c8a316a-bd7d-41e5-917c-a8303109b34e.png)

Cả 2 đều không lỗi ta có thể kết luận tempalate này là jinja2, giờ hoặc tự tạo builtin rồi import os và dùng cmd hoặc dùng luôn hàm sẵn đều được: 

![image](https://user-images.githubusercontent.com/104350480/232293106-b378c27f-0d72-4523-bd9e-f4abc9457382.png)

![image](https://user-images.githubusercontent.com/104350480/232293265-610b9fe0-3f93-4cdc-b349-30580a5d286e.png)

Ta được file FLAG_is_H3RE.txt, giờ cat file ra là ta có flag: 

![image](https://user-images.githubusercontent.com/104350480/232293382-4518b3e0-c046-452c-97dd-218ba340fe4b.png)

> Flag: jctf{Ar3Nt_Y0U_GLaD_I_Didnt_SAY_NINJA}

## [6. Poisoned](https://jerseyctf-poisoned.chals.io/?page=welcome)<a name="6"></a>

![image](https://user-images.githubusercontent.com/104350480/232411154-3e2ba221-99fc-4159-932d-fda0b955fe18.png)

Giao diện của bài đang get dữ liệu qua tham số truy vấn page, ta thử thay giá trị thành a xem sao: 

![image](https://user-images.githubusercontent.com/104350480/232411976-e6a1583f-31d1-4540-b842-e9052618ab76.png)

Lỗi hiển thị đường dẫn hiển thị trên màn hình, đặc trưng của lfi và path traversal, ngoài ra nó đang ở trong thư mục pages, giờ ta sẽ thử đọc file hệ thống xem được không: 

![image](https://user-images.githubusercontent.com/104350480/232413381-6e35d0b4-5372-4f4f-a401-07a29c8fb009.png)

Bài có filter đi ../, nhưng ở đây bài không filter đệ quy, nó chỉ filter 1 lần, ta sửa thành ....// để tiếp tục:

![image](https://user-images.githubusercontent.com/104350480/232413910-7c2191b1-d124-4688-a511-085c412b079f.png)

Nhưng đọc được file hệ thống như thế này thì cũng kh giải quyết được gì vì ta cần biết chính xác vị trí của tệp flag được giấu trong bài, ở đây ta sẽ thực hiện inject qua vul log poisoning như tên đề đã gợi ý: 
Trước hết truy cập vào file log, ....//....//....//....//var/log/apache2/access.log ta được:

![image](https://user-images.githubusercontent.com/104350480/232415890-e4d7914a-3251-45ca-ab20-42a4b8203229.png)


Thông thường ta sẽ tiêm shell code thông qua user-agent như dưới để có thể thực thi được các lệnh: 

![image](https://user-images.githubusercontent.com/104350480/232416062-c135f743-adda-47b6-bce8-f89593f47343.png)

Nhưng có vẻ bài đã cho sẵn shell code nên giờ ta cần truyền nó qua tham số truy vấn poison là xong, thử với đường dẫn /?page=....//....//....//....//....//....//var/log/apache2/access.log&poison=ls , ta được:

![image](https://user-images.githubusercontent.com/104350480/232416732-2203b627-282d-4bf8-a699-63c7d8363de9.png)

Giờ cần tìm vị trí file flag và đọc nó ra là xong, thay ls thành ls / xem được gì: 

![image](https://user-images.githubusercontent.com/104350480/232417077-f7172281-f22a-4d10-aae7-8d69a380267d.png)

Ta có được file secret_fl4g.txt, tiếp tục cmd: &poison=cat /secret_fl4g.txt ta được flag của bài: 

![image](https://user-images.githubusercontent.com/104350480/232417516-17a28318-483f-4f59-8080-aeec743a5d31.png)

> Flag: jctf{4PachE_L0G_POiS0nInG}


## [7. xss-terminator](http://198.211.99.71:3000/)<a name="7"></a>

![image](https://user-images.githubusercontent.com/104350480/232418058-74501bc5-8c33-477c-9606-faa9297c1237.png)

Giao diện của bài: 

![image](https://user-images.githubusercontent.com/104350480/232418129-943f91be-d620-4635-bf01-f2550f3a8c25.png)


![image](https://user-images.githubusercontent.com/104350480/232418176-ace4d091-0bff-49a7-8f45-8fa46d0a56b5.png)

Thử xss cơ bản xem sao: 

![image](https://user-images.githubusercontent.com/104350480/232418413-6794c250-787d-4efd-950c-2db50bd5897d.png)

Kh alert được, ta thử cái khác, <svg/onload=alert(document.domain)>

Nhưng bài này kh có chỗ nộp để lấy document.cookie, đọc lại đề bài chút:

![image](https://user-images.githubusercontent.com/104350480/232418579-8bfdb8b0-0c8c-4fd9-9f5e-89067f47681b.png)


```
The Terminator told you to put the cookie down, and you didn't listen. Now he is very angry! You must find a way to steal the cookie from the vulnerable website and send it to the evil server where the Terminator is waiting... just don't make him wait too long.

```

Ở đây bài nói cookie chưa được hạ xuống, có vẻ vẫn lưu trên web và dựa vào giao diện web t2 thì hướng đi giờ chỉ cần lấy document.cookie và thực hiện như này là xong: **Steal the cookie and send it to my /cookie?data endpooint** , tên là bài hard nhưng hơi lạ.

Ta chỉ việc vào thẳng web lấy: 

![image](https://user-images.githubusercontent.com/104350480/232419459-dfe4d57e-8723-4acc-872b-227439457966.png)

Và sau đó gửi nó qua bên trang web còn lại để lấy flag: 

![image](https://user-images.githubusercontent.com/104350480/232419696-19f73d14-714c-4e6f-9eb0-2e0fb47948f5.png)

Load lại trang là có flag:

![image](https://user-images.githubusercontent.com/104350480/232419832-6f432f4e-7333-4199-92f3-aacc4dc11847.png)

> Flag: Flag: jctf{who_said_you_could_open_the_cookie_jar!?}

## [8. avenge-my-password](http://159.203.191.48/)<a name="8"></a>


![image](https://user-images.githubusercontent.com/104350480/232690800-6374c3a3-2567-420a-b7b9-965852b88593.png)

Ta truy cập đường dẫn và nhập bất kì: 

![image](https://user-images.githubusercontent.com/104350480/232692157-49ef18a5-3363-4683-a5db-9b6703ee0395.png)

Đề bài cho phép ta kết nối ssh sang máy chủ của họ: 

![image](https://user-images.githubusercontent.com/104350480/232692416-be1cf6fb-f059-4da2-9bc7-0a796d023b2b.png)

Check thử ls -lsa, có mysql_history, ta cat thử xem file có gì: 

![image](https://user-images.githubusercontent.com/104350480/232692554-dde91d94-4d2e-4571-b5a5-9409c47ed756.png)

Tiếp tục ta truy cập vào mysql của hệ thống xem có gì với cmd: **mysql -p** với password như đề bài cho: 

![image](https://user-images.githubusercontent.com/104350480/233173388-21c7497b-cebd-4db1-89ac-0caee4ff6e47.png)

Thực hiện truy vấn trong table login: **select * from login;** ta được 500 mật khẩu trả về dạng băm, để ý ở userID 401, có một mật khẩu ở dạng rõ: 

![image](https://user-images.githubusercontent.com/104350480/233173563-1c4a76b4-bcca-4aaf-98b0-f69726df8b03.png)

Ta sẽ dùng mật khẩu này để thực hiện đăng nhập. 

Tiếp theo ta cần tìm được username, truy cập vào đường dẫn /var/www/html nơi chứa các tệp của website đang được dựng lên: 

![image](https://user-images.githubusercontent.com/104350480/233174527-0effa0eb-d8af-4a47-b689-610f7f4a1a3f.png)

Ta không đọc được file .usernames.txt trực tiếp như vậy vì ta kh được cấp quyền root, tuy vậy ta có thể đọc nó qua đường dẫn trên trang web với path: **http://159.203.191.48/.username/.usernames.txt** và được một danh sách username trả về:

![image](https://user-images.githubusercontent.com/104350480/233175266-c40ad5fd-0672-4451-9185-fcaf0c6cd174.png)

Có danh sách username và password rồi, bước tiếp theo chỉ cần viết script brute-force và ngồi đợi là xong: 

```

import requests
usernames = open("./username.txt","r").readlines().split("/n")
print(usernames)
password ="Spring2023!!!"
for username in usernames:
    print(f"{username}=")
    r = requests.post("http://159.203.191.48/", data = {"username":username, "password":password, "submit":"Login"})
    if "Invalid" not in r.text:
        print(r.text)
        break

```

![image](https://user-images.githubusercontent.com/104350480/233179203-cfa69d82-4bc6-4adf-8c02-6c16791515c1.png)


> FLag: jctf{w3_LoV3_M@RV3L_:)_GOOD_JOB!}


# Hack The Box Lab 

## [1. Templated]<a name="htb1"></a>

```

Can you exploit this simple mistake?

```

![image](https://user-images.githubusercontent.com/104350480/233298712-b22805a1-9811-45d9-b733-ddf90c29517b.png)

Một chall đơn giản về ssti, vào giao diện ta có thể thấy nó gợi ý luôn cho ta về việc trang web đang dùng template jinja2.
Test thử một số payload: 

![image](https://user-images.githubusercontent.com/104350480/233298939-284f534f-aa0a-40e4-8686-bae61b449cb2.png)

![image](https://user-images.githubusercontent.com/104350480/233299010-07120251-de4b-4935-9ae1-37007c5bfda8.png)

Giờ ta sẽ tự built payload để thực hiện lệnh trong hệ thống. Trong jinja2 template, thì ta có thể sử dụng TemplateReference để tái sử dụng lại các code block từ template. Chẳng hạn, để tránh viết lại tiêu đề trên tất cả các template chẳng hạn, thì ta có thể đặt tiêu đề trong 1 khối block chẳng hạn như {% block title%} và truy xuất nó với {{self.title()}} sau đó. Ta có thể truy cập TemplateReference obj mà kh cần các điều kiện, ngay cả khi không có biến nào được đặt trong render(): 

>>> jinja2.Template("My name is {{ self }}").render()
'My name is <TemplateReference None>'

Dựa vào đó ta sẽ xây dựng pyaload để cho phép truy cập vào các os module từ TemplateReference obj.

### Building a classic payload: 
Ý tưởng cổ điển để sử dụng TemplateReference obj là truy cập tới các hàm **__builtins__** và sử dụng hàm **__import__** để import module chúng ta muốn. Đầu tiên ta sẽ sử dụng biến toàn cục __globals__ thông qua: self.__init__.__globals__

Trong __globals__, chúng ta có thể sử dụng các hàm buidtins sẵn, trong đó có chứa hàm __import__. Ta sẽ sử dụng trực tiếp module os như sau: 
```
{{self.__init__.__globals__.__builtins__.__import__('os').popen('id').read()}}

```
Ta sử dụng popen để thực thi lệnh hệ thống id và trả về đối tượng có thể đọc được thông qua output của nó thông qua phương thức read. 

Như lệnh ở trên ta có thể khai thác được bài này rồi. Ngoài ra có thể đơn giản hơn là dùng hàm sẵn không cần thông qua builtins như trên vì thường nó sẽ bị filter: 

![image](https://user-images.githubusercontent.com/104350480/233304119-21d3c136-a2cd-47fc-b23d-46257d5f9832.png)

Cái trên dưới là tương tự nhau nhưng cái dưới là bản shortcut đơng giản hơn. Còn cycler , joiner và namespace là gì thì ta có thể hiểu đơn giản là nó là nơi mà hàm os trong hệ thống đã được gọi sẵn, thì khi gọi đến nó thì os sẽ được gọi mà kh cần phải import nữa. Giờ ta tìm và cat flag ra là xong: 

![image](https://user-images.githubusercontent.com/104350480/233304665-514006a4-4582-41e5-9e87-23d457d3e560.png)

![image](https://user-images.githubusercontent.com/104350480/233304810-f417a39d-2dbe-46d0-90f6-fe238b123de5.png)

> Flag: HTB{t3mpl4t3s_4r3_m0r3_p0w3rfu1_th4n_u_th1nk!}
        




## [2. Neonify]<a name="htb2"></a>

```
It's time for a shiny new reveal for the first-ever text neonifier. Come test out our brand new website and make any text glow like a lo-fi neon tube!

```

Giao diện của bài:

![image](https://user-images.githubusercontent.com/104350480/233559237-9cd64465-1c7b-48fc-922f-e15a06356676.png)

 Bài cho ta một form nhập, nhập cái gì thì hiện lên trang web cái đó, trừ mấy kí tự đặc biệt: 
        
![image](https://user-images.githubusercontent.com/104350480/233559381-554121ae-da3b-4599-9603-38afbd2170b7.png)
        
Dạng đặc trưng của SSTI, bài cho ta source code: 

![image](https://user-images.githubusercontent.com/104350480/233559541-e365e7c0-3ef5-4f47-b228-4a97a30ab41d.png)

```
        
        class NeonControllers < Sinatra::Base

  configure do
    set :views, "app/views"
    set :public_dir, "public"
  end

  get '/' do
    @neon = "Glow With The Flow"
    erb :'index'
  end

  post '/' do
    if params[:neon] =~ /^[0-9a-z ]+$/i
      @neon = ERB.new(params[:neon]).result(binding)
    else
      @neon = "Malicious Input Detected"
    end 
    erb :'index'
  end

end
                                             
```

Bài đang dùng template ERB của ruby, phân tích một chút.
**params[:neon] =~ /^[0-9a-z ]+$/i**, dòng điều kiện này giống như biểu thức chính quy regex với "=~" được sử dụng để so sánh giá trị của tham số "neon" với biểu thức chính quy từ 0 đến 9 và từ a đến z và không phân biệt hoa thường. Mục tiêu của ta giờ là phải bypass được nó để thực hiện ssti. 
Ta để ý đoạn regex bắt đầu từ ^ và kết thúc với $, đọc tài liệu ta sẽ có thông tin như sau: 

![image](https://user-images.githubusercontent.com/104350480/233561661-079bbcb6-5483-4e53-81d0-fa33a8a36917.png)

tức là ^ và $ sẽ thực hiện regex bắt đầu và kết thúc cho 1 dòng chứ không phải 1 chuỗi, vậy nếu ta điền 1 dòng hợp lệ và ở dòng thứ 2 ta nhập bất cứ gì thì có thể sẽ thành công, trick quen thuộc sẽ là dùng %0A hoặc là \n. 
                                          
![image](https://user-images.githubusercontent.com/104350480/233562145-13c5f3c0-4c0d-4b13-b53e-b1ccc6b6b0af.png)

Oke vậy là ổn, giờ ta sẽ thực hiện cmd ssti của erb, một số lệnh ssti của erb: 
                                             
 ```
                                             
{{7*7}} = {{7*7}}
${7*7} = ${7*7}
<%= 7*7 %> = 49
<%= foobar %> = Error
<%= system("whoami") %> #Execute code
<%= Dir.entries('/') %> #List folder
<%= File.open('/etc/passwd').read %> #Read file

<%= system('cat /etc/passwd') %>
<%= `ls /` %>
<%= IO.popen('ls /').readlines()  %>
<% require 'open3' %><% @a,@b,@c,@d=Open3.popen3('whoami') %><%= @b.readline()%>
<% require 'open4' %><% @a,@b,@c,@d=Open4.popen4('whoami') %><%= @c.readline()%>                                         
                                             
```
        
![image](https://user-images.githubusercontent.com/104350480/233562696-036b2e24-ffb5-4539-a0f7-5a64f88fddb4.png)

Giờ tìm vị trí chứa flag nữa là xong: thay bằng lệnh **find / -name "flag\*"** để tìm kiếm 
        
![image](https://user-images.githubusercontent.com/104350480/233563694-5523ff49-bc16-4214-bd2b-a814ded25ba4.png)

Đọc flag nữa là xong: 
        
![image](https://user-images.githubusercontent.com/104350480/233563851-de721d80-7c5d-42dc-952c-071fcbe32dc5.png)
        
> Flag: HTB{r3pl4c3m3n7_s3cur1ty}
    
## [3. LoveTok]<a name="htb3"></a>
        
Giao diện của bài: 
        
![image](https://user-images.githubusercontent.com/104350480/233661647-28e5f7e9-72b8-494a-8b03-83949a4a44a7.png)

Bài không có phần nào nhập hay gì hết, bài cho ta source code, ta sẽ phân tích xem khai thác được gì không:

![image](https://user-images.githubusercontent.com/104350480/233661882-0def240b-20ab-4557-b0ca-6e9bb57ea59c.png)

![image](https://user-images.githubusercontent.com/104350480/233661975-0ae68f43-5547-4ed8-a6e8-b85bdf2d7570.png)

Để ý có 2 đoạn code ở trên, đoạn đầu kiểm tra xem có tham số format được truyền vào không thông qua biến $_GET hay không, nếu $_GET['format'] tồn tại, $format sẽ được gán với giá trị đó, nếu không, $format sẽ được gán với giá trị 'r'. Tuy vậy thì đọc code ở đoạn 2 thì nó cũng chỉ là giá trị random sẽ được in ra màn hình:

![image](https://user-images.githubusercontent.com/104350480/233663812-f94f93d1-43cd-41a1-aed8-d650363e2cc0.png)

Để ý ở đoạn code thứ 2, khi tra truyền giá trị vào tham số format, nó sẽ đi qua hàm addslashes, **$this->format = addslashes($format);** - phương thức addslashes() trong PHP được sử dụng để thêm ký tự backslash (\) trước các ký tự đặc biệt trong một chuỗi, như các ký tự nháy đơn ('), nháy kép ("), backslash (\) và NULL (\0). Để ý hàm exec ở dưới, tức bài muốn ngăn ta không thực thi các câu lệnh với các dấu như ",' . Vậy hướng đi của ta bây giờ là sẽ bypass hàm addslashes này, để có thể thực thi được lệnh hệ thống. Trước hết ta thử thực thi lệnh mà kh cần dùng đến ",',\. Chẳng hạn phpinf() với việc thông qua dùng một lệnh tương tự như đô la thần chưởng bên js: 
**${phpinfo()}**
        
![image](https://user-images.githubusercontent.com/104350480/233665878-be5d7b8b-a0cc-468a-9b82-490cc4f7ee7b.png)

Thực hiện được luôn, giờ ta muốn thực hiện cmd trong hệ thống, thì cú pháp sẽ là <?php system("ls");?> tuy vậy thì cú pháp này không thực thi được, ta sẽ thay thế bằng $, ${system("ls")}, lệnh này cũng không vì vẫn có dấu "". Ở đây có 1 trick là ta có thể dùng $_GET trong php như một mảng kết hợp chứa các tham số được truyền vào từ URL, ta có thể định danh nó bằng việc dùng nó như 1 mảng. Chẳng hạn $_GET[0]&0=ls sẽ thay thế cho "ls". Ta thực thi lệnh như sau: 

![image](https://user-images.githubusercontent.com/104350480/233667494-22782f6d-3786-4aa4-ac2c-f576ab0e8839.png)

Ta thực thi thành công, giờ ta sẽ dùng lệnh find tìm nơi chứa flag luôn: find / -name "flag*"
        
![image](https://user-images.githubusercontent.com/104350480/233667674-e08a13d3-abd9-4fc1-951b-3a8dad532eb5.png)

Giờ cat flag nữa là xong: 

![image](https://user-images.githubusercontent.com/104350480/233667789-25497a69-517f-4e89-b045-d0c8c5a9e1ef.png)

> FLag: HTB{wh3n_l0v3_g3ts_eval3d_sh3lls_st4rt_p0pp1ng}

     
