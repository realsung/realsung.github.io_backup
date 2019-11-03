---
layout: post
title: 2016 Nuit du Hack CTF Quals Matriochka
author: "Realsung"
comments: true
published: false
featured: true
sitemap :
  changefreq : nuitduhackctf
  priority : 1.0
---

## Stage1

첫번째 스테이지에서 argv[1]의 값이 `Much_secure__So_safe__Wow` 이어야 한다.

![](https://user-images.githubusercontent.com/32904385/57570849-a0bdcb80-7441-11e9-8e58-6cd7ad30f0db.png)

그리고 그 밑에서 뭔가 값을 계속 만들어준다. 그것을 b64decode하니까 다음 스테이지로 갈 수 있엇다.

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  __int64 v4; // rdx
  char buf; // [rsp+17h] [rbp-9h]
  int v6; // [rsp+18h] [rbp-8h]
  int v7; // [rsp+1Ch] [rbp-4h]

  if ( argc != 2 )
    return fprintf(stdout, "Usage: %s <pass>\n", *argv, argv);
  if ( !strcmp(argv[1], "Much_secure__So_safe__Wow") )
  {
    fwrite("Good good!\n", 1uLL, 0xBuLL, stdout);
    v6 = 0;
    v7 = 0;
    while ( v6 <= 55334 )
    {
      v4 = 0x47AE147AE147AE15LL * v7 >> 64;
      buf = next_step[v6] ^ argv[1][v7 - 25 * ((v4 + ((v7 - v4) >> 1)) >> 4)];
      write(2, &buf, 1uLL);
      ++v6;
      ++v7;
    }
  }
  else
  {
    fwrite("Try again...\n", 1uLL, 0xDuLL, stdout);
  }
  return 1;
}
```

**FLAG : `Much_secure__So_safe__Wow`**

## Stage2

그냥 z3 쓰거나 일일이 계산해도 될 거 같다. v5값을 계속 1로 유지시키고 sub_40064D까지 가면 여기서 다음 스테이지 코드들을 주고 그걸 또 b64decode하면 된다.

```c
int __fastcall main(int a1, char **a2, char **a3)
{
  __int64 v4; // rbx
  signed int v5; // [rsp+1Ch] [rbp-14h]

  if ( a1 != 2 )
    return fprintf(stdout, "Usage: %s <pass>\n", *a2, a2);
  if ( 42 * (strlen(a2[1]) + 1) == 504 )
  {
    v5 = 1;
    if ( *a2[1] != 80 )
      v5 = 0;
    if ( 2 * a2[1][3] != 200 )
      v5 = 0;
    if ( *a2[1] + 16 != a2[1][6] - 16 )
      v5 = 0;
    v4 = a2[1][5];
    if ( v4 != 9 * strlen(a2[1]) - 4 )
      v5 = 0;
    if ( a2[1][1] != a2[1][7] )
      v5 = 0;
    if ( a2[1][1] != a2[1][10] )
      v5 = 0;
    if ( a2[1][1] - 17 != *a2[1] )
      v5 = 0;
    if ( a2[1][3] != a2[1][9] )
      v5 = 0;
    if ( a2[1][4] != 105 )
      v5 = 0;
    if ( a2[1][2] - a2[1][1] != 13 )
      v5 = 0;
    if ( a2[1][8] - a2[1][7] != 13 )
      v5 = 0;
    if ( v5 )
      return sub_40064D(a2[1]);
  }
  return fprintf(stdout, "Try again...\n", a2);
}
```

<br>



**solve.py**

```python
from z3 import *

a1=[Int('a%i'%i)for i in range(11)]
s=Solver()
s.add(a1[0]==80)
s.add(a1[3]==100)
s.add(a1[0]+16==a1[6]-16)
s.add(a1[5]==9*len(a1)-4)
s.add(a1[1]==a1[7])
s.add(a1[1]==a1[10])
s.add(a1[1]-17==a1[0])
s.add(a1[3]==a1[9])
s.add(a1[4]==105)
s.add(a1[2]-a1[1]==13)
s.add(a1[8]-a1[7]==13)
print s.check()
flag=[]
tmp = s.model()
for i in range(len(tmp)):
	flag.append(int(str((tmp.evaluate(a1[i])))))
answer=""
for i in flag:
	answer+=chr(i)
print answer
```

**FLAG : `Pandi_panda`**



## Stage3

사실 너무 간단하다.

```c
int __fastcall main(int a1, char **a2, char **a3)
{
  signed int i; // [rsp+18h] [rbp-8h]
  __pid_t pid; // [rsp+1Ch] [rbp-4h]

  if ( a1 != 2 )
    return printf("Usage: %s <pass>\n", *a2, a3, a2);
  strncpy(&dest, a2[1], 0x3FFuLL);
  pid = getpid();
  signal(11, sub_4007FD);
  signal(8, sub_401050);
  for ( i = 0; i <= 1023; ++i )
    kill(pid, 11);
  puts("Try again!");
  return 1;
}
```

시그널 11번을 제어하면서 계속 함수를 호출해준다. 그 함수를 보면 그냥 값들 긁어왔다.

<br>

**sovle.py**

```python
table=[68,105,100,95,121,111,117,95,108,105,107,101,95,115,105,103,110,97,108,115,63,]
flag=''
for i in table:
	flag+=chr(i)
print flag
```

**FLAG : `Did_you_like_signals?`**