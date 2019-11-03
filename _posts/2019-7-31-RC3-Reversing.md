---
layout: post
title: 2016 RC3 CTF Reversing Writeup
author: "Realsung"
published: false
comments: true
featured: true
sitemap :
  changefreq : rc3 reversing
  priority : 1.0
---

# Copy of logmein

logmein은 독일어로 로그인이란다

우선 메인을 보면 그냥 입력 받고 막 연산을 해서 분기를 다 헤쳐 나가면 `sub_4007F0();` 에서 Correct를 출력해준다.

```c
void __fastcall __noreturn main(__int64 a1, char **a2, char **a3)
{
  size_t v3; // rsi
  int i; // [rsp+3Ch] [rbp-54h]
  char s[36]; // [rsp+40h] [rbp-50h]
  int v6; // [rsp+64h] [rbp-2Ch]
  __int64 v7; // [rsp+68h] [rbp-28h]
  char v8[8]; // [rsp+70h] [rbp-20h]
  int v9; // [rsp+8Ch] [rbp-4h]

  v9 = 0;
  strcpy(v8, ":\"AL_RT^L*.?+6/46");
  v7 = 28537194573619560LL;
  v6 = 7;
  printf("Welcome to the RC3 secure password guesser.\n", a2, a3);
  printf("To continue, you must enter the correct password.\n");
  printf("Enter your guess: ");
  __isoc99_scanf("%32s", s);
  v3 = strlen(s);
  if ( v3 < strlen(v8) )
    sub_4007C0(v8);
  for ( i = 0; i < strlen(s); ++i )
  {
    if ( i >= strlen(v8) )
      ((void (*)(void))sub_4007C0)();
    if ( s[i] != (char)(*((_BYTE *)&v7 + i % v6) ^ v8[i]) )
      ((void (*)(void))sub_4007C0)();
  }
  sub_4007F0();
}
```

입력받은 값과 `*v7[i%7] ^ v8[i]` 값과 같게 하면 된다. Easy

```python
v8 = ':\"AL_RT^L*.?+6/46'
v7 = 'ebmarah'
print ''.join(chr(ord(v7[::-1][i%7]) ^ ord(v8[i])) for i in range(len(v8)))
```

**FLAG : `RC3-2016-XORISGUD`**



# FLE

