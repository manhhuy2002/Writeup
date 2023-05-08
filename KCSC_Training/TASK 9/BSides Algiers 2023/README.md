# BSides Algiers 2023 web challenges
* [Website checkers](#wb1)
* [Jokes Hub 1](#jh1)

![image](https://user-images.githubusercontent.com/104350480/236913082-1835eb92-bcb5-4250-9ede-e509efa9761e.png)

<hr>

## [Website checkers](https://website-checker.bsides.shellmates.club/)<a name="wb1"></a>

![image](https://user-images.githubusercontent.com/104350480/236922439-e12117cb-5baa-4379-bbcd-102dbb75b78b.png)

Giao diện của bài là 1 ô nhập đường dẫn url và ta sẽ check được trạng thái của trang đó:

![image](https://user-images.githubusercontent.com/104350480/236922693-7e580fb5-8d83-4f28-a672-b3ccb2c3e375.png)

Ném qua burpsuite thì xem thử: 

![image](https://user-images.githubusercontent.com/104350480/236922987-d38b90be-035d-42b5-8098-664af5b02115.png)

Nó thực hiện post với 2 tham số ở đây là host và options, mình chỉ mới biết host là để truyền url vào còn option ở đây chưa biết để làm gì. Lúc đầu mình nghĩ hướng đi bài này là os command injection nhưng kh phải. Sau được a bạn Qq hint cho là hãy check thử request và phân tích thử xem có thể khai thác được gì không thì hướng đi đã được chuyển sang hướng khác. Cụ thể thì mình ném thử 1 đường dẫn của 1 endpoint trên pipedream và và send request đi thì mình nhận được 1 yêu cầu get đến url của mình:

![image](https://user-images.githubusercontent.com/104350480/236924245-08c170e2-bf32-479d-b960-53b997cecc49.png)

Điều thú vị ở đây là khi ta để ý user agent thì ta biết được nó đến từ curl, tức bên con bot phía máy chủ đang dùng lệnh curl để thực thi, vậy options ở đây chính là option của th curl. Đề bài đã gợi ý flag nằm trong /flag, vậy thì ý tưởng của ta ở đây là nếu ta post được /flag từ phía máy chủ nên host của ta thì ta sẽ đợc được nó đúng chứ. Nhưng hiện tại thì curl đang được dùng với GET, nên ta sẽ thêm vào phần options như sau để chuyển sang POST và thực thi: 

![image](https://user-images.githubusercontent.com/104350480/236925162-efb7ad34-dd6f-4a4a-9445-ae3cd91594b1.png)

Trong đó thì: -X được sử dụng để chỉ định phương thức HTTP, -d được sử dụng để gửi dữ liệu trong yêu cầu HTTP và @ được sử dụng để chỉ định tệp chứa dữ liệu cần gửi đi.

Ta kiểm tra đường dẫn và được flag: 

![image](https://user-images.githubusercontent.com/104350480/236922258-fc3c4658-8d62-4c88-b990-b8421994a050.png)

> FLag: shellmates{Curl_4nd_1t5_m4g1c4l_0Pt10n$$}

## [Jokes Hub 1](https://jokes-hub-1.bsides.shellmates.club/)<a name="jh1"></a>

![image](https://user-images.githubusercontent.com/104350480/236913308-6badb50a-f0d2-47c1-9b62-7a6af17fe0dd.png)

Giao diện của bài như sau: 

![image](https://user-images.githubusercontent.com/104350480/236913487-34176c45-6e2f-49c7-84d6-bbc0d501abee.png)

Bài cho ta 2 nút ấn, 1 là get a joke và 2 là show punchline, khi ta ấn vào get a joke thì nó sẽ hiển thị 1 câu bất kì, sau đó show punchline xuất hiện và nêu ta ấn vào thì nó sẽ hiển thị 1 câu quotes gì đó bất kì. Ta ném vào burpsuite xem thử: 

![image](https://user-images.githubusercontent.com/104350480/236922110-35fd9e1a-52d3-4409-8153-4b97829c6668.png)


Ta có thể thấy đây là một truy vấn POST với tham số truy vấn là {"joke":7} và kết quả trả về là một JSON object chứa giá trị {"result":"Why did the scarecrow win an award?"}. Tương tự với punchline, nó cũng thực hiện truy vấn như vậy:

![image](https://user-images.githubusercontent.com/104350480/236914823-8a856c05-993a-4391-9117-d3e15a8c5809.png)

Bài đang lấy dữ liệu từ phía server trả về trên màn hình của ta, tham số joke hay punchline ở đây có thể hiểu là đang được dùng như 1 cột với giá trị của tham số tương ứng để trả về kết quả, hiểu đơn giản là mã id chẳng hạn. Ta thay đổi nó thì sẽ được các results khác nhau trả về tương ứng với mỗi loại. Ở đây nếu ta có thể khai thác sqli thì sao? Bài này thì hướng đi là fuzz hết các kiểu để xem nó là loại gì thôi, còn cũng dễ đoán đây là sqli thuộc kiểu boolean. 

fuzz độ dài kí tự của bảng:

![image](https://user-images.githubusercontent.com/104350480/236916985-7696bbc5-35aa-412e-9b34-a14b4019d23e.png)

Thử lần lượt ta sẽ có độ dài của tables <=5,sau đó thì ta fuzz các bảng bằng substr: 

![image](https://user-images.githubusercontent.com/104350480/236918341-c18798a3-5726-4f26-a3bc-999877de6aea.png)

Ghép vào ta sẽ được các bảng là: flags, jokes, notes, sqli ( còn muốn check thì ta có thể dùng check điều kiện với like để thử là thấy ngay)

Vì cần lấy flags nên ta sẽ đi tiếp tục mò kim ở table flags thôi :)) Ở đây ta sẽ dùng truy vấn tên cột với cú pháp:

> (CASE WHEN EXISTS(SELECT name FROM pragma_table_info('flags') WHERE substr(name,1,1)='a') THEN 'hhh12313213123' ELSE '0' END)

Tương tự như tên ta sẽ tìm ra được cột là flag:

![image](https://user-images.githubusercontent.com/104350480/236921369-12bbe4d4-d087-4a86-b21d-4fc010718993.png)

Giờ dump dữ liệu ra nữa là xong: 

> "(CASE WHEN EXISTS(SELECT flag FROM flags where substr(flag,§35§,1)='§a§') THEN '1123243432' ELSE '1' END)":5}

![image](https://user-images.githubusercontent.com/104350480/236917483-0b0d71c0-d5b4-4430-9b35-6286a6c224bb.png)

> FLag: shellmates{ar3_sqli_still_4_THing?}
