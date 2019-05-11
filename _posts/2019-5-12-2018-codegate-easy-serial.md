---
layout: post
title: 2018 Codegate Prequal easy serial
author: "Realsung"
comments: true
featured: true
---

```
$ file easy
easy: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=4bd1814b358aaa9e61a968f20bd14ffa9cd208e8, not stripped
```

우선 까보면 c로 만들어진 바이너리가 아닌 것 같았다. 하스켈로 컴파일된 바이너리였다. 아래 하스켈 디컴파일러를 이용해서 디컴파일 하였다.

[하스켈 디컴파일러](https://github.com/gereeter/hsdecomp)



```haskell
            >> $fMonadIO
                (putStrLn (++ (unpackCString# "your serial key >>> ")
                (++ s1b7_info (++ (unpackCString# "_") 
                (++ s1b9_info (++ (unpackCString# "_") s1bb_info))))))
                (case 
                && (== $fEqInt (ord (!! s1b7_info loc_7172456)) (I# 70)) 
                (&& (== $fEqInt (ord (!! s1b7_info loc_7172472)) (I# 108)) 
                (&& (== $fEqInt (ord (!! s1b7_info loc_7172488)) (I# 97)) 
                (&& (== $fEqInt (ord (!! s1b7_info loc_7172504)) (I# 103)) 
                (&& (== $fEqInt (ord (!! s1b7_info loc_7172520)) (I# 123)) 
                (&& (== $fEqInt (ord (!! s1b7_info loc_7172536)) (I# 83)) 
                (&& (== $fEqInt (ord (!! s1b7_info loc_7172552)) (I# 48)) 
                (&& (== $fEqInt (ord (!! s1b7_info loc_7172568)) (I# 109)) 
                (&& (== $fEqInt (ord (!! s1b7_info loc_7172584)) (I# 101)) 
                (&& (== $fEqInt (ord (!! s1b7_info loc_7172600)) (I# 48)) 
                (&& (== $fEqInt (ord (!! s1b7_info (I# 10))) (I# 102)) 
                (&& (== $fEqInt (ord (!! s1b7_info (I# 11))) (I# 85)) 
                (== $fEqInt (ord (!! s1b7_info (I# 12))) (I# 53))))))))))))) of
                    <tag 1> -> putStrLn (unpackCString# ":p"),
                    c1ni_info_case_tag_DEFAULT_arg_0@_DEFAULT -> case == ($fEq[] $fEqChar) (reverse s1b9_info)
                    (: (C# 103) 
                    (: (C# 110)
                    (: (C# 105)
                    (: (C# 107) 
                    (: loc_7168872 
                    (: loc_7168872 
                    (: (C# 76) 
                    (: (C# 51) 
                    (: (C# 114) 
                    (: (C# 52) [])))))))))) of
                        False -> putStrLn (unpackCString# ":p"),
                        True -> case && 
                        (== $fEqChar (!! s1bb_info loc_7172456) (!! s1b3_info loc_7172456)) 
                        && (== $fEqChar (!! s1bb_info loc_7172472) (!! s1b4_info (I# 19))) 
                        (&& (== $fEqChar (!! s1bb_info loc_7172488) (!! s1b3_info (I# 19))) 
                        (&& (== $fEqChar (!! s1bb_info loc_7172504) (!! s1b4_info loc_7172568)) 
                        (&& (== $fEqChar (!! s1bb_info loc_7172520) (!! s1b2_info loc_7172488)) 
                        (&& (== $fEqChar (!! s1bb_info loc_7172536) (!! s1b3_info (I# 18))) 
                        (&& (== $fEqChar (!! s1bb_info loc_7172552) (!! s1b4_info (I# 19))) 
                        (&& (== $fEqChar (!! s1bb_info loc_7172568) (!! s1b2_info loc_7172504)) 
                        (&& (== $fEqChar (!! s1bb_info loc_7172584) (!! s1b4_info (I# 17))) 
                        (== $fEqChar (!! s1bb_info loc_7172600) (!! s1b4_info (I# 18))))))))))) of
                            <tag 1> -> putStrLn (unpackCString# ":p"),
                            c1tb_info_case_tag_DEFAULT_arg_0@_DEFAULT -> putStrLn (unpackCString# "Correct Serial Key! Auth Flag!")
                )
        )
    )
s1b4_info = unpackCString# "abcdefghijklmnopqrstuvwxyz"
loc_7172600 = I# 9
s1bb_info = !! s1b5_info loc_7172488
loc_7172488 = I# 2
s1b5_info = splitOn $fEqChar (unpackCString# "#") s1dZ_info_arg_0
loc_7172584 = I# 8
loc_7172504 = I# 3
s1b2_info = unpackCString# "1234567890"
loc_7172568 = I# 7
loc_7172552 = I# 6
s1b3_info = unpackCString# "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
loc_7172536 = I# 5
loc_7172520 = I# 4
loc_7172472 = I# 1
loc_7172456 = I# 0
loc_7168872 = C# 48
s1b9_info = !! s1b5_info loc_7172472
s1b7_info = !! s1b5_info loc_7172456
```

더 복잡했는데 최대한 줄 바꿈해서 보기 쉽게 나열하였다.



<br>

**solve.py**

a2를 리버스한건 s1b9_info를 reverse한다고 써있었다. 그리고 reverse를 안하면 말이 안되게 나온다.

그리고 이 배열들을 다 붙여주면 된다.

그리고 split해줬으니까 사이사이 `_` 를 넣어주면 될 것이다.

`}` 는 왜 빠졌는지 잘 모르겠다.

```python
s1b2_info="1234567890"
s1b3_info="ABCDEFGHIJKLMNOPQRSTUVWXYZ"
s1b4_info="abcdefghijklmnopqrstuvwxyz"

a1=[70,108,97,103,123,83,48,109,101,48,102,85,53]
a2=[103,110,105,107,48,76,51,114,52]
a2=a2[::-1]
a3=[s1b3_info[0],s1b4_info[19],s1b3_info[19],s1b4_info[7],s1b2_info[2],s1b3_info[18],s1b4_info[19],s1b2_info[3],s1b4_info[17],s1b4_info[18]]

flag=''
for i in a1:
	flag+=chr(i)
for i in a2:
	flag+=chr(i)
for i in a3:
	flag+=i
print flag + '}'
```

**FLAG : `Flag{S0me0fU5_4r3L0king_AtTh3St4rs}`**