9.3kb의 작은 크기의 64bit 바이너리가 주어졌다.

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char *dest; // [rsp+28h] [rbp-8h]

  dest = (char *)malloc(0x561uLL);
  strcpy(dest, argv[1]);
  enc(dest);
  if ( !memcmp(flag, dest, 0x23uLL) )
    puts("gottem");
  free(dest);
  return 0;
}
```

정말 간단하게 생겼다. argv[1]으로 입력받은 값을 dest로 넣고 enc함수에 인자로 넣고 연산을 한 후 flag table과 메모리 값의 내용이 같은지 비교해 같으면 gottem을 출력해준다. 그 이후 동적할당 해제한다.

```c
unsigned __int64 __fastcall enc(const char *a1)
{
  char v1; // r13
  int i; // [rsp+10h] [rbp-50h]
  int v4; // [rsp+14h] [rbp-4Ch]
  char v5; // [rsp+30h] [rbp-30h]
  char v6; // [rsp+31h] [rbp-2Fh]
  char v7; // [rsp+32h] [rbp-2Eh]
  char v8; // [rsp+33h] [rbp-2Dh]
  char v9; // [rsp+34h] [rbp-2Ch]
  unsigned __int64 v10; // [rsp+38h] [rbp-28h]

  v10 = __readfsqword(0x28u);
  v5 = 100;
  v6 = 97;
  v7 = 100;
  v8 = 63;
  v9 = 0;
  v4 = strlen(a1);
  for ( i = 0; i < v4; ++i )
  {
    v1 = a1[i];
    a1[i] = v1 ^ *(&v5 + i % strlen(&v5));
  }
  return __readfsqword(0x28u) ^ v10;
}
```

enc함수를 보면 *v5[i%4]와 xor연산을 해주고 있다. 

flag 메모리에 저장된 값과 *v5[i%4]를 xor연산해주면 passcode가 나올 것이다.

```python
table = [0x64,0x61,0x64,0x3f]
unk_400918 = [0x36,0x22,0x57,0x12,0x2a,0x2e,0x30,0x12,0x30,0x29,0x21,0x12,0x22,
0x2d,0x25,0x78,0x49,0x38,0x2b,0x6a,0x36,0x24,0x49,0x73,0x2b,0x2e,0x2f,0x76,0x2a,0x26,
0x49,0x79,0x2b,0x33]
print ''.join(chr(table[i%4]^unk_400918[i]) for i in range(len(unk_400918)))
```

이렇게 역연산을 하면 `RC3-NOT-THE-FLAG-YOURE-LOOKING-FOR` 가 나오게된다.

근데 이게 플래그가 아니라고 한다.

이 FLE 64bit 파일의 hex 값을 보면 마지막쪽에 GALF가 있는데 이걸 뒤집으면 FLAG가 되는데 이 바이너리를 뒤집으면 될 것 같았다. 이 hex 값을 전체 다 뒤집으면 32bit 바이너리가 나온다. 되게 신기했다.

```python
f = open('fle','rb')
d = f.read()[::-1]
f.close()
f = open('fle_reverse','wb')
f.write(d)
f.close()
```

파일의 이름도 보면 거꾸로 뒤집어져있었다. FLE ELF 그냥 문제 제목으로 힌트를 준셈이다. 

![](https://user-images.githubusercontent.com/32904385/62198637-f361b080-b3bc-11e9-9235-27a0c3951e7b.png)

이렇게 hex값을 다 받아와서 뒤집은 후 보면 FLAG를 이제 패치해줘서 `7F454C46` ELF 헤더로 바꾸어주면 된다. 

그러면 32bit 바이너리가 된다. 심지어 실행도 된다.

```c
void __usercall start(int a1@<eax>)
{
  int v1; // eax
  unsigned __int32 *v2; // eax
  signed int i; // ecx
  int v4; // eax
  unsigned __int32 v5; // [esp-6Ch] [ebp-6Ch]
  unsigned __int32 v6; // [esp-68h] [ebp-68h]
  unsigned __int32 v7; // [esp-64h] [ebp-64h]
  unsigned __int32 v8; // [esp-60h] [ebp-60h]
  unsigned __int32 v9; // [esp-5Ch] [ebp-5Ch]
  unsigned __int32 v10; // [esp-58h] [ebp-58h]
  unsigned __int32 v11; // [esp-54h] [ebp-54h]
  unsigned __int32 v12; // [esp-50h] [ebp-50h]
  unsigned __int32 v13; // [esp-4Ch] [ebp-4Ch]
  int v14; // [esp-48h] [ebp-48h]
  int v15; // [esp-44h] [ebp-44h]
  unsigned int v16; // [esp-40h] [ebp-40h]
  int v17; // [esp-3Ch] [ebp-3Ch]
  char v18; // [esp-1Ch] [ebp-1Ch]

  v1 = sub_8048066(a1, 55, byte_8048175);
  LOBYTE(v1) = 3;
  __asm { int     80h; LINUX - }
  if ( v1 != 31 )
    goto LABEL_11;
  v18 = 0;
  v16 = 134512640;
  v15 = 0;
  v14 = 0;
  v13 = _byteswap_ulong(0x61050000u);
  v12 = _byteswap_ulong(0x67631205u);
  v11 = _byteswap_ulong(0x7B1D507Fu);
  v10 = _byteswap_ulong(0x68764664u);
  v9 = _byteswap_ulong(0x6C1A5567u);
  v8 = _byteswap_ulong(0x6C1B2E6Au);
  v7 = _byteswap_ulong(0x3723608u);
  v6 = _byteswap_ulong(0x141A4719u);
  v5 = _byteswap_ulong(0x61684A64u);
  v2 = &v5;
  for ( i = 0; i < 30; ++i )
  {
    *(&v17 + i) ^= *v2;
    v2 = (v2 + 1);
  }
  v1 = sub_804804C(29, 30, &dword_8049BCC[v16 / 4 - 33628160], &v17);
  if ( v1 )
LABEL_11:
    sub_8048066(v1, 22, dword_80481AC);
  else
    sub_8048066(0, 15, &word_80481C2);
  v4 = sys_exit(1);
}
```

우선은 분기를 피하기 위해서 입력 받는 문자열의 길이를 30으로 맞춰놓고 디버깅하였다.

![](https://user-images.githubusercontent.com/32904385/62209203-eef4c200-b3d3-11e9-98be-969543d9d490.png)

EBX의 값까지 ECX를 1씩 증가 시켜서 for문 루프를 돌아준다. 그러면서 입력한 값과 [eax]값과 인덱스끼리 xor연산을 해준다. 

![](https://user-images.githubusercontent.com/32904385/62209201-ee5c2b80-b3d3-11e9-9a34-20afd9fe9072.png)

eax값의 주소 값을 보니까 이 테이블과 입력받은 값을 xor 연산해주고 있다.

![](https://user-images.githubusercontent.com/32904385/62211917-b3112b00-b3da-11e9-8903-1e553323fbf3.png)

이제 요 결과 값을 긁어와서 역연산 해주면 passcode를 알 수 있다.

```python
result = [0x33,0x2b,0x79,0x49,0x26,0x2a,0x76,0x2f,0x2e,0x2b,0x73,0x49,0x24,0x36,0x6a,0x2b,0x38,0x49,0x78,0x25,0x2d,0x22,0x12,0x21,0x29,0x30,0x12,0x30,0x2e,0x2a]
eax = [0x61,0x68,0x4a,0x64,0x14,0x1a,0x47,0x19,0x3,0x72,0x36,0x8,0x6c,0x1b,0x2e,0x6a,0x6c,0x1a,0x55,0x67,0x68,0x76,0x46,0x64,0x7b,0x1d,0x50,0x7f,0x67,0x63]
print ''.join(chr(result[i]^eax[i]) for i in range(30))
```

![](https://user-images.githubusercontent.com/32904385/62212543-e56f5800-b3db-11e9-9ecd-d573f53a810a.png)

**FLAG : `RC3-2016-YEAH-DATS-BETTER-BOII`**