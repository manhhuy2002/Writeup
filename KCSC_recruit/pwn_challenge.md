## PWN_CHANLLENGE

## Table of content web challenge:
* [Đề 1: Cat](#Đề-1-Cat)
* [Đề 2: Treasure](#Đề-2-treasure)


## Đề 1: Cat
```
Cat or cat?

nc 146.190.115.228 9994

```
Download: [Cat](https://github.com/manhhuy2002/hello-world/blob/main/cat)

Dùng IDA phân tích:

```
{
  int v4; // [rsp+Ch] [rbp-4h]
  int v5; // [rsp+Ch] [rbp-4h]

  init(argc, argv, envp);
  read_flag();
  printf("Username: ");
  v4 = read(0, username, 0x20uLL);
  if ( username[v4 - 1] == 10 )
    username[v4 - 1] = 0;
  printf("Password: ");
  v5 = read(0, password, 0x20uLL);
  if ( password[v5 - 1] == 10 )
    password[v5 - 1] = 0;
  if ( !strcmp(username, "KCSC_4dm1n1str4t0r") && !strcmp(password, passwd) )
  {
    puts("Logged in!");
    printf("Your secret: ");
    read(0, secret, 0x200uLL);
    printf("Saving secret \"%s\"...\n", secret);
    puts("Done! Exiting...");
  }
  else
  {
    puts("Unauthorized access is forbidden!");
  }
  return 0;
}
 ```
#### Phân tích:
Đoạn code trên là chương trình C thực hiện quy trình đăng nhập. Đầu vào được lưu trữ trong các biến "username" và "password" tương ứng. Chương trình sẽ kiểm tra xem ký tự cuối cùng của đầu vào có phải là ký tự xuống dòng (giá trị ASCII 10) hay không và nếu có, sẽ thay thế ký tự đó bằng ký tự null (ASCII 0) để xóa xuống dòng.
Sau đó, nó kiểm tra xem username và password có khớp với "KCSC_4dm1n1str4t0r" và biến "passwd" (passwd     ; "wh3r3_1s_th3_fl4g") hay không bằng cách sử dụng chức năng "stcmp". Nếu cả hai phép so sánh đều đúng, chương trình sẽ hiển thị thông báo "Logged in!" và yêu cầu nhập 1 secret. Còn kh thì sẽ báo "Unauthorized access is forbidden!"

#### Khai thác:
Đăng nhập thành công với username: KCSC_4dm1n1str4t0r, password: wh3r3_1s_th3_fl4g

```
puts("Logged in!");
    printf("Your secret: ");
    read(0, secret, 0x200uLL);
    printf("Saving secret \"%s\"...\n", secret);
    puts("Done! Exiting...");
    
```
Khai thác dòng thứ 3, 0x200uLL chỉ định giới hạn đầu vào là 512 bytes

#### Vậy làm tràn bộ đệm là xong: 
Dùng payload: python -c 'print("A"*1500)'

Rồi cp sang your secret: 
![image](https://github.com/manhhuy2002/hello-world/blob/main/cat_.jpg)

>flag: KCSC{w3ll_d0n3_y0u_g0t_my_s3cr3t_n0w_d04942f299}
 
 
## Đề 2: Treasure:
```
There are 3 parts of flag in the binary, try to obtain all parts and the real treasure will be reveal.

```
Download: [treasure](https://github.com/manhhuy2002/hello-world/blob/main/treasure)

Dùng IDA phân tích được: 

```
constructor:
  void __noreturn constructor()
  {
    init();
    puts("Part 1: KCSC{");
    exit(0);
   }
   Part 1: KCSC{
 
 main:
   void __noreturn constructor()
   {
    *(_QWORD *)&v4[15] = __readfsqword(0x28u);
    qmemcpy(v4, "4_t1ny_tr34sur3", 15);
    printf("Part 2: %s", v4);
    return 0;
    }
    Part 2: 4_t1ny_tr34sur3
    
 Và Part 3 lấy bên phần hex view: _27651d2df78e1998}
 
```

 ![treasure](https://github.com/manhhuy2002/hello-world/blob/main/treasure1.jpg)
 
 
 >flag: KCSC{4_t1ny_tr34sur3_27651d2df78e1998}
 







