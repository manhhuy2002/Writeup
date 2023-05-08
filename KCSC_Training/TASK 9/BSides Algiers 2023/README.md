# BSides Algiers 2023 web challenges

![image](https://user-images.githubusercontent.com/104350480/236913082-1835eb92-bcb5-4250-9ede-e509efa9761e.png)

## Jokes Hub 1

![image](https://user-images.githubusercontent.com/104350480/236913308-6badb50a-f0d2-47c1-9b62-7a6af17fe0dd.png)

Giao diện của bài như sau: 

![image](https://user-images.githubusercontent.com/104350480/236913487-34176c45-6e2f-49c7-84d6-bbc0d501abee.png)

Bài cho ta 2 nút ấn, 1 là get a joke và 2 là show punchline, khi ta ấn vào get a joke thì nó sẽ hiển thị 1 câu bất kì, sau đó show punchline xuất hiện và nêu ta ấn vào thì nó sẽ hiển thị 1 câu quotes gì đó bất kì. Ta ném vào burpsuite xem thử: 

<img>

Ta có thể thấy đây là một truy vấn POST với tham số truy vấn là {"joke":7} và kết quả trả về là một JSON object chứa giá trị {"result":"Why did the scarecrow win an award?"}. Tương tự với punchline, nó cũng thực hiện truy vấn như vậy:

![image](https://user-images.githubusercontent.com/104350480/236914823-8a856c05-993a-4391-9117-d3e15a8c5809.png)

Bài đang lấy dữ liệu từ phía server trả về trên màn hình của ta, tham số joke hay punchline ở đây có thể hiểu là đang được dùng như 1 cột với giá trị của tham số tương ứng để trả về kết quả, hiểu đơn giản là mã id chẳng hạn. Ta thay đổi nó thì sẽ được các results khác nhau trả về tương ứng với mỗi loại. Ở đây nếu ta có thể khai thác sqli thì sao? Bài này thì hướng đi là fuzz hết các kiểu để xem nó là loại gì thôi, còn cũng dễ đoán đây là sqli thuộc kiểu boolean. 

fuzz độ dài kí tự của bảng:

![image](https://user-images.githubusercontent.com/104350480/236916985-7696bbc5-35aa-412e-9b34-a14b4019d23e.png)

Được <=5,sau đó thì ta fuzz các bảng có thể có bằng substr: 

![image](https://user-images.githubusercontent.com/104350480/236918341-c18798a3-5726-4f26-a3bc-999877de6aea.png)

Ghép vào ta sẽ được các bảng là: flags, jokes, notes, sqli ( còn muốn check thì ta có thể dùng check điều kiện với like để thử là thấy ngay)

![image](https://user-images.githubusercontent.com/104350480/236917483-0b0d71c0-d5b4-4430-9b35-6286a6c224bb.png)

Vì cần lấy flags nên ta sẽ đi tiếp tục mò kim ở table flags thôi :)) Ở đây ta sẽ dùng truy vấn tên cột với cú pháp:

> (CASE WHEN EXISTS(SELECT name FROM pragma_table_info('flags') WHERE substr(name,1,1)='a') THEN 'hhh12313213123' ELSE '0' END)

Tương tự như tên ta sẽ tìm ra được cột là flag:

![image](https://user-images.githubusercontent.com/104350480/236921369-12bbe4d4-d087-4a86-b21d-4fc010718993.png)

Giờ dump dữ liệu ra nữa là xong: 

> FLag: shellmates{ar3_sqli_still_4_THing?}
