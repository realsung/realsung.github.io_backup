---
layout: post
title: ARM Reversing
author: "Realsung"
comments: true
featured: true
sitemap :
  changefreq : arm reversing
  priority : 1.0
---

ARM Reversing 문제 풀기 위해 좀 정리하려고 한다.

<br />

## Register

```
R0 ~ R12 : 범용 레지스터 (다목적 레지스터)
R0 : 함수 리턴 값 저장 (EAX 같은 느낌)
R0 ~ R3 : 함수 호출 인자 전달
R13 ~ R15 : 특수 레지스터
R13(SP) : 스택 포인터 : 스택의 맨 위를 가리킴
R14(LR) : 링크 레지스터 : 서브루틴 후에 돌아갈 리턴 주소 저장
R15(PC) : 프로그램 카운터 : 현재 fetch되고 있는 명령어의 주소 - 따라서 현재 실행되는 명령어의 다음다음 주소
```

<br />

## CSPR Register

CSPR(Current Program Status Register)

CPSR의 레이아웃은 32비트를 기준으로 8 비트씩, 플래그(Flag) 비트, 상태(Status) 비트, 확장(Extension)비트, 제어(Control)비트로 나뉜다.

![](https://user-images.githubusercontent.com/32904385/61804158-64a9dc80-ae6e-11e9-9cab-d30ec4dd19af.png)

```
N(Negative) : 음수 플래그 (연산 결과가 음수일 경우)
Z(Zero) : 제로 플래그 (연산 결과가 0일 경우, 비교 결과가 같을 경우)
C(Carry) : 캐리 플래그 (연산 결과에서 자리 올림이 발생한 경우)
V(oVerflow) : 오버플로우 플래그 (연산 결과가 오버플로우 난 경우)
```

<br />

## Instruction

```
형식
<Operation>{<cond>}{S} Rd, Rn, Op2
- Operation : 명령어
- cond : 접미사
- S : CSPR Setting
- Rd(Destination Register) : 목적지 레지스터
- Rn : 레지스터
- 두 번째 OPERAND : 레지스터 or 상수(앞에 #이 붙음)

ex) ADD r0, r1, r2 ; r0 = r1 + r2
```

<br />

## 접미사

```
EQ	: Z Set				                equal
NE	: Z Clear			                not equal
GE	: N equal V			              greater or equal
LT	: N not equal V		            less than
GT	: Z Clear and (N equal V)   	greater than
LE	: Z Set or (N not equal V)  	less than or equal
S	  : Execution Instruction and CPSR Register Setting

ex) ADDEQ r0, r1, r2 ; if(ZF) r0 = r1 + r2 -> if(r0 == r1+r2){ }
```

![](https://user-images.githubusercontent.com/32904385/61806777-40043380-ae73-11e9-8948-709d3dad72e0.jpg)

<br />

## Function Calling

```
1) 프롤로그 (서브루틴을 호출하기 직전)에 r4 부터 r11 까지 스택에 저장(push)하고 r14(리턴어드레스)를 스택에 저장(push)한다.
2) r0 - r3 중에 함수에 전달할 인자값이 있으면 이것을 r4 - r11 (임의)로 복사한다.
3) 나머지 지역변수들은 r4 - r11 중 남아있는 곳에 할당한다. 
4) 연산을 수행한 후 다른 서브루틴이 있다면 호출한다.
5) r0 에 리턴값(결과)를 저장한다.
6) 에필로그(원래있던 곳으로 복귀)에 스택에서 r4 - r11 을 꺼내고 r15(프로그램 카운터)에서 리턴어드레스(복귀주소)를 꺼낸다.
```

