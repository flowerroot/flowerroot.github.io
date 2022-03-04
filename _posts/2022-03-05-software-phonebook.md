---
layout: single
title: "Phonebook program"
categories: C
tag: [C]
toc: true
---

2019년도 2학기 소프트웨어공학 수업에서 제출했던 과제물입니다.
이떄 군 휴학을 끝내고 복학하여 처음 C언어를 접했습니다.
이 코드는 제가 코딩을 배우며 처음 만든 프로그램입니다.
지금보니 많이 허접하지만 그래도 애착이 가네요 ㅎㅎ

**전화번호부** 프로그램을 만들어보았고 구현되지 않은 기능이 많습니다. ㅋㅋ

### 코드

```c
#define _CRT_SECURE_NO_WARNINGS // scanf 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>

typedef struct // 구조체선언
{
	int d1; // 번호저장을 위한 변수선언
	char s1[10]; // 이름저장을 위한 변수선언
}gujo;

int main(void)
{
	gujo num[100]; // 구조체 num 변수선언 용량 100

	int select; // if문을 통한 선택지 구현을 위해 select변수 선언
	int memory = 0; // 저장할 연락처의 순서 0부터 시작
	char search[10]; // 검색기능 구현을위해 search 변수 선언
	int sir;

	while (1)
	{
		printf("----------------------------------------- \n");
		printf("전화번호부 \n");
		printf("무엇을 하실 것인지 입력하세요. \n");
		printf("1. 연락처저장 \n");
		printf("2. 연락처검색(통화 및 삭제) \n");
		printf("3. 저장된 연락처 목록보기 \n");
		printf("4. 종료 \n");
		printf("입력:");

		scanf("%d", &select); // 입력된select를 스캔 일치하는 if문으로 이동

		if (select == 1) // 연락처 추가
		{
			printf("----------------------------------------- \n");
			printf("연락처 정보를 입력해주세요. \n");

			printf("이름:");
			scanf("%s", num[memory].s1); // 이름입력
			printf("번호:");
			scanf("%d", &num[memory].d1); // 번호입력

			printf("----------------------------------------- \n");
			printf("%s:0%d ", num[memory].s1, num[memory].d1); // 입력된 연락처를 게시
			printf(" 성공적으로 저장이 완료되었습니다. \n");

			memory++; // 덮어쓰기 방지용 ++
		}

		else if (select == 2) // 검색
		{
			printf("----------------------------------------- \n");
			printf("이름으로 검색합니다. \n");
			printf("검색:");
			scanf("%s", search); // 검색어를 변수 search에 초기화

			for (int i = 0; i < memory; i++) { /* strcmp문을 통한 문자열비교를위해 변수 i=0; 선언해주고
														일치할때까지 하나씩 추가해서 비교를하기위해 ++ */
				if (strcmp(search, num[i].s1) == 0) { // search에 초기화된 검색어와 num에 저장된 연락처를 비교

					printf("----------------------------------------- \n");
					printf("%s:0%d", num[i].s1, num[i].d1); // 일치하는 연락처가 나오면 출력
					printf("검색 결과로 무엇을 하시겠습니까? \n");
					printf("1. 통화 \n2. 삭제 \n");
					printf("입력:");
					scanf("%d", &sir); // 입력된 선택지를 스캔			

					if (sir == 1) // 통화하기
					{
						printf("----------------------------------------- \n");
						printf("통화를 연결합니다. \n");
						printf("통화중... \n");
						printf("통화를 종료합니다. \n");
					}
					if (sir == 2) // 삭제하기
					{
						printf("----------------------------------------- \n");
						printf("%s:0%d 연락처의 정보를 삭제합니다. \n", num[i].s1, num[i].d1); // 삭제된 연락처정보 노출
					}
				}				
			}
		}
		
		else if (select == 3) // 전화번호 목록노출
		{			
			printf("----------------------------------------- \n");
			printf("저장된 연락처 목록입니다. \n");
			for (int list = 0; list < memory; list++) // 변수list를 0으로 선언 memory보다 작을때마다 +1해서 반복			
				printf("%s:0%d \n",num[list].s1, num[list].d1); // list에 해당하는 순서의 연락처를 노출
		}	

		else if (select == 4) // 프로그램 종료
		{
			printf("프로그램을 종료합니다.");
			break; // 종료
		}
	}
	return 0; // 4번의 break을 통해 이 곳으로 오면 프로그램이 종료됨.
}
```