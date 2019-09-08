---
layout: post
title: 2019 InterKosen CTF
author: "Realsung"
comments: true
featured: true
sitemap :
  changefreq : 2019 interkosenctf
  priority : 1.0
---

# Reversing

## basic crackme

```c
table = {180,247,57,89,234,57,75,107,191,128,61,209,66,16,228,66,261,88,21,264,171,24,232,205,27,235,81,30,273,68,81,134,83,72,89,54,266,155,253}
v4 = 0;
for ( i = 0; i < strlen(argv[1]); ++i )
	v4 |= i + ((argv[1][i] >> 4) | (argv[1][i] << 4)) - table[i]
if ( v4 )
	printf("Try harder!", argv);
else
	puts("Yes. This is the your flag :)");
```

이런식으로 주어져있는데 argv로 넘긴 값을 연산해 false면 된다. 

아래처럼 역연산하는 방법이 있다.

```python
table = [180,247,57,89,234,57,75,107,191,128,61,209,66,16,228,66,261,88,21,264,171,24,232,205,27,235,81,30,273,68,81,134,83,72,89,54,266,155,253]

print ''.join(chr(((j - i << 4) | (j - i >> 4)) & 0xff) for i,j in enumerate(table))
```

아니면 한글자만 입력해도 그에 따른 인덱스의 글자가 맞는지 비교해준다. strlen(argv[1]) 만큼 반복하기 때문이다.

아래처럼 브루트포스 공격을 해서 풀 수 있다. 

```python
from pwn import *

payload = ""

for i in range(39):
	for j in range(33,127):
		argvs = payload + chr(j)
		p = process(['./crackme',argvs])
		print '[*]' + argvs
		recv = p.recv(10)
		print recv
		if 'Yes.' in recv:
			payload = argvs
			p.close()
			break
		else:
			p.close()
```

![](https://user-images.githubusercontent.com/32904385/64486420-ee1f3d80-d267-11e9-9260-429e44edb318.png)

**FLAG : `KosenCTF{w3lc0m3_t0_y0-k0-s0_r3v3rs1ng}`**

<br />