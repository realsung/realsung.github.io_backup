---
layout: post
title: CTF Tips
author: "Realsung"
comments: true
featured: true
---

#### 물론 제 경험상 작성.. 계속 작성해나갈듯

* file 명령어로 봤을 때 stripped 돼있고 main 함수가 안 보인다면 start로 가면 libc_start_main으로 콜하는 데 그 중에서 첫번째 인자로 들어가는 주소가 main함수이다.

* pwntools 쉘코드 생성

  ```python
  asm(shellcraft.amd64.sh(), arch='amd64')
  ```

* 64 Bit ROP

  ```markdown
  [buf] +  gadget [pop rdi; ret] + [/bin/sh string addr] + [system addr]
  ```

* 함수 오프셋

  ```python
  printf_off = e.symbols['printf']
  system_off = e.symbols['system']
  
  libc_base = printf - printf_off
  system = libc_base + system_off
  
  binsh = libc_base
  binsh += e.search('/bin/sh').next()
  ```