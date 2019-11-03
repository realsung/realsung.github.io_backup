---
layout: post
title: White Hacker League Archon Writeup
author: "Realsung"
published: false
comments: true
featured: true
sitemap :
  changefreq : white archon
  priority : 1.0
---

.NET 바이너리가 주어졌다. 바이너리를 실행해보면 각 칸마다 넣고 Sign-in 해주고 맞으면 플래그를 출력해주는 형식이다.

```c#
for (int i = 0; i < 16; i++)
  {
    if (this.key.ToCharArray()[i] < ' ' || this.key.ToCharArray()[i] > '\u007f')
    {
      this.cnt++;
      break;
    }
    if (i == 6)
    {
      this.num1 = (int)this.key.ToCharArray()[i];
      this.num2 = (int)this.key.ToCharArray()[i + 10];
      this.num3 = (int)this.key.ToCharArray()[i + 11];
      this.temp1 = (this.num2 >> 2 ^ this.num3);
      this.temp2 = (this.num3 >> 2 ^ this.num1);
      this.temp3 = (this.num2 >> 2 ^ this.num1);
      if (this.num1 < this.temp1 || this.num2 < this.temp2 || this.num3 < this.temp3)
      {
        this.cnt++;
        break;
      }
    }
    else
    {
      this.num1 = (int)this.key.ToCharArray()[i];
      this.num2 = (int)(this.key.ToCharArray()[i + 1] >> 2 ^ this.key.ToCharArray()[i + 2]);
      if (this.num1 < this.num2)
      {
        this.cnt++;
        break;
      }
    }
    if (i % 2 == 0 && i < 14)
    {
      this.num1 = (int)this.key.ToCharArray()[i];
      this.num2 = (int)this.key.ToCharArray()[i + 2];
      this.num3 = (int)this.key.ToCharArray()[i + 4];
      this.num4 = (int)this.key.ToCharArray()[17 - i];
      switch (this.tmp)
      {
      case 0:
        if (this.num1 != (this.num2 ^ this.num3) + this.num4 || 94 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 2:
        if (this.num1 != (this.num2 ^ this.num3) - this.num4 + 5 || 32 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 4:
        if (this.num1 != ((this.num2 ^ this.num3) - this.num4) * 18 || 11 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 6:
        if (this.num1 != (this.num2 ^ this.num3) + this.num4 - 2 || 49 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 8:
        if (this.num1 != (this.num2 ^ this.num3 ^ this.num4) - 4 || 89 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 10:
        if (this.num1 != (this.num2 ^ this.num3 ^ this.num4) + 19 || 114 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 12:
        if (this.num1 != -((this.num2 ^ this.num3) & 2) + this.num4 || 69 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      }
      this.tmp++;
    }
    else if (i % 2 != 0 && i < 14)
    {
      this.num1 = (int)this.key.ToCharArray()[i];
      this.num2 = (int)this.key.ToCharArray()[i + 2];
      this.num3 = (int)this.key.ToCharArray()[i + 4];
      this.num4 = (int)this.key.ToCharArray()[17 - i];
      switch (this.tmp)
      {
      case 1:
        if (102 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 3:
        if (74 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 5:
        if (107 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 7:
        if (57 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 9:
        if (89 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 11:
        if (41 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      case 13:
        if (40 != (this.num1 ^ this.num2 ^ this.num3))
        {
          this.cnt++;
        }
        break;
      }
      this.tmp++;
    }
  }
```

플래그의 인덱스 6번째는 쉬프트 연산을 해주고 6 제외한 나머지 인덱스도 쉬프트와 xor연산을 해준다.

그리고 또 인덱스끼리 xor하는 연산이 있는데 그냥 무슨 값이 나오는지도 알 수 있어서 Solver를 이용해서 풀 수 있다.

```python
from z3 import *

s = Solver()
a1 = [BitVec('a%i'%i,8)for i in range(18)]

for i in range(16):
	s.add(a1[i] >= 32)
	s.add(a1[i] <= 127)

for i in range(16):
	if i == 6:
		s.add(a1[i] >= (a1[i+10] >> 2 ^ a1[i+11]))
		s.add(a1[i+10] >= (a1[i+11] >> 2 ^ a1[i]))
		s.add(a1[i+11] >= (a1[i+10] >> 2 ^ a1[i]))
	else:
		s.add(a1[i] >= a1[i+1] >> 2 ^ a1[i+2])

s.add(a1[0] == ((a1[2] ^ a1[4]) + a1[17]))
s.add(94 == (a1[0] ^ a1[2] ^ a1[4]))
s.add(a1[2] == ((a1[4] ^ a1[6]) - a1[15] + 5))
s.add(32 == (a1[2] ^ a1[4] ^ a1[6]))
s.add(a1[4] == (((a1[6] ^ a1[8]) - a1[13]) * 18))
s.add(11 == (a1[4] ^ a1[6] ^ a1[8]))
s.add(a1[6] == ((a1[8] ^ a1[10]) + a1[11] - 2))
s.add(49 == (a1[6] ^ a1[8] ^ a1[10]))
s.add(a1[8] == ((a1[10] ^ a1[12] ^ a1[9]) - 4))
s.add(89 == (a1[8] ^ a1[10] ^ a1[12]))
s.add(a1[10] == ((a1[12] ^ a1[14] ^ a1[7]) + 19))
s.add(114 == (a1[10] ^ a1[12] ^ a1[14]))
s.add(a1[12] == ((-((a1[14] ^ a1[16]) & 2)) + a1[5]))
s.add(69 == (a1[12] ^ a1[14] ^ a1[16]))
s.add(40 == (a1[13] ^ a1[15] ^ a1[17]))
s.add(102 == (a1[1] ^ a1[3] ^ a1[5]))
s.add(74 == (a1[3] ^ a1[5] ^ a1[7]))
s.add(107 == (a1[5] ^ a1[7] ^ a1[9]))
s.add(57 == (a1[7] ^ a1[9] ^ a1[11]))
s.add(89 == (a1[9] ^ a1[11] ^ a1[13]))
s.add(41 == a1[11] ^ a1[13] ^ a1[15])
print s.check()
m = s.model()
print ''.join(chr(int(str(m.evaluate(a1[i])))) for i in range(18))
```

![](https://user-images.githubusercontent.com/32904385/62299397-d5c14380-b4af-11e9-9e2b-dcd5af9ef394.png)

**FLAG : `CATSEC{d0_7ou_kN0VV_Me1Omanc3_??_goO0od_!!}`**