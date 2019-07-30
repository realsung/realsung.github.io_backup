---
layout: post
title: 2019 Codegate open CTF Writeup
author: "Realsung"
comments: true
featured: true
sitemap :
  changefreq : codegate openct
  priority : 1.0
---



# Reversing

### J._.n3utr0n

` process hallow ` 기법을 사용했다고 한다.

아직 좀 더 분석해야 하는 문제이다. `drop.exe` 파일을 드랍한 다음 `svchost.exe` 프로세스를 생성하고 이 프로세스에 drop.exe의 내용을 삽입하고 삭제한다.

```c++
int __cdecl main(int argc, const char **argv, const char **envp)
{
  CHAR *lpBuffer; // ST54_4
  HANDLE hFile; // ST28_4
  size_t i; // [esp+34h] [ebp-13450h]
  DWORD NumberOfBytesWritten; // [esp+40h] [ebp-13444h]
  size_t Size; // [esp+44h] [ebp-13440h]
  char Src; // [esp+48h] [ebp-1343Ch]
  char Dst; // [esp+49h] [ebp-1343Bh]
  char Buffer; // [esp+9A4Ch] [ebp-9A38h]
  char v12; // [esp+9A4Dh] [ebp-9A37h]
  CHAR CommandLine[4]; // [esp+13450h] [ebp-34h]
  CHAR CmdLine; // [esp+13458h] [ebp-2Ch]
  char v15; // [esp+13459h] [ebp-2Bh]
  char v16; // [esp+1345Ah] [ebp-2Ah]
  char v17; // [esp+1345Bh] [ebp-29h]
  char v18; // [esp+1345Ch] [ebp-28h]
  char v19; // [esp+1345Dh] [ebp-27h]
  char v20; // [esp+1345Eh] [ebp-26h]
  char v21; // [esp+1345Fh] [ebp-25h]
  char v22; // [esp+13460h] [ebp-24h]
  char v23; // [esp+13461h] [ebp-23h]
  char v24; // [esp+13462h] [ebp-22h]
  char v25; // [esp+13463h] [ebp-21h]
  char v26; // [esp+13464h] [ebp-20h]
  char v27; // [esp+13465h] [ebp-1Fh]
  char v28; // [esp+13466h] [ebp-1Eh]
  char v29; // [esp+13467h] [ebp-1Dh]
  char v30; // [esp+13468h] [ebp-1Ch]
  char v31; // [esp+13469h] [ebp-1Bh]
  char v32; // [esp+1346Ah] [ebp-1Ah]
  char v33; // [esp+1346Bh] [ebp-19h]
  char v34; // [esp+1346Ch] [ebp-18h]
  char v35; // [esp+1346Dh] [ebp-17h]
  char v36; // [esp+1346Eh] [ebp-16h]
  char v37; // [esp+1346Fh] [ebp-15h]
  char v38; // [esp+13470h] [ebp-14h]
  char v39; // [esp+13471h] [ebp-13h]
  char v40; // [esp+13472h] [ebp-12h]
  char v41; // [esp+13474h] [ebp-10h]
  char v42; // [esp+13475h] [ebp-Fh]
  char v43; // [esp+13476h] [ebp-Eh]
  char v44; // [esp+13477h] [ebp-Dh]
  char v45; // [esp+13478h] [ebp-Ch]
  char v46; // [esp+13479h] [ebp-Bh]
  char v47; // [esp+1347Ah] [ebp-Ah]
  char v48; // [esp+1347Bh] [ebp-9h]
  char v49; // [esp+1347Ch] [ebp-8h]

  v41 = 117;
  v42 = 99;
  v43 = 126;
  v44 = 97;
  v45 = 63;
  v46 = 116;
  v47 = 105;
  v48 = 116;
  v49 = 0;
  CmdLine = 114;
  v15 = 124;
  v16 = 117;
  v17 = 63;
  v18 = 116;
  v19 = 105;
  v20 = 116;
  v21 = 49;
  v22 = 62;
  v23 = 122;
  v24 = 49;
  v25 = 117;
  v26 = 116;
  v27 = 125;
  v28 = 49;
  v29 = 82;
  v30 = 43;
  v31 = 77;
  v32 = 117;
  v33 = 99;
  v34 = 126;
  v35 = 97;
  v36 = 63;
  v37 = 116;
  v38 = 105;
  v39 = 116;
  v40 = 0;
  Src = 0;
  memset(&Dst, 0, 0x9A00u);
  Size = 0;
  Buffer = 0;
  memset(&v12, 0, 0x9A00u);
  strcpy(CommandLine, "svchost");
  NumberOfBytesWritten = 0;
  sub_401770(&CmdLine, 26);
  sub_401770(&v41, 8);
  if ( !__FrameUnwindToState(0, &Src, (int)&Size) )
    return 0;
  memset(&Buffer, 0, Size + 1);
  memcpy(&Buffer, &Src, Size);
  for ( i = 0; i <= Size; ++i )
    *(&Buffer + i) = ~*(&Buffer + i) ^ 0x41;
  lpBuffer = (CHAR *)operator new[](0x104u);
  GetTempPathA(0x104u, lpBuffer);
  *(_BYTE *)(sub_401000(lpBuffer, 92) + 1) = 0;
  qmemcpy(&lpBuffer[strlen(lpBuffer)], &v41, &v41 + strlen(&v41) + 1 - &v41);
  hFile = CreateFileA(lpBuffer, 0x40000000u, 0, 0, 2u, 0x80u, 0);
  WriteFile(hFile, &Buffer, 0x9A00u, &NumberOfBytesWritten, 0);
  sub_4010C0(CommandLine, lpBuffer);
  WinExec(&CmdLine, 5u);
  return 0;
}
```

