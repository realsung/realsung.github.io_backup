---
layout: post
title: GCC Compile Option
author: "Realsung"
comments: true
featured: true
sitemap :
  changefreq : gcc
  priority : 1.0
---



## 3 2비트로 컴파일

-m32

## 64비트로 컴파일

-m64

## 스택 바운더리 제거

-mpreferred-stack-boundary=2

## 최적화 옵션하면 __printf_chk@plt말고 printf@plt 이렇게 보이도록 함

-U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=0

## CANARY 제거

-fno-stack-protector

## CANARY 생성

-fstack-protector

## CANARY FULL

-fstack-protector-all

## NX Bit 제거

-z execstack

## 스택에 실행 권한

-z execstack

## No RELRO인데 Partial RELRO로

-z relro

## No RELRO인데 Full RELRO로

-z relro -z now

## Partial RELRO인데 No RELRO로

-z norelro

## Partial RELRO인데 Full RELRO로

-z now

## .text 랜덤

-fpie

## PIE 사용

-fpie -pie

## PIE 제거

-no-pie

## ETC

## 운영체제에서 계속 ASLR 끄기

echo "kernel.randomize_va_space=0" > /etc/sysctl.d/01-disable-aslr.conf