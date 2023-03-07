# Csrf - Access control vulnerabilities - Authentication - Jwt - OAuth authentication
* [Csrf - cross-site request foregry](#csrf)
  - [1. what is csrf?](#csrf1)
  - [2. What is the impact of a CSRF attack?](#csrf2)

## I. Csrf - cross-site request foregry<a name='csrf'></a>

### 1. What is CSRF?<a name='csrf1'></a>
Csrf là viết tắt của cross-site request fogery là 1 lỗ hổng bảo mật web cho phép kẻ tấn công buộc hay khiến người dùng thực hiện các hành động mà họ không có ý định thực hiện.
Nó cho phép kẻ tấn công phá vỡ 1 phần origin policy mà được thiết kế để ngăn chặn các trang web khác nhau can thiệp vào nhau.

Minh họa:

![image](https://user-images.githubusercontent.com/104350480/223511276-ba7d71b5-ce59-41f2-9414-fa742f198bcb.png)


### 2. What is the impact of a CSRF attack?<a name='csrf2'></a>
