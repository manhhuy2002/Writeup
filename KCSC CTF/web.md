## Web challenge


## Bypass Captcha

Giao diện bài:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/00af1fa3-db4b-4028-b695-507c736675b4)


Bài có 1 phần nhập input đầu vào cho trường password và điền capcha để xác thực, khi ta ấn danger thì sẽ có 2 trường hợp xảy ra:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/ff372a51-9f02-4c46-bcfb-6a2b4834dbb4)


![image](https://github.com/manhhuy2002/Writeup/assets/104350480/c57001de-139a-4090-9f73-5ebd9dff48e8)

Một trường hợp là wrong password và 1 trường hợp là Verify captcha failed!, ngoài ra nếu ta kh nhập gì thì sẽ trả về **Pls verify captcha or input password**, ta sẽ cùng phân tích nó ở phần source code đề bài cho: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/77e82664-af4d-4504-8ef3-aafdf4a8a3e8)

Ở file index, ta có thể thấy phần xử lí luồng code này, sẽ có 2 tham số để truyền lên qua phương thức post là passwd và cf-turnstile-response. Tiếp đến là phần xử lí logic code ở dưới, đọc qua thì sẽ kh thấy có bất kì lỗ hổng nào từ việc truyền password đến việc sử dụng capcha để xác thực. Cái duy nhất mình chú ý ở đây là đoạn **if ($data->success == 1 && $now - $challenge_ts <= 5)** dùn gđể kiểm tra xem Captcha đã được xác thực thành công hay không và thời gian xác thực là không quá 5s. Còn lại thì mình kh thấy có lỗi nào. Ở bài này mãi sau mình mới để ý đến file config.php:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/18c64d26-cc0c-42f0-8edc-614c6aa30ca9)


  Sau 1 hồi tìm hiểu thì có 1 lỗ hổng có thể khai thác ở đây, việc ta lấy được password đúng với password ở bên phía cơ sở dữ liệu là nohope, nhưng mà để ý hàm parse_str($\_SERVER['QUERY_STRING']); và $PASSWD = getenv('PASSWD');. Cụ thể thì hàm getenv('PASSWD') ở đây được sử dụng để lấy giá trị của biến môi trường có tên là PASSWD, tức là PASSWD bên phía server. Tiếp đến ở cái parse_str() được sử dụng để chuyển đổi một chuỗi query  trong URL thành các biến và giá trị trong PHP và nó có thể ghi đè lên $PASSWD ở trên. Ý tưởng của ta ở đây sẽ là nhập mk bất kì, sau đó ta sẽ truyền QUERY_STRING là ?PASSWD= khi POST lên với mật khẩu tương ứng đó để ghi đè lên giá trị lấy từ biến môi trường ở trên. Ở đay mình nhập pass=1 và thêm ?PASSWD=1 khi POST:
  
![image](https://github.com/manhhuy2002/Writeup/assets/104350480/7bd4214e-e3a9-4ca1-964c-0a2bddd324c3)

Tuy vậy thì có 1 vấn đề ở đây là capcha sẽ không tồn tại quá 5s như đã phân tích lúc đầu, nếu không nó sẽ báo lỗi:

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/2cb6aa7d-a289-4aca-a830-58d19abc05e8)

Ở đây mình sẽ thay nhanh hết mức có thể để nó có thể bypass được điều kiện này và flag được trả về: 

![image](https://github.com/manhhuy2002/Writeup/assets/104350480/3537ecc2-9ff0-44c9-afde-40313a76ef3b)

> Flag: KCSC{Bypass_Turnstile_Cloudflare_1e22c0f8}