<br />

### babyarm

arm_asm.s 파일이 주어져서 핸드레이를 해야한다.

```assembly
flag:
	.ascii	"]cX^r@VC`b*V+idVk_+eVD(gjt\000"
main:
	@ args = 0, pretend = 0, frame = 8
	@ frame_needed = 1, uses_anonymous_args = 0
	push	{fp, lr}
	lr fp 순으로 stack에 값을 넣는다. 함수 프롤로그 부분
	add	fp, sp, #4
	fp
	sub	sp, sp, #8
	sp -= 8이라고 볼 수 있다. 
	스택 사용 공간을 할당하는듯 하다.
	ldr	r0, .L5
	*(r0) = .L5
	bl	srand
	bl	rand
	mov	r2, r0
	ldr	r3, .L5+4
	smull	r1, r3, r2, r3
	asr	r1, r3, #2
	asr	r3, r2, #31
	sub	r1, r1, r3
	mov	r3, r1
	lsl	r3, r3, #2
	add	r3, r3, r1
	lsl	r3, r3, #1
	sub	r3, r2, r3
	r3 = r2 - r3
	str	r3, [fp, #-8]
	*(fp-8)에 r3를 넣는다
	mov	r3, #0
	r3 = 0으로 셋팅
	str	r3, [fp, #-12]
	*(fp-12)에 r3를 넣는다.
	b	.L2
	.L2 함수 호출한다.
.L3:
	ldr	r2, .L5+8
	ldr	r3, [fp, #-12]
	add	r3, r2, r3
	ldrb	r2, [r3]	@ zero_extendqisi2
	ldr	r3, [fp, #-8]
	and	r3, r3, #255
	add	r3, r2, r3
	and	r1, r3, #255
	ldr	r2, .L5+8
	ldr	r3, [fp, #-12]
	add	r3, r2, r3
	mov	r2, r1
	strb	r2, [r3]
	ldr	r3, [fp, #-12]
	add	r3, r3, #1
	str	r3, [fp, #-12]
.L2:
	ldr	r3, [fp, #-12]
	r3 = *(fp-12)
	cmp	r3, #25
	r3가 25인지 비교하고 25면 제로 플래그 0으로 세팅
	글자수만큼 계속 ~
	ble	.L3
	.L3 연산 결과가 작거나 같으면 .L3를 호출한다.
	ldr	r1, .L5+8
	ldr	r0, .L5+12
	bl	printf
	mov	r3, #0
	r3에 0을 넣는다.
	mov	r0, r3
	r0에도 0을 넣는다.
	리턴 값에 0을 넣은 것이다. return 0; 해준듯 하다.
	sub	sp, fp, #4
	@ sp needed
	pop	{fp, pc}
	함수 프롤로그 부분인듯하다.
```



```python
a="]cX^r@VC`b*V+idVk_+eVD(gjt\000"
print ''.join(chr(ord(i)+9) for i in a)
```

**FLAG : `flag{I_Lik3_4rm_th4n_M1ps}`**

<br />

### easy_rev

```
easy_rev: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 2.6.32, BuildID[sha1]=d6ab8e0c86636e8331cc465ae54a5013598dd79e, not stripped
```

64비트 바이너리다. 

그냥 메인에서는 10개 입력 받아준다.

```c
__int64 __fastcall swap(__int64 a1)
{
  unsigned int v2; // [rsp+18h] [rbp-48h]
  signed int v3; // [rsp+1Ch] [rbp-44h]
  int v4; // [rsp+20h] [rbp-40h]
  signed int i; // [rsp+24h] [rbp-3Ch]
  signed int j; // [rsp+28h] [rbp-38h]
  signed int k; // [rsp+2Ch] [rbp-34h]
  int v8; // [rsp+30h] [rbp-30h]
  int v9; // [rsp+34h] [rbp-2Ch]
  int v10; // [rsp+38h] [rbp-28h]
  int v11; // [rsp+3Ch] [rbp-24h]
  int v12; // [rsp+40h] [rbp-20h]
  int v13; // [rsp+44h] [rbp-1Ch]
  int v14; // [rsp+48h] [rbp-18h]
  int v15; // [rsp+4Ch] [rbp-14h]
  int v16; // [rsp+50h] [rbp-10h]
  int v17; // [rsp+54h] [rbp-Ch]
  unsigned __int64 v18; // [rsp+58h] [rbp-8h]

  v18 = __readfsqword(0x28u);
  v2 = 0;
  v3 = 3;
  v4 = 0;
  v8 = 79;
  v9 = 4;
  v10 = 36;
  v11 = 628;
  v12 = 117;
  v13 = 62;
  v14 = 2458;
  v15 = -101;
  v16 = 41;
  v17 = 239;
  for ( i = 0; i <= 9; ++i )
  {
    if ( v4 % 3 )
    {
      if ( v4 % 3 == 1 )
        v3 -= i;
      else
        v3 += i;
    }
    else
    {
      v3 *= i;
    }
    *(_DWORD *)(a1 + 4LL * i) = v3 ^ *(_DWORD *)(4LL * i + a1);
    ++v4;
  }
  for ( j = 0; j <= 9; ++j )
    *(_DWORD *)(4LL * j + a1) ^= 0xFu;
  for ( k = 0; k <= 9; ++k )
  {
    if ( *(_DWORD *)(4LL * k + a1) == *(&v8 + k) )
      ++v2;
  }
  return v2;
}
```

swap함수를 보면은 v3의 값을 구해서 a1[i]의 값과 xor연산을 해준다. 그리고 밑에 보면 0xF와 xor한 값이 *(v8[i])이면 된다. 

이제 이것을 역연산을 해서 구하면 된다.

```python
table = [79,4,36,628,117,62,2458,-101,41,239]
v3 = 3
a=[]
for i in range(10):
	if i % 3:
		if i % 3 == 1:
			v3 -= i
			a.append(v3)
		else:
			v3 += i
			a.append(v3)
	else:
		v3 *= i
		a.append(v3)

b=[]
for i in range(10):
	for j in range(-3000,3000):
		if table[i] == j^15^a[i]:
			b.append(j)
			break

print 'FLAG key is : ' + str(sum(b))
```

브루트포스해서 풀었다.

```
$ ./easy_rev
==========================================
       NEWBIE REV1 right here !!
solve the magic I putted, and get the flag
==========================================
>> 64 -12 42 632 -123 53 2445 -123 63 1
++++++++++++++++++++++++++++++++++++++++++
Let's See the result!!!!
++++++++++++++++++++++++++++++++++++++++++
>> Yes, You got right ( IF YOU CERTAINLY INSERTED EXACTLY 10 NUMBERS )
>> You just need to 'add' all the no for every index. That sum is key for flag file !!
>> (flag file is encryted aes-256-cbc of openssl)
```

**FLAG : `flag{R2versing_1s_b4sed_0n_H4cking_:)}`**

<br />

### find_flag

파이썬으로 만들어진 exe 파일이다. [python-exe-unpacker](https://github.com/countercept/python-exe-unpacker) 를 이용해서 풀었다. 

그냥 파일 추출해주면 플래그가 있다.

**FLAG : `Pyth0n_m4k2_2X2_B1n4ry_:D`**

<br />

### seori
