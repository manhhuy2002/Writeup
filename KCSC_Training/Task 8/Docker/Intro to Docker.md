## Trong bài này ta sẽ ôn lại cách tương tác và triển khai đối với docker. 
Cụ thể trong đó: 
- các syntax cơ bản trong docker
- chạy và triển khai container
- Hiểu cách phân phối các container Docker sử dụng các images
- Tạo image của riêng mình bằng Dockerfile 
- Cách Dockerfile build các container và sử dụng docker compose để kết hợp nhiều container. 
- Sau đó áp dụng kiến thức và làm bài lab The Great Escape ta sẽ đề cập ở phần sau. 


## 1.  Basic Docker Syntax

Trước hết ta sẽ đi tìm hiểu 1 số lệnh thường dùng trong docker, ta sẽ chia thành 4 nhóm syntax chính: 
- syntax để chạy 1 container
- syntax để quản lý và kiểm tra 1 container
- syntax quản lý images
- syntax về thống kê và thông tin của daemon Docker.

### Managing Docker Images - với 1 số cmd là docker pull, docker image ls, docker image rm

**Docker pull** : trước khit ta chạy 1 container, ta sẽ cần 1 image trước, chẳng hạn ở đây ta sử dụng Nginx image để chạy 1 webserver trong 1 container. Trước khi download 1 image, ta sẽ phân tích lệnh và cú pháp cần thiết để download 1 image. Các image thì có thể download bằng cách sử dụng docker pull + name image. 

> docker pull nginx

Chạy lệnh trên ta sẽ được như sau: 

