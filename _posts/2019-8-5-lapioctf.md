---
layout: post
title: Lapio CTF Reversing
author: "Realsung"
comments: true
featured: true
sitemap :
  changefreq : 1st Lapio ctf
  priority : 1.0
---

# messagebox

위에는 배열로 되어있는 변수에 어떠한 값들을 넣어준다.

![](https://user-images.githubusercontent.com/32904385/62457106-ff7eb100-b7b4-11e9-9a15-46c9f7331238.png)

우선 바이너리 패치로 JNZ 하는걸 JE로 바꿔서 끝나지 않도록하고 밑으로 내리다보면 "I'm Han"과 "I'm Ayoung"을 strcmp로 비교하는 곳도 있다.

![](https://user-images.githubusercontent.com/32904385/62457109-01487480-b7b5-11e9-96d6-05e88d6130a3.png)

여기서 그냥 계속 넘기고 밑에 보니까 플래그를 만들어주는 것 같은 loop를 발견했다.

![](https://user-images.githubusercontent.com/32904385/62457108-00afde00-b7b5-11e9-91c0-72f49160a12e.png)

이쪽부분을 쓱삭 가져오니까 아래와 같은 값들이 나왔다.

![](https://user-images.githubusercontent.com/32904385/62457105-fee61a80-b7b4-11e9-82ad-d94539d8c88b.png)

메세지박스를 보니 4로 평문을 가져올 수 있다는 것 같다. 그래서 이 값들과 4와 xor연산을 해줬다.

```python
table=[0x40, 0x4d, 0x57, 0x47, 0x7f, 0x35, 0x24, 0x6c, 0x44, 0x72, 0x61, 0x24, 0x65, 0x24, 0x67, 0x6c, 0x34, 0x67, 0x6b, 0x68, 0x44, 0x70, 0x37, 0x79]
print ''.join(chr(i^4) for i in table)
```

**FLAG : `DISC{1 h@ve a ch0col@t3}`**

<br />

# serial

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int v3; // eax
  int result; // eax
  __int16 v5; // [esp+1Eh] [ebp-22h]
  int v6; // [esp+28h] [ebp-18h]
  __int16 v7; // [esp+32h] [ebp-Eh]
  int v8; // [esp+3Ch] [ebp-4h]

  __main();
  printf("name ? ");
  scanf("%s", &v7);
  v8 = change_name(&v6, &v7);
  printf("key ? ");
  scanf("%s", &v5);
  v3 = check_key(&v6, &v5, v8);
  if ( v3 == v8 )
    result = print();
  else
    result = printf("that's wrong");
  return result;
}
```

바이너를 열어보면 뭐 이름 입력받고 `change_name ` 함수 호출하고 key 값도 입력받아서 `check_key` 에 인자 넣어서 비교해주는데 그냥 두개 비교해서 맞았을 때 `print` 함수를 리턴해준다.

```c
int print()
{
  v1 = 99;
  v2 = 105;
  v3 = 100;
  v4 = 98;
  v5 = 37;
  v6 = 63;
  v7 = 37;
  v8 = 65;
  v9 = 76;
  v10 = 86;
  v11 = 70;
  v12 = 126;
  v13 = 99;
  v14 = 105;
  v15 = 69;
  v16 = 98;
  v17 = 90;
  v18 = 52;
  v19 = 118;
  v20 = 90;
  v21 = 119;
  v22 = 54;
  v23 = 69;
  v24 = 115;
  v25 = 96;
  v26 = 119;
  v27 = 118;
  v28 = 52;
  v29 = 107;
  v30 = 98;
  v31 = 120;
  for ( i = 0; i <= 30; ++i )
    result = putchar((char)(*(&v1 + i) ^ 5));
  return result;
}
```

메인에서 `print()` 함수를 보면 플래그 값을 출력해주는 걸 알 수 있다.

```python
tb = "cidb%?%ALVF~ciEbZ4vZw6Es`wv4kbx"
print ''.join(chr(ord(tb[i])^5) for i in range(len(tb)))
```

**FLAG : `DISC{fl@g_1s_r3@vers1ng}`**