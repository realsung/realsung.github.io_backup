---
layout: post
title: Duelist Crackme Writeup
author: "Realsung"
comments: true
featured: true
published: false
sitemap :
  changefreq : Duelist Crackme
  priority : 1.0
---

# Crackme #1

이런 코드 하나를 입력 받는 바이너리 하나가 주어진다. 

![](https://user-images.githubusercontent.com/32904385/62149533-e1d5c580-b336-11e9-817b-f34a7f252afa.png)

Check!를 누르면 비교 값이 맞으면 맞다 아니면 아니다를 출력해주는 바이너리다. 

너무 친절하게 아무것도 입력하지 않으면 아무것도 안 입력했다고 해준다..

![](https://user-images.githubusercontent.com/32904385/62149444-b18e2700-b336-11e9-8bac-a3ab159b5397.png)

위에는 조금 생략했는데 입력 받은 값은 `004020f7` 에 저장되고 버퍼 크기는 36이다. 

입력 받은 값과 `004020f7` 값을  xor한게 같으면 된다.

![](https://user-images.githubusercontent.com/32904385/62149445-b226bd80-b336-11e9-9cc3-e98b250fd0bb.png)

그리고 밑에 보면 stack에 값을 넣고 있는데 이 주소를 보니 테이블이 있었다.

![](https://user-images.githubusercontent.com/32904385/62149671-3d07b800-b337-11e9-8a8a-e279580c42b1.png)

```python
table = '7B 61 65 78 64 6D 26 6B 7A 69 6B 63 65 6D 26 3C 26 66 6D 7F 6A 61 6D 7B 26 6A 71 26 6C 7D 6D 64 61 7B 7C'
table = table.split(' ')
print ''.join(chr(int(i,16)^0x43^0x1e^0x55) for i in table)
```

그냥 테이블 가져와서 역연산해주면 된다.

![](https://user-images.githubusercontent.com/32904385/62149439-af2bcd00-b336-11e9-8ae8-c97c087c02f0.png)

**FLAG : `simple.crackme.4.newbies.by.duelist`**

<br />

# Crackme #2

Crackme #2 파일을 실행하게 되면 무슨 알림창이 뜬다. 

keyfile을 이 파일과 같은 디렉토리에 놓으라는 소리 같다.

![](https://user-images.githubusercontent.com/32904385/62151666-d0db8300-b33b-11e9-8227-5324d051b34c.png)