![image](https://user-images.githubusercontent.com/104350480/236678724-182598ec-02a9-402e-b6e6-342a794465e1.png)

Nó sẽ tự đông down bản mới nhất của nginx, được gọi là latest, nó là 1 tag mặc định trong 1 số biến thể ta có thể chỉ định của image, chẳng hạn là: docker pull ubuntu:latest, docker pull ubuntu:22.04, docker pull ubuntu:20.04....

Nếu kh phải là latest thì ta có thể chỉ định version của nó và tải xuống, và chúng được phân tách với nhau bởi dấu : nhé. 



**Docker Image x/y/z**: Tiếp theo là đến với 1 số cmd khác của docker image:

![image](https://user-images.githubusercontent.com/104350480/236678944-9072e19b-7b7a-4d19-a56a-198106a38c6c.png)

> Docker Image ls

Lệnh này sẽ nó sẽ liệt kê ra thông tin các image được lưu trữ trên máy chủ của ta, chẳng hạn: 

![image](https://user-images.githubusercontent.com/104350480/236679027-6aed45f9-130b-498a-beb3-25a9ebdc557f.png)

> Docker image rm: lệnh này chỉ đơn giản là dùng để xóa đi 1 image, có đi kèm với name image hoặc id cũng được. 


Tiếp đến ta sẽ chạy 1 container để cho dễ hiểu, nhắc lại thì docker run cmd tạo ra container từ các iamge có sẵn hoặc ta tự build, và đây là nơi các cmd trong Dockerfile được thực thi, cú pháp của nó như sau: 

> cmd: docker run [OPTIONS] IMAGE_NAME [COMMAND] [ARGUMENTS...] 

Chẳng hạn ở đây, tên image là helloworld, options ở đây là -it (interactive terminal) : cho phép tương tác với container trực tiếp qua cmd bằng cách mở 1 termial và liên kết terminal đó với quá trình thực thi container, ở đây môi trường dòng lệnh sẽ là /bin/bash

Ta có thể check container có chạy thành công hay công bằng cmd **docker ps -a** (process status):

![image](https://user-images.githubusercontent.com/104350480/236679793-54bb9062-a110-4bc2-bd92-e7acef53afa6.png)

Ta có thể nhận được các thông tin về các container thông qua lệnh trên.

Ngoài ra sẽ có 1 số option khác thường gặp như: 

```
 docker run -d alpine: (detached mode) lệnh này cho phép container chạy ở chế độ ngầm mà không cần phải kết nối trực tiếp với terminal.
 docker run -p 80:80 webserver: (port) được sử dụng để chuyển tiếp các yêu cầu từ máy chủ sang container. 
 docker run --rm helloworld: được sử dụng để khi container kết thúc và thoát ra nó sẽ tự động rm container này để giải phóng tài nguyên. 
 docker run --name hello alpine: nếu ta để ý ở ảnh ngay trên thì tên của container sẽ được tạo ngẫu nhiên, và ta hoàn toàn có thể chỉ định tên cụ thể cho nó. 

```

> Link docker run: https://docs.docker.com/engine/reference/run/

<hr> 


## Intro giới thiệu về Dockerfiles

Dockerfile đóng vai trò quan trọng trong docker, nó là một tệp văn bản được định dạng, nóchứa một loạt các chỉ thị (instruction) và lệnh để tạo một image Docker. Nó cho phép người dùng xây dựng các image tùy chỉnh dựa trên các image có sẵn hoặc từ đầu. 
Một số instruction phổ biến trong Dockerfile: 
> FROM <image-base>: instruction này được sử dụng để khai báo một bước xây dựng (build stage) mới và đặt hình ảnh cơ sở (base image) cho bước xây dựng đó.
 
> RUN <command>: được sử dụng để thực thi các lệnh trong container và tạo ra một lớp mới (new layer) trong image Docker

 ```
 RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    package3
 ```
 
> COPY <src> <dest>:  được sử dụng để sao chép các tệp tin từ hệ thống cục bộ vào thư mục làm việc (working directory) trong container Docker đang được tạo.

> WORKDIR <directory>: được sử dụng để thiết lập thư mục làm việc (working directory) cho container Docker đang được tạo. EX: WORKDIR /app , lưu ý là nên để phần dest tương ứng giữa COPY và WORKDIR để nó hoạt động. 
 
 > CMD: là một câu lệnh trong Dockerfile được sử dụng để xác định lệnh hoặc tiến trình được thực thi khi container Docker khởi động
 
 Ví dụ để khởi chạy chương trình ta có thể truyền các tham số với cách như sau: **CMD ["python", "app.py"]**
 
 Có 1 số lưu ý là đôi khi ta sẽ thấy phần code người ta sử dụng ENTRYPOINT thay vì CMD, về cơ bản chúng cùng chức năng nhưng có vài điểm khác nhau: 
 
 ```
CMD chỉ định lệnh hoặc tiến trình mặc định được thực thi khi container khởi động, có thể bị ghi đè bởi các tham số được truyền vào khi khởi động container. Trong khi đó, ENTRYPOINT xác định lệnh hoặc tiến trình chính được thực thi khi container khởi động, không thể bị ghi đè bởi các tham số.
Khi sử dụng CMD, ta có thể chỉ định nhiều câu lệnh hoặc tiến trình. Trong khi đó, khi sử dụng ENTRYPOINT, chỉ có một lệnh hoặc tiến trình được chỉ định.
Ngoài ra ta có thể sử dụng ENTRYPOINT để thực thi các lệnh bổ sung để cấu hình và tùy chỉnh lệnh chính, ví dụ:
 
ENTRYPOINT ["./entrypoint.sh"]
CMD ["python", "app.py"]
 
 ```
 
 > Tiếp đến là lệnh EXPOSE: được sử dụng để chỉ định cổng mà ứng dụng trong container sẽ lắng nghe các kết nối tới. Lưu ý là EXPOSE không thực sự mở các cổng này trên host, mà chỉ định rằng các cổng đó sẽ được sử dụng bởi container. Để mở các cổng này trên host, ta cần sử dụng tùy chọn -p khi chạy lệnh docker run.

 Ex: docker run -p 80:80 <image-name>
 
 
 #### Để build 1 docker image khi đã hoàn thành Dockerfile, ta có thể dùng cmd sau: docker build -t webserver .
 Và để chạy 1 Dockerfile: docker run -d --name webserver -p 80:80  webserver
 
 
 ### Tối ưu hóa Dockerfile
 
 Từ như này: 
 
 ```
 FROM ubuntu:latest
RUN apt-get update -y
RUN apt-get upgrade -y
RUN apt-get install apache2 -y
RUN apt-get install net-tools -y

 ```
 
 Như đã nói ở trên ta có thể dùng thành như này: 
 
 ```
 FROM ubuntu:latest
RUN apt-get update -y && apt-get upgrade -y && apt-get install apache2 -y && apt-get install net-tools
 
 ```
