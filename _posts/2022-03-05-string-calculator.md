---
layout: single
title: "[C] String Calculator."
categories: C
tag: [C, calculator]
toc: true
toc_sticky: true
---

```c
/*
제    목 : 컴퓨팅사고력 7주차 과제
기    능 : 문자열을 입력받는 계산기
파일이름 : 201622821_김영진_컴퓨팅사고력_7주차과제.cpp
수정날짜 : 2020. 05. 10
작 성 자 : 김영진
*/
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>

int main(void) {

	int num1 = 0, num2 = 0;
	int i = 0, j = 0;
	char eq[100];
	char op;

	printf("문자열을 입력받는 계산기 \n");
	printf("입력:");
	scanf("%s", eq);

	for (;; i++) {
		if (eq[i] < '0' || eq[i]>'9')break;
		eq[i] -= '0';
		if (eq[i + 1] > '0' || eq[i + 1] < '9') {
			num1 *= 10;
		}
		num1 += eq[i];
	}
	op = eq[i++];
	for (; eq[i]; i++) {
		eq[i] -= '0';
		if (eq[i + 1] > '0' || eq[i + 1] < '9') {
			num2 *= 10;
		}
		num2 += eq[i];
	}

	switch (op) {
	case '+':
		printf("%d + %d = %d", num1, num2, num1 + num2);
		break;
	case '-':
		printf("%d - %d = %d", num1, num2, num1 - num2);
		break;
	case '*':
		printf("%d * %d = %d", num1, num2, num1 * num2);
		break;
	case '/':
		printf("%d / %d = %.1lf", num1, num2, ((double)num1 / num2));
		break;
	default:
		printf("잘못된 연산자입니다. \n");
		break;
	}
	return 0;
}
```