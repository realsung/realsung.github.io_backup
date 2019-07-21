---
layout: post
title: CTF Tips
author: "Realsung"
comments: true
featured: true
sitemap :
  changefreq : ctftips
  priority : 1.0
---

* file 명령어로 봤을 때 stripped 돼있고 main 함수가 안 보인다면 start로 가면 libc_start_main으로 콜하는 데 그 중에서 첫번째 인자로 들어가는 주소가 main함수이다.

* pwntools 쉘코드 생성

  ```python
  asm(shellcraft.amd64.sh(), arch='amd64')
  ```

* 64 Bit ROP

  ```markdown
  [buf] +  gadget [pop rdi; ret] + [/bin/sh string addr] + [system addr]
  ```

* 함수 오프셋

  ```python
  printf_off = e.symbols['printf']
  system_off = e.symbols['system']
  
  libc_base = printf - printf_off
  system = libc_base + system_off
  
  binsh = libc_base
  binsh += e.search('/bin/sh').next()
  ```



# Python Tips

XOR Tip

두 개의 문자 xor연산 할 때 itertools cycle 모듈 사용해서 인덱스가 끝나도 처음으로 가서 계속 xor 연산 가능

zip 함수는 두 개의 리스트의 같은 인덱스를 짝 지어준다.

Ex) [1,2] [3,4] 있으면 (1,3) (2,4) 이런식으로

```python
from itertools import cycle
a="Eo@GxVoclNqioF^tkF^clNqyoFe}"
b="\x03#\x01\x00"
flag=""
for x,y in zip(a,cycle(b)):
	flag += chr(ord(x)^ord(y))
print flag
```



# Web Tips

### POST

> Use python requests

```python
import requests

req = requests.post('주소', data={'name': value, cookies={'PHPSESSID': session})
```

> Use cURL

```shell
curl -d "id=admin&pw=admin&press=Login" 주소
```



### Header

> Use python requests

```python
import requests
head={'user-agent': 'Test'}
req = requests.get('주소', headers=head)
```

