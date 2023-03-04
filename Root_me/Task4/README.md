## Insecure deserialization

* [Lab 1: Modifying serialized objects](#Lab-1-Modifying-serialized-objects)
* [Lab 2: Modifying serialized data types](#lab-2-modifying-serialized-data-types)
* [Lab 3: Using application functionality to exploit insecure deserialization](#lab-3-using-application-functionality-to-exploit-insecure-deserialization)
* [Lab 4: Arbitrary object injection in PHP](#lab-4-arbitrary-object-injection-in-php)
* [Lab 5: Exploiting Java deserialization with Apache Commons](#lab-5-exploiting-java-deserialization-with-apache-commons)
* [Lab 6: Exploiting PHP deserialization with a pre-built gadget chain](#lab-6-exploiting-php-deserialization-with-a-pre-built-gadget-chain)
* [Lab 7: Exploiting Ruby deserialization using a documented gadget chain](# lab-7-exploiting-ruby-deserialization-using-a-documented-gadget-chain)

## Lab 1: Modifying serialized objects

Mô tả: 
```
```

Ta đăng nhập với tên wiener:peter

Check request burpsuite:

![image](https://user-images.githubusercontent.com/104350480/222875097-6c226f96-f43b-40d6-917e-8af2ebade0a6.png)

Có string admin với boolean = 0, sửa bằng 1 rồi gửi lại request:

![image](https://user-images.githubusercontent.com/104350480/222875132-be73d816-76b9-442b-889b-b70badb41738.png)

Truy cập /admin rồi xóa user carlos: /admin/delete?username=carlos là ta giải quyết xong.

## Lab 2: Modifying serialized data types

Mô tả: 

```
```

Cũng tương tự như bài trên, đăng nhập và check burpsuite: 

![image](https://user-images.githubusercontent.com/104350480/222875237-3a26cd97-eea7-4975-89be-0a3421e5e4f0.png)
 
 Ở đây nó check access_token, vậy nếu ta sửa tên thành administrator thì tất nhiên access_token cũng phải khác rồi, nhưng có thể áp dụng như bài 1, ta sửa
 giá trị access_token mặc định thành b:1, tức mặc định là true, gửi đi ta được:
 
 ![image](https://user-images.githubusercontent.com/104350480/222875428-836477e3-9dde-4f2d-834b-628ffd00c125.png)
 
 Tương tự như bài trên ta xong bài lab: 
 
 ![image](https://user-images.githubusercontent.com/104350480/222875455-997282c0-825a-4af0-b108-610aed84c13c.png)


## Lab 3: Using application functionality to exploit insecure deserialization

Mô tả: 

```

```

Tương tự ta đăng nhập rồi check burp:

![image](https://user-images.githubusercontent.com/104350480/222875513-2177ba70-66e4-4eaa-8c78-cc32acaa84be.png)



