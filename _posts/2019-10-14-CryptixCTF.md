---
layout: post
title: 2019 Cryptix CTF Writeup
author: "Realsung"
comments: true
featured: true
sitemap :
  changefreq : cryptix
  priority : 1.0
---

# The Spy

```
HackCrypt1337: Do you know, primes are great way to hide secrets!

n00b1001: Whatt..? primes? I don't believe you

HackCrypt1337: You are being too loud!, remember this number, 3073416132828889709313918053975078361304902307, it will be useful to understand the flag. and one more is......
Oh no! Someone is here, you can guess other one yourself. It is trivial anyways.
Here, keep this number safe with you!
1217323181436745647195685030986548015017805440

And they leave....

Get the flag!
```

n = 3073416132828889709313918053975078361304902307

c = 1217323181436745647195685030986548015017805440

이렇게 주어지는데 n으로 p,q 구하고 e는 그냥 자주 사용하는 65537 넣어서 풀었다.

**FLAG : `flag{w3ak_R5A_bad}`**

<br />

# Weird machine

이거 풀고 깨달은 게 있다. 나는 python2를 애용하는데 여기서는 python3로 Encrypt를 해놨다. 

python2와 python3의 random seed값을 똑같이 줘도 나오는 랜덤 값이 달랐다.. 이거 때문에 시간을 좀 낭비했다.

```python
import random
import string
from deep_memory import message

print("Encrpyt everything!!.... Oh no, system failure. Encrypting last message received")

rand = random.randint(1,10000)
alphanum = string.ascii_letters + string.digits

def random_string(rand_seed, message):
    random.seed(rand_seed)
    rand_string = ''
    for i in range(len(message)):
        rand_string += alphanum[random.randint(1,1000)%len(alphanum)]
    return rand_string

def encrpyt(random_str, message):
    encrpyted = ''
    for i in range(len(message)):
        k = ord(message[i])^ord(random_str[i])
        encrpyted += (bin(k)[2:]).zfill(8)
    return encrpyted

print(encrpyt(random_string(rand, message), message))
```

seed값 10000개중에 한개 넣어주고 거기서 랜덤 값 계속 가져온 거 이용해서 alphanum 인덱스를 가져와서 rand_string으로 만든 값과 message값을 xor한걸 2진수로 줬다.

seed값 10000개 BruteForce 해주면 된다..

```python
import string
import random
from binascii import *
alphanum=string.ascii_letters + string.digits

enc = 0b000010010101110100011000010100110011110101100010011000000001111100110101011000110101010100110100010010110101101001010101001101100110110000111100011000010001111000001011000011010000100000000001010101100011100000100101
enc=unhexlify(('0%x'%enc)).decode()
# table = map(lambda x: int(x, 2), [enc[i:i+8] for i in range(0, len(enc),8)])
# print(enc)

for i in range(0,10000):
	flag = ''
	random.seed(i)
	rand_string = ''
	for j in range(len(enc)):
		rand_string += alphanum[random.randint(1,1000)%len(alphanum)]
	for j in range(len(enc)):
		flag += chr(ord(rand_string[j])^ord(enc[j]))
	if 'flag' in flag:
		print(flag)
		break
```

python3로 돌리니까 풀린다.. ㅠ

**FLAG : `flag{R4nd0m_s33d_s4v3d_y0u}`**

<br/>

# Your ID please

```php
include_once 'flag.php';

if($_SERVER["REQUEST_METHOD"] == "POST"){
    if(isset($_POST["ID"])&&isset($_POST["pwd"])){
        if(strcmp($secretpassphrase, $_POST["pwd"]) == 0){
            echo "Hey, you are in!  " . $_POST["ID"]  . "<br>";
            if($_POST["ID"] == "SuperUser1337"){
                echo "Your Flag: " . $flag;
            }
        }else{
            echo "<script type='text/javascript'>alert('Unable to Login');</script>";
        }
    }
}
```

strcmp 취약점 터진다. 배열로 삽입해서 풀면 된다.

**FLAG : `flag{Why_Juggl3_th3_Typ5}`**

<br />

# Let's climb the ladder

```python
from z3 import *

s = Solver()
a1 = [BitVec('a%i'%i,32) for i in range(21)]

s.add(a1[7] == a1[16])
s.add(a1[1] == a1[11])
s.add(a1[2] == a1[8])
s.add(a1[8] == a1[18])
s.add(a1[3] == a1[17])
s.add(a1[5] == a1[20])
s.add(a1[9] == a1[10])
s.add(a1[12] == a1[19])
s.add(a1[0] == 114)
s.add(a1[2] - a1[1] == 1)
s.add(a1[10] - a1[8] == 1)
s.add(a1[2] == 52)
s.add(a1[3] + a1[0] == 214)
s.add(a1[4] - a1[3] == 5)
s.add(a1[5] + a1[6] == 213)
s.add(a1[5] - a1[6] == 7)
s.add(a1[7] - a1[8] == 43)
s.add(a1[12] + a1[13] == 207)
s.add(a1[12] * a1[13] == 10682)
s.add(a1[15] - a1[14] == 13)
s.add(a1[15] - a1[0] == 7)

print s.check()
m = s.model()
print m
print ''.join(chr(int(str(m.evaluate(a1[i])))) for i in range(len(a1)))
```

**FLAG : `flag{r34ding_4553bmly_d4bn}`**