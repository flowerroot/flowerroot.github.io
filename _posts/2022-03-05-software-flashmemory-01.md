---
layout: single
title: "Flash Memory 1차"
categories: C
tag: [C, FlashMemory]
toc: true
---

2019년도 2학기 소프트웨어공학 수업 과제물 **flash memory 1차**입니다.
아직 구조체에 대한 이해가 부족하던 때라서 header 파일에서 뻘짓하고 있는 모습을 볼 수 있네요...ㅋㅋ
### main

```c
/*
* 파일이름 : Flash Memory
* 작 성 자 : 김영진
* 작 성 일 : 2019년 11월 24일
* 목    적 : 소프트웨어 공학 3차 프로젝트
*/

#include "header.h"
#include <stdio.h>

FILE* fp;
char text[30]; //입력할 텍스트 저장
int sect; //섹터
int sect_share; //섹터 몫
int sect_remainder; //섹터 나머지

int main(void) {

	char autority[2]; //권한 입력받음

	fileproduce(); //프로그램 최초 실행 시 txt파일 생성
	dataload(); //txt파일의 데이터를 구조체에 복사


	printf("┌------------------------------------------┐ \n");
	printf("│             < FLASH MEOMRY >             │ \n");
	printf("│------------------------------------------│ \n");
	printf("│ 권한입력: w = write, r = read, e = erase │ \n");
	printf("│           x = 종료                       │ \n");
	printf("│ 섹터입력: 0~127 까지 원하는섹터를 입력   │ \n");
	printf("│ 문자입력: 섹터에 입력하려는 문자를 입력  │ \n");
	printf("└------------------------------------------┘ \n");
	while (1) {
		printf("==================== \n");
		printf("권한입력:");
		scanf("%s", autority);
		printf("섹터입력:");
		scanf("%d", &sect);

		sect_share = sect / 32; //입력된 sect의 몫을 sect_share에 대입
		sect_remainder = sect % 32; //입력된 sect의 나머지를 sect_remainder에 대입

		//autority 가 w와 일치할 때 write
		if (strcmp(autority, "w") == 0) {			
			datacheck();
		}

		//autority가 r과 일치할때 read
		else if (strcmp(autority, "r") == 0) {			
			flash_read();
		}

		//autority가 e와 일치할때 erase
		else if (strcmp(autority, "e") == 0) {			
			flash_erase();
		}

		else if (strcmp(autority, "x") == 0) {
			return 0;
		}

		else {
			printf("잘못된 접근입니다. \n");
		}
	}
}

void datacheck() {

	char lines[30];
	int sel;

	//----------------------------sect의 몫에 해당하는 값으로 이동
	switch (sect_share) {
	case 0: //0번째 블록
		fp = fopen("block00.txt", "rt"); //rt권한으로 파일개방
		if (fscanf(fp, "%s", lines) > 0) { //fscanf에 정수값이 반환될 시 해당 if문 발동
			printf("해당 블록에 데이터가 존재합니다. \n");
			printf("삭제하고 계속 하시겠습니까? \n");
			printf("1. 예 \n2. 아니오 \n");
			scanf("%d", &sel);
			if (sel == 1) {
				fclose(fp);
				printf("계속 진행합니다. \n");
				Flash_Write(); //wt권한으로 새로운 데이터 입력
			}
			else if (sel == 2) {
				printf("취소되었습니다. \n");
				return ;
			}
			else {
				printf("잘못된 입력입니다. \n");
				return;
			}
		}
		else if (fscanf(fp, "%s", lines) < 0) { //fscanf에 정수값이 반환되지 않을 시
			fclose(fp);
			Flash_Write(); //데이터입력
		}
		
		break;
		// 나머지 case는 case 0 과 구동원리가 같기때문에 설명을 생략하겠습니다.
	case 1: //1번째 블록
		fp = fopen("block01.txt", "rt");
		if (fscanf(fp, "%s", lines) > 0) {
			printf("해당 블록에 데이터가 존재합니다. \n");
			printf("삭제하고 계속 하시겠습니까? \n");
			printf("1. 예 \n2. 아니오 \n");
			scanf("%d", &sel);
			if (sel == 1) {
				fclose(fp);
				printf("계속 진행합니다. \n");
				Flash_Write();
			}
			else if (sel == 2) {
				printf("취소되었습니다. \n");
				return;
			}
			else {
				printf("잘못된 입력입니다. \n");
				return;
			}
		}
		else if (fscanf(fp, "%s", lines) < 0) {
			fclose(fp);
			Flash_Write();
		}
	
		break;
	case 2: //2번째 블록
		fp = fopen("block02.txt", "rt");
		if (fscanf(fp, "%s", lines) > 0) {
			printf("해당 블록에 데이터가 존재합니다. \n");
			printf("삭제하고 계속 하시겠습니까? \n");
			printf("1. 예 \n2. 아니오 \n");
			scanf("%d", &sel);
			if (sel == 1) { 
				fclose(fp);
				printf("계속 진행합니다. \n");
				Flash_Write();
			}
			else if (sel == 2) {
				printf("취소되었습니다. \n");
				return;
			}
			else {
				printf("잘못된 입력입니다. \n");
				return;
			}
		}
		else if (fscanf(fp, "%s", lines) < 0) {
			fclose(fp);
			Flash_Write();
		}
		
		break;
	case 3: //3번째 블록
		fp = fopen("block03.txt", "rt");
		if (fscanf(fp, "%s", lines) > 0) {
			printf("해당 블록에 데이터가 존재합니다. \n");
			printf("삭제하고 계속 하시겠습니까? \n");
			printf("1. 예 \n2. 아니오 \n");
			scanf("%d", &sel);
			if (sel == 1) {
				fclose(fp);
				printf("계속 진행합니다. \n");
				Flash_Write();
			}
			else if (sel == 2) {
				printf("취소되었습니다. \n");
				return;
			}
			else {
				printf("잘못된 입력입니다. \n");
				return;
			}
		}
		else if (fscanf(fp, "%s", lines) < 0) {
			fclose(fp);
			Flash_Write();
		}
		
		break;
	default:
		break;
	}
}

void Flash_Write() {
	printf("문자입력:");
	scanf("%s", text);

	switch (sect_share) { //sect의 몫에 해당하는 case로 이동
	case 0: //0번째 블록
		strcpy(block00[sect_remainder].text, text); //입력된 text를 구조체에 복사
		fp = fopen("block00.txt", "wt"); //wt권한으로 파일개방
		fprinting(); //fprinting 함수 발동
		fclose(fp); //파일닫음

		fp = fopen("sect00.txt", "wt"); //wt권한으로 sect00파일을 개방
		fprintf(fp, "%d", sect_remainder); //입력된 sect의 나머지값을 저장
		//나머지값을 왜 저장하는지는 밑에서 마저 설명
		fclose(fp); //파일닫음
		break;
		
		//나머지 case는 case 0 과 구동원리가 동일하기때문에 설명을 생략.
	case 1: //1번째 블록
		strcpy(block01[sect_remainder].text, text);
		fp = fopen("block01.txt", "wt");
		fprinting();
		fclose(fp);

		fp = fopen("sect01.txt", "wt");
		fprintf(fp, "%d", sect_remainder);
		fclose(fp);
		break;
	case 2: //2번째 블록
		strcpy(block02[sect_remainder].text, text);
		fp = fopen("block02.txt", "wt");
		fprinting();
		fclose(fp);

		fp = fopen("sect02.txt", "wt");
		fprintf(fp, "%d", sect_remainder);
		fclose(fp);
		break;
	case 3: //3번째 블록
		strcpy(block03[sect_remainder].text, text);
		fp = fopen("block03.txt", "wt");
		fprinting();
		fclose(fp);

		fp = fopen("sect03.txt", "wt");
		fprintf(fp, "%d", sect_remainder);
		fclose(fp);
		break;
	}
}

void fprinting() {
	//sect의 나머지값만큼 개행문자로 채운 뒤 마지막에 text를 print
	//이런 방식으로 메모장안에서 섹터를 구분한다.
	for (int i = 0; i < sect_remainder; i++) {
		fprintf(fp, "\n");
	}
	fprintf(fp, "%s", text);
}

void flash_read() { //sect의 나머지값에 해당하는 구조체를 print
	switch (sect_share) {
	case 0:
		printf("데이터 결과 : %s \n", block00[sect_remainder].text);
		break;
	case 1:
		printf("데이터 결과 : %s \n", block01[sect_remainder].text);
		break;
	case 2:
		printf("데이터 결과 : %s \n", block02[sect_remainder].text);
		break;
	case 3:
		printf("데이터 결과 : %s \n", block03[sect_remainder].text);
		break;
	}
}

void flash_erase() {
	switch (sect_share) {
	case 0: //구조체에는 ""를 채우고 txt파일은 wt권한으로 열었다 닫는다.
		for (int i = 0; i < MAX; i++) {
			strcpy(block00[i].text, "");
		}
		fp = fopen("block00.txt", "wt");
		fclose(fp);
		break;
	case 1:
		for (int i = 0; i < MAX; i++) {
			strcpy(block01[i].text, "");
		}
		fp = fopen("block01.txt", "wt");
		fclose(fp);
		break;
	case 2:
		for (int i = 0; i < MAX; i++) {
			strcpy(block02[i].text, "");
		}
		fp = fopen("block02.txt", "wt");
		fclose(fp);
		break;
	case 3:
		for (int i = 0; i < MAX; i++) {
			strcpy(block03[i].text, "");
		}
		fp = fopen("block03.txt", "wt");
		fclose(fp);
		break;
	}
}

//----------------------------------프로그램 최초 실행 시 txt파일 생성.
void fileproduce() {
	fp = fopen("block00.txt", "at");
	fclose(fp);
	fp = fopen("block01.txt", "at");
	fclose(fp);
	fp = fopen("block02.txt", "at");
	fclose(fp);
	fp = fopen("block03.txt", "at");
	fclose(fp);

	fp = fopen("sect00.txt", "at");
	fclose(fp);
	fp = fopen("sect01.txt", "at");
	fclose(fp);
	fp = fopen("sect02.txt", "at");
	fclose(fp);
	fp = fopen("sect03.txt", "at");
	fclose(fp);
}

//----------------------------------txt파일에 저장된 데이터를 시스템구조체에 복사
void dataload() {
	int sect00;
	int sect01;
	int sect02;
	int sect03;

	char line[MAX];
	//------------------------------------- block00 구조체 데이터 로드
	fp = fopen("sect00.txt", "rt"); //rt권한으로 파일개방
	fscanf(fp, "%d", &sect00); //파일에있는 문자를 정수로받아 sect00에 대입
	fclose(fp); //파일닫음

	fp = fopen("block00.txt", "rt"); //rt권한으로 파일개방	
	for (int i = 0; i < sect00 + 1; i++) { //sect00에 대입된 정수+1 만큼 for문 반복
		fgets(line, MAX, fp); //개행기준으로 문자를받아 line에 저장

		strcpy(block00[i].text, line); //line에 저장된 문자를 구조체에 복사
	}
	fclose(fp); //파일닫음
	//------------------------------------- block01 구조체 데이터 로드
	fp = fopen("sect01.txt", "rt");
	fscanf(fp, "%d", &sect01);
	fclose(fp);

	fp = fopen("block01.txt", "rt");
	for (int i = 0; i < sect01 + 1; i++) {
		fgets(line, MAX, fp);

		strcpy(block01[i].text, line);
	}
	fclose(fp);
	//-------------------------------------- block02 구조체 데이터 로드
	fp = fopen("sect02.txt", "rt");
	fscanf(fp, "%d", &sect02);
	fclose(fp);

	fp = fopen("block02.txt", "rt");
	for (int i = 0; i < sect02 + 1; i++) {
		fgets(line, MAX, fp);

		strcpy(block02[i].text, line);
	}
	fclose(fp);
	//-------------------------------------- block03 구조체 데이터 로드
	fp = fopen("sect03.txt", "rt");
	fscanf(fp, "%d", &sect03);
	fclose(fp);

	fp = fopen("block03.txt", "rt");
	for (int i = 0; i < sect03 + 1; i++) {
		fgets(line, MAX, fp);

		strcpy(block03[i].text, line);
	}
	fclose(fp);
}
```


### header

```c
#define _CRT_SECURE_NO_WARNINGS //scanf 오류방지
#define MAX 32 //섹터수
#include <stdio.h>
#include <string.h>

void fprinting(); //write권한 중 fprint 기능 함수
void Flash_Write(); //write기능 함수

void flash_read(); //read기능 함수
void flash_erase(); //erase기능 함수

void datacheck(); //write 기능 발현하기 전 파일 내 데이터 유무 판별기능 함수

void fileproduce(); //프로그램 최초 실행시 txt파일 생성
void dataload(); //txt파일의 데이터를 프로그램 구조체에 복사

//0번째 블록 구조체
typedef struct {
	char text[30];
}BLOCK00;
BLOCK00 block00[MAX];

//1번째 블록 구조체
typedef struct {
	char text[30];
}BLOCK01;
BLOCK01 block01[MAX];

//2번째 블록 구조체
typedef struct {
	char text[30];
}BLOCK02;
BLOCK02 block02[MAX];

//3번째 블록 구조체
typedef struct {
	char text[30];
}BLOCK03;
BLOCK03 block03[MAX];
```