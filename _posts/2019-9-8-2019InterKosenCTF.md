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

이런식으로 주어져있는데 argv로 넘긴 값을 연산해 맞는 글자인지 아닌지 판단해준다.

```c
table = {180,247,57,89,234,57,75,107,191,128,61,209,66,16,228,66,261,88,21,264,171
  ,24,232,205,27,235,81,30,273,68,81,134,83,72,89,54,266,155,253}
v4 = 0;
for ( i = 0; i < strlen(argv[1]); ++i )
	v4 |= i + ((argv[1][i] >> 4) | (argv[1][i] << 4)) - table[i]
if ( v4 )
	printf("Try harder!", argv);
else
	puts("Yes. This is the your flag :)");
```

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

## magic function

f1 f2 f3 함수에서 무슨 math 함수 이용해서 무슨 연산들을 해준다. 

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char v3; // bl
  char v4; // bl
  char v5; // bl
  int result; // eax
  const char *v7; // [rsp+10h] [rbp-20h]
  signed int v8; // [rsp+1Ch] [rbp-14h]

  if ( argc <= 1 )
    goto LABEL_14;
  v8 = 0;
  v7 = argv[1];
  while ( *v7 )
  {
    if ( v8 > 7 )
    {
      if ( v8 > 15 )
      {
        v5 = *v7;
        if ( v5 != f3(v8 - 16) )
          goto LABEL_14;
      }
      else
      {
        v4 = *v7;
        if ( v4 != f2(v8 - 8) )
          goto LABEL_14;
      }
    }
    else
    {
      v3 = *v7;
      if ( v3 != f1(v8) )
        goto LABEL_14;
    }
    ++v7;
    ++v8;
  }
  if ( v8 != 24 )
  {
LABEL_14:
    puts("NG");
    result = 1;
  }
  else
  {
    puts("OK");
    result = 0;
  }
  return result;
}
```

마지막에 그냥 goto LABEL_14; 로 안가게 해주면 된다. 어셈으로 보게 되면 함수 호출 다음 줄에 cmp가 있는데 여기에서 bl,al를 비교하는데 bl은 우리가 입력한 값이고 al는 여기서 어떠한 연산을 한 값이다. 

그래서 그냥 al에 저장된 값을 계속 가져오고 bl는 set으로 al과 똑같이 맞춰주면서 24번 반복하면 된다.

```python
import gdb

gdb.execute('file chall')
print(gdb.execute('disas main',to_string=True))

gdb.execute('b * 0x000000000040080b') # main+70  cmp    bl,al
gdb.execute('b * 0x000000000040082b') # main+102 cmp    bl,al
gdb.execute('b * 0x0000000000400845') # main+128 cmp    bl,al

pay = 'A'*24
gdb.execute('r ' + pay,to_string=True)

flag = ''

for i in range(24):
	al = gdb.execute('p $al',to_string=True).split(' ')[2]
	flag += chr(int(al,16))
	print(flag)
	gdb.execute('set $bl =' + al,to_string=True)
	gdb.execute('c',to_string=True)

gdb.execute('q',to_string=True)
```

![](https://user-images.githubusercontent.com/32904385/64585999-c0f09d80-d3d5-11e9-98fb-9267edf38d64.png)

**FLAG : `KosenCTF{fl4ggy_p0lyn0m}`**

<br />