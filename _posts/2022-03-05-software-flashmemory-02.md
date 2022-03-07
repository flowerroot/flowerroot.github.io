---
layout: single
title: "Flash Memory 2차"
categories: C
tag: [C, FlashMemory]
toc: true
toc_sticky: true
---

2019년도 2학기 소프트웨어공학 수업 과제물 **flash memory 2차**입니다.
1차에서보다 구조체에 대한 이해도가 올라간 모습이 보이네요.
### main

```c
#include "header.h"

FILE* fp; //데이터베이스 구축을위한 FILE형 포인텨변수 fp 선언
int Sect; //섹터를 입력받음
int Sect_Share; //섹터를 나눈 몫
int Sect_Remainder; //섹터를 나눈 나머지
char Input[30]; //입력데이터
int choice; //Flash_Read 함수에서 나오는 결과값
//---------------------------------------
int main(void) {
	Data_Load(); //최초 프로그램 실행 시 데이터로드

	char Autority[2]; //권한입력받음
	printf("┌------------------------------------------┐ \n");
	printf("│             < FLASH MEOMRY >             │ \n");
	printf("│------------------------------------------│ \n");
	printf("│ 권한입력: w = Write, r = Read, e = Erase │ \n");
	printf("│           x = Exit, i = Init             │ \n");
	printf("│ 섹터입력: 0~127 까지 원하는섹터를 입력   │ \n");
	printf("│ 문자입력: 섹터에 입력하려는 문자를 입력  │ \n");
	printf("└------------------------------------------┘ \n");
	while (1) {
		printf("------------------------------------------- \n");
		printf("권한입력:");
		scanf("%s", Autority);
		printf("섹터입력:");
		scanf("%d", &Sect);

		Sect_Share = Sect / 32;
		Sect_Remainder = Sect % 32;

		if (strcmp(Autority, "w") == 0) {
			FTL_Write();
		}
		else if (strcmp(Autority, "r") == 0) {
			FTL_Read();
		}
		else if (strcmp(Autority, "e") == 0) {
			Flash_Erase();
		}
		else if (strcmp(Autority, "i") == 0) {
			Init();
		}
		else if (strcmp(Autority, "x") == 0) {
			return 0;
		}
		else {
			printf("잘못된 접근입니다. \n");
		}
	}
}

void FTL_Write() {
	printf("문자입력:");
	scanf("%s", Input);

	Flash_Read(); //Flash_Read에서 choice에 대한 초기화값이 결정된다.
	if (choice == 0) {
		Flash_Write();
	}
	else if (choice == 1) {
		Flash_Erase();
		Flash_Write();
	}
	else {
		printf("취소되었습니다. \n");
	}
	Data_Save();
}

void FTL_Read() {
	switch (Sect_Share) {
	case 0:
		printf("%s \n", Block_0[Sect_Remainder].Text);
		break;
	case 1:
		printf("%s \n", Block_1[Sect_Remainder].Text);
		break;
	case 2:
		printf("%s \n", Block_2[Sect_Remainder].Text);
		break;
	case 3:
		printf("%s \n", Block_3[Sect_Remainder].Text);
		break;
	default:
		break;
	}
}

void Flash_Write() {
	switch (Sect_Share) {
	case 0:
		strcpy(Block_0[Sect_Remainder].Text, Input);
		Block_0[0].Sect = Sect_Remainder; //Data_Load() 함수에서 사용할 섹터값 저장
		break;
	case 1:
		strcpy(Block_1[Sect_Remainder].Text, Input);
		Block_1[0].Sect = Sect_Remainder;
		break;
	case 2:
		strcpy(Block_2[Sect_Remainder].Text, Input);
		Block_2[0].Sect = Sect_Remainder;
		break;
	case 3:
		strcpy(Block_3[Sect_Remainder].Text, Input);
		Block_3[0].Sect = Sect_Remainder;
		break;
	}
}

void Flash_Read() {
	char scan[30];

	switch (Sect_Share) {
	case 0://-------------------------------------------------
		fp = fopen("Block_0.txt", "rt");
		if (fscanf(fp, "%s", scan) > 0) { //fscanf 에 정수값이 반환될경우 choice값 선택
			printf("해당블록에 이미 데이터가 존재합니다 \n");
			printf("삭제하고 계속하시겠습니까? \n");
			printf("1. 예 \n2. 아니오 \n");
			printf("입력:");
			scanf("%d", &choice);
		}
		else if (fscanf(fp, "%s", scan) < 0) { //반환되지 않을경우 choice에 0을 대입
			choice = 0;
		}
		fclose(fp);
		break;
	case 1://-------------------------------------------------
		fp = fopen("Block_1.txt", "rt");
		if (fscanf(fp, "%s", scan) > 0) {
			printf("해당블록에 이미 데이터가 존재합니다 \n");
			printf("삭제하고 계속하시겠습니까? \n");
			printf("1. 예 \n2. 아니오 \n");
			printf("입력:");
			scanf("%d", &choice);
		}
		else if (fscanf(fp, "%s", scan) < 0) {
			choice = 0;
		}
		fclose(fp);
		break;
	case 2://-------------------------------------------------
		fp = fopen("Block_2.txt", "rt");
		if (fscanf(fp, "%s", scan) > 0) {
			printf("해당블록에 이미 데이터가 존재합니다 \n");
			printf("삭제하고 계속하시겠습니까? \n");
			printf("1. 예 \n2. 아니오 \n");
			printf("입력:");
			scanf("%d", &choice);
		}
		else if (fscanf(fp, "%s", scan) < 0) {
			choice = 0;
		}
		fclose(fp);
		break;
	case 3://-------------------------------------------------
		fp = fopen("Block_3.txt", "rt");
		if (fscanf(fp, "%s", scan) > 0) {
			printf("해당블록에 이미 데이터가 존재합니다 \n");
			printf("삭제하고 계속하시겠습니까? \n");
			printf("1. 예 \n2. 아니오 \n");
			printf("입력:");
			scanf("%d", &choice);
		}
		else if (fscanf(fp, "%s", scan) < 0) {
			choice = 0;
		}
		fclose(fp);
	default:
		break;
	}
}

void Flash_Erase() {
	switch (Sect_Share) {
	case 0:
		for (int i = 0; i < SECT; i++) {
			strcpy(Block_0[i].Text, ""); //구조체에 ""를 복사
		}
		break;
	case 1:
		for (int i = 0; i < SECT; i++) {
			strcpy(Block_1[i].Text, "");
		}
		break;
	case 2:
		for (int i = 0; i < SECT; i++) {
			strcpy(Block_2[i].Text, "");
		}
	case 3:
		for (int i = 0; i < SECT; i++) {
			strcpy(Block_3[i].Text, "");
		}
		break;
	}
	Data_Save();
}

void Init() {
	//동적할당 실패...
}

void Data_Load() {
	char line[30];
	int num;
	//--------------------------------------
	fp = fopen("Sect_0.txt", "a+");
	fscanf(fp, "%d", &num); //Sect.txt 파일에 저장된 문자를 정수값으로 num에저장
	fclose(fp);
	fp = fopen("Block_0.txt", "a+");
	for (int i = 0; i < num + 1; i++) { //num만큼 반복
		fgets(line, SECT, fp); //문자열을 한줄씩 입력받아
		strcpy(Block_0[i].Text, line); //구조체에 저장
	}
	fclose(fp);
	//--------------------------------------
	fp = fopen("Sect_1.txt", "a+");
	fscanf(fp, "%d", &num);
	fclose(fp);
	fp = fopen("Block_1.txt", "a+");
	for (int i = 0; i < num + 1; i++) {
		fgets(line, SECT, fp);
		strcpy(Block_1[i].Text, line);
	}
	fclose(fp);
	//--------------------------------------
	fp = fopen("Sect_2.txt", "a+");
	fscanf(fp, "%d", &num);
	fclose(fp);
	fp = fopen("Block_2.txt", "a+");
	for (int i = 0; i < num + 1; i++) {
		fgets(line, SECT, fp);
		strcpy(Block_2[i].Text, line);
	}
	fclose(fp);
	//--------------------------------------
	fp = fopen("Sect_3.txt", "a+");
	fscanf(fp, "%d", &num);
	fclose(fp);
	fp = fopen("Block_3.txt", "a+");
	for (int i = 0; i < num + 1; i++) {
		fgets(line, SECT, fp);
		strcpy(Block_3[i].Text, line);
	}
	fclose(fp);
}

void Data_Save() {
	//--------------------------------------
	fp = fopen("Block_0.txt", "wt");
	for (int i = 0; i < SECT; i++) {
		fprintf(fp, "%s\n", Block_0[i].Text); //구조체 내용 텍스트에 저장
	}
	fclose(fp);
	fp = fopen("Sect_0.txt", "wt");
	fprintf(fp, "%d", Block_0[0].Sect); //Data_Load() 함수를 위해 섹터값 저장
	fclose(fp);
	//--------------------------------------
	fp = fopen("Block_1.txt", "wt");
	for (int i = 0; i < SECT; i++) {
		fprintf(fp, "%s\n", Block_1[i].Text);
	}
	fclose(fp);
	fp = fopen("Sect_1.txt", "wt");
	fprintf(fp, "%d", Block_1[0].Sect);
	fclose(fp);
	//--------------------------------------
	fp = fopen("Block_2.txt", "wt");
	for (int i = 0; i < SECT; i++) {
		fprintf(fp, "%s\n", Block_2[i].Text);
	}
	fclose(fp);
	fp = fopen("Sect_2.txt", "wt");
	fprintf(fp, "%d", Block_2[0].Sect);
	fclose(fp);
	//--------------------------------------
	fp = fopen("Block_3.txt", "wt");
	for (int i = 0; i < SECT; i++) {
		fprintf(fp, "%s\n", Block_3[i].Text);
	}
	fclose(fp);
	fp = fopen("Sect_3.txt", "wt");
	fprintf(fp, "%d", Block_3[0].Sect);
	fclose(fp);
}
```


### header

```c
#define _CRT_SECURE_NO_WARNINGS

#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define BLOCK 4
#define SECT 32

typedef struct {
	char Text[30];
	int Sect;
}BLOCKGUJO;
BLOCKGUJO Block_0[SECT];
BLOCKGUJO Block_1[SECT];
BLOCKGUJO Block_2[SECT];
BLOCKGUJO Block_3[SECT];

void FTL_Write();
void FTL_Read();
void Flash_Write();
void Flash_Read();
void Flash_Erase();
void Init();

void Data_Load();
void Data_Save();
```