---
layout: single
title: "[C] String Converter."
categories: C
tag: [C, converter]
toc: true
toc_sticky: true
---

```c
/*
제    목 : 컴퓨팅사고력 10주차 과제
기    능 : 소문자를 대문자로 바꿔준다.
파일이름 : 201622821_김영진_컴퓨팅사고력_10주차과제.cpp
수정날짜 : 2020. 05. 21
작 성 자 : 김영진
*/
#include <stdio.h>
#include <string.h>
void upper_case(char input[]); // 문자열 변환 함수 선언

int main(void) {
	char input[] = "My Name is YeongJin Kim";
	upper_case(input);
	printf("%s", input);

	return 0;
}

void upper_case(char input[]) { // 문자열 변환 함수 정의
	for (int i = 0; i < strlen(input); i++) { // 문자열의 길이만큼 for문 반복
		if (input[i] >= 97) { // 97보다 크거나 같을 경우
			input[i] -= 32; // 32를 빼서 대문자로 변환
		}
	}
}
```