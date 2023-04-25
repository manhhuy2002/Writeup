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
