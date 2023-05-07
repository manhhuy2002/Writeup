## Basic Docker

* [Introduction to Docker](#dk1)
  - [1.1. What is Docker?](#dk11)
  - [1.2. What are Docker "containers" & why are they used?](#dk12)
  - [1.3. What are Docker Images?](#dk13)
* [Basic Docker Syntax And Run Container](#dk2)
* [Intro to Dockerfiles](#dk3)
* [Intro to Docker Compose](#dk4)
* [Intro to the Docker Socket](#dk5)


## Introduction to Docker<a name="dk1"></a>

### 1.1. What is Docker?<a name="dk11"></a>
Docker được giới thiệu năm 2013 nhằm giải quyết quá trình phát trình ứng dụng và vấn đề cung cấp dịch vụ tốn kém cũng như tốn thời gian.
Docker sử dụng công nghệ containerization để giải quyết vấn đề này. Kĩ thuật công nghệ này cho phép ứng dụng được đóng gói trong các container riêng biệt, trong đó chúng có thể chia sẻ tài nguyên với nhau nhưng hoạt động độc lập với nhau. Việc sử dụng containerization giúp tạo ra các container nhẹ và di động, giúp các nhà phát triển và quản trị viên hệ thống có thể dễ dàng xây dựng, triển khai và quản lý các ứng dụng một cách hiệu quả hơn.

Cụ thể hơn thì: 

```
Docker là một công nghệ rất di động và có thể đưa container của nó chạy trên bất kỳ máy tính nào có hỗ trợ Docker. Điều này giúp cho việc phát triển và triển khai ứng dụng trở nên dễ dàng hơn, vì chỉ cần viết ứng dụng một lần và triển khai nó trên nhiều thiết bị khác nhau.

Một điểm đáng chú ý khác của Docker là sử dụng tài nguyên ít hơn so với các máy ảo. Điều này là do Docker sử dụng cùng một hạt nhân Linux cho tất cả các container, do đó, mỗi container chỉ cần sử dụng một phần của tài nguyên hệ thống. Điều này giúp tiết kiệm tài nguyên và cho phép chạy nhiều container trên cùng một hệ thống một cách hiệu quả hơn so với việc sử dụng các VMs.

Docker cũng cho phép ta thiết lập môi trường phức tạp trong vài bước đơn giản thông qua các Dockerfiles. Dockerfile là một tập tin dạng văn bản chứa các instruction để xây dựng một image Docker, giúp cho việc triển khai ứng dụng trở nên dễ dàng và có thể tự động hóa.

Với sự phổ biến của containerization trong ngành công nghệ thông tin ngày nay, Docker là một công cụ rất quan trọng cho pentester để thực hiện các kiểm tra bảo mật và phát hiện lỗ hổng trong các ứng dụng chạy trên các container Docker.

Hoặc tóm cái váy lại thì docker nó sẽ có những lọi ích siêu ngắn gọn sau: Free,Compatible, Efficient & Minimal, Easy to Get Started With, Easy to Share With Others, Better security, Cheaper to Run
```



### 1.2. What are Docker "containers" & why are they used?<a name="dk12"></a>

Như đã đề cập ở trên thì docker, các container có thể chia sẻ tài nguyên cho nhau nhưng chúng hoạt động 1 cách độc lập, tránh được sự xung đột lẫn nhau thông qua Docker engine. Sơ đồ miêu tả như sau: 

![image](https://user-images.githubusercontent.com/104350480/236696108-5faa77c9-4bc5-447a-aa82-e5021ad56300.png)

Ta có thể thấy ba container chạy các ứng dụng của riêng chúng mà không có virtualization. Ba ứng dụng được phân lập với nhau, nhưng sử dụng tài nguyên của hệ điều hành chính. Trong khi đó, so với việc chạy các ứng dụng này trong các máy ảo:

![image](https://user-images.githubusercontent.com/104350480/236696183-c9468e4a-0125-4d6b-be78-6448704c6889.png)

Cái "Guest operating system" ở đây là nơi sử dụng tài nguyên. Ví dụ: kích thước cài đặt tối thiểu được đề xuất của Ubuntu là 20GB, nếu ta chạy điều này cho ba ứng dụng, ta sẽ yêu cầu lưu trữ 60GB. Trong khi đó, một image Docker Ubuntu có kích thước cơ sở khoảng 180MB ~. Container cũng có thể chia sẻ base images, giúp tối ưu  hiệu quả không gian.

###  1.3. What are Docker Images? <a name="dk13"></a>

Docker Container được tạo ra từ Docker Image. Docker Image là một gói đóng gói chứa toàn bộ các thành phần cần thiết để tạo ra một Docker Container, bao gồm các thư viện, tệp cấu hình, mã nguồn và các công cụ liên quan.
Trong Docker Image, các tập tin được định nghĩa bằng các lệnh Dockerfile, bao gồm các lệnh RUN, COPY, ADD, CMD, ENTRYPOINT và nhiều lệnh khác. Các lệnh này được sử dụng để định nghĩa cách cài đặt và cấu hình môi trường cho ứng dụng trong Docker Container, ta sẽ nói rõ hơn ở phần sau. 

<hr>


## Basic Docker Syntax And Run Container<a name="dk2"></name>
Phần này ta sẽ tìm hiểu về:
- các syntax cơ bản trong docker
- Chạy và triển khai container
- Hiểu cách phân phối các container Docker sử dụng các images
- Tạo image của riêng mình bằng Dockerfile 
- Cách Dockerfile build các container và sử dụng docker compose để kết hợp nhiều container. 
- Sau đó áp dụng kiến thức và làm bài lab The Great Escape ta sẽ đề cập ở phần sau. 


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


## Intro giới thiệu về Dockerfiles<a name="dk3"></a>

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
