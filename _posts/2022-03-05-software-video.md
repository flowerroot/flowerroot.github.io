---
layout: single
title: "VideoRent Program"
categories: C
tag: [C, VideoRent]
toc: true
---

2019년도 2학기 소프트웨어공학 수업 과제물 **비디오 대여 프로그램**입니다.

### main

```c
/*
* 파일이름: 비디오대여 프로그램
* 작 성 자: 김영진
* 작 성 일: 2019-10-27
* 목    적: 소프트웨어 공학 2차 프로젝트
*/

#include "header.h" //헤더파일 호출
#include <stdio.h>

int select; //메인화면에서 switch문을 이용하기위해 int형 변수 select 선언

int main(void) {
	DataLoad(); //프로그램 시작 시 텍스트파일에 저장되어있는 데이터를 불러온다.
	while (select != 5) { //5가 아닐때 반복시키는 조건으로 while문 생성
		printf("------------------------------------------------------ \n");
		printf("비디오대여 프로그램입니다. \n");
		printf("무엇을 하시겠습니까? \n");
		printf("1. 대여 \n2. 반납 \n3. 회원가입 \n4. 관리자모드 \n5. 종료 \n");
		printf("입력:");
		scanf_s("%d", &select); //입력받은 숫자를 select에 저장
		switch (select) {
		case 1:
			Rental(); //1번 입력 시 Rental 함수 활성화
			break;
		case 2:
			Return(); //2번 입력 시 Return 함수 활성화
			break;
		case 3:
			SignUp(); //3번 입력 시 SignUp 함수 활성화
			break;
		case 4:
			Administrator(); //4번 입력 시 Administrator 함수 활성화
			break;
		case 5:
			printf("프로그램을 종료합니다. \n"); //5번 입력 시 반복문 종료 및 프로그램 종료
			break;
		default:
			printf("다시 입력해주세요. \n"); //엉뚱한거 입력시 "다시 입력해주세요" 노출
		}
	}
}
```


### header

```c
#pragma once
#define _CRT_SECURE_NO_WARNINGS //scanf 및 fopen 컴파일에러 방지

#include <stdio.h> //입출력 함수 사용 위해서 호출
#include <string.h> //string 관련 함수 사용 위해서 호출
#include <stdlib.h> //atoi  사용을 위해 호출

typedef struct { //회원정보를 저장하기위한 구조체
	char name[10]; //회원이름
	char number[15]; //회원 연락처
}USER;

typedef struct { //비디오정보를 저장하기위한 구조체
	char video[20]; //비디오이름
	int state; //대여상태
}VIDEO;

typedef struct { //대여현황을 저장하기위한 구조체
	char u_name[10]; //대여한 회원이름
	char v_name[20]; //대여한 비디오
}RENT;

void Rental(); //대여기능 함수
void Return(); //반납기능 함수
void SignUp(); //회원가입기능 함수
void Administrator(); //관리자모드기능 함수

void Rental_videolist(); //대여_비디오 목록보기 기능 함수
void Rental_videosearch(); //대여_비디오 검색하기 기능 함수

void Administrator_member(); //관리자모드_회원관리 기능 함수

void DataLoad(); //프로그램 시작 시 데이터를 받아오는 기능 함수
void NewDataSave(); //삭제 또는 수정 기능 후 새로운 데이터를 저장시키는 함수
```


### function

```c
#include "header.h"	//헤더파일 호출
#include <stdio.h>

USER user[100]; //구조체 USER를 user라는 이름으로 100만큼의 저장공간을 할당한다.
VIDEO video[100]; //구조체 VIDEO를 video라는 이름으로 100만큼의 저장공간을 할당한다.
RENT rent[100]; //구조체RENT를 rent라는 이름으로 100만큼의 저장공간을 할당한다.

int user_m = 0; //회원정보 저장 시 카운팅하는 용도
int video_m = 0; //비디오정보 저장 시 카운팅하는 용도
int rent_m = 0; //대여내역 저장 시 카운팅하는 용도

int select_f; //현재 cpp화면에서 switch문을 이용하기 위해 선언됨
char search[20]; //비디오 검색기능에서 사용됨
int choice; //목록보기에서 선택하는 용도로 사용됨
char Rname[10]; //대여 및 반납 시 일치하는 정보를 비교하는용도로 사용됨
int password; //관리자모드 접근 시 입력된 비밀번호를 저장하는 용도로 사용됨
int state; //비디오의 대여상태와 비교하기위해 사용됨

FILE* fpp; //FILE형 pointer함수 fpp를 호출, fopen or fclose 등 파일을 열고 닫을 때 사용됨

void Rental() { //비디오 대여기능, 코드가 길어져서 목록보기와 검색기능을 따로 함수로 뺐다.
	printf("비디오대여를 진행합니다. \n");
	printf("1. 비디오목록 \n2. 비디오검색 \n");
	printf("입력:");
	scanf("%d", &select_f);
	switch (select_f) {
	case 1:
		Rental_videolist(); //1번 입력 시 Rental_videolist 함수가 활성화된다.
		break;
	case 2:
		Rental_videosearch(); //2번 입력 시 Rental_videosearch 함수가 활성화된다.
		break;
	default:
		printf("다시 입력해주세요. \n"); //엉뚱한 값 입력 시 다시 입력해주세요가 출력된다.
		return;
	}
}

void Rental_videolist() { //비디오 목록보기
	printf("대여 가능한 비디오 목록을 노출합니다. \n");
	for (int i = 0, list = 1; i < video_m; i++, list++) { //i 가 video_m 의 값만큼 반복하여 증가한다
		if (video[i].state == 0) {	//이때 video 구조체중 state값이 0인 것 만 출력시킨다.
			printf("%d. \"%s\" state %d\n", list, video[i].video, video[i].state);  //변수 list를 사용해 비디오번호를 출력시킨다.
		}
	}
	printf("비디오의 번호를 입력해주세요. \n");
	printf("입력:");
	scanf("%d", &choice); //list로 표현된 비디오번호를 보고 사용자가 선택을한다. 이 때 choice에 저장된다.
	printf("\"%s\"가 선택되었습니다. \n", video[choice - 1].video); //list는 1부터 증가하고 i는 0부터 증가하기 때문에
	printf("\"%s\"를 대여하시겠습니까? \n", video[choice - 1].video);//list를 보고 선택한 choice에는 -1을 해줘야 선택한 video와 일치한다.
	printf("1. 예 \n2. 아니오. \n");
	printf("입력:");
	scanf("%d", &state); //대여할지말지 1번 2번을 선택한 값이 state에 저장된다.

	if (state == video[choice - 1].state) { //1번을 입력했는데 이미 비디오의 상태가 대여중이라면
		printf("이미 대여중인 비디오입니다. \n"); //이미 대여중인 비디오라고 표시된다.
		return;
	}
	if (state == 2) { //2번을 입력하면 취소된다.
		printf("취소되었습니다. \n");
		return;
	}
	if (state != video[choice - 1].state) { //1번을 입력했고 비디오가 대여중이 아니라면 대여를 진행하게된다.
		printf("비디오대여를 위해 회원정보를 확인합니다. \n");
		printf("이름:"); //대여를 위해 회원정보를 확인한다.
		scanf("%s", Rname); //입력한 이름을 Rname에 저장한다.
		for (int i = 0; i < user_m; i++) { //i 가 user_m값 만큼 1씩 증가를 반복한다.
			if (strcmp(Rname, user[i].name) == 0) { //0번째 부터의 데이터와 차례대로 비교한 뒤 일치하는 데이터가 존재할 시
				printf("\"1\"번을 누르시면 완료됩니다. \n");
				printf("입력:");
				scanf("%d", &video[choice - 1].state); //비디오의 state값에 1을 입력하며 마무리된다.
				printf("\"%s\"님감사합니다. \"%s\"를 대여했습니다. \n", user[i].name, video[choice - 1].video);
				fpp = fopen("rent.txt", "at"); //rent.txt.파일을 at 권한으로 연다.
				fprintf(fpp, "%s:%s\n", user[i].name, video[choice - 1].video); //txt파일에 대여내역을 이어 작성한다.
				strcpy(rent[rent_m].u_name, user[i].name); //대여내역을 구조체에 복사한다.
				strcpy(rent[rent_m].v_name, video[choice - 1].video); //대여내역을 구조체에 복사한다.
				rent_m++; //rent_m 값을 1 증가시킨다.
				fclose(fpp); //rent.txt.파일을 닫는다.

				NewDataSave(); //회원정보,고객정보,대여현황정보를 최신화시키는 기능함수이다.
			}
		}
		return;
	}
}

void Rental_videosearch() { //비디오 검색하기
	printf("비디오검색을 진행합니다. \n");
	printf("검색:");
	scanf("%s", search); //입력받은 값을 search에 저장
	for (int i = 0; i < video_m; i++) { //i 를 video_m 값만큼 1씩 증가를 반복
		if (strcmp(search, video[i].video) == 0) { //search의 값과 video구조체 중 video의 값과 일치하는 데이터가 나올 시
			printf("\"%s\"에 대한 검색결과입니다. \n", video[i].video);
			printf("\"%s\" state %d \n", video[i].video, video[i].state); //검색결과 도출
			printf("\"0\"은 대여가능 \"1\"은 다른사람이 대여중인 비디오입니다. \n");
			printf("\"%s\"를 대여하시겠습니까? \n", video[i].video);
			printf("1. 예\n2. 아니오 \n");
			printf("입력:");
			scanf("%d", &state); //1 or 2를 state에 저장

			if (state == video[i].state) { //1번 입력 시 비디오가 대여중이라면 대여불가
				printf("다른 회원이 이미 이용중인 비디오입니다. \n");
				return;
			}
			if (state == 2) { //2번 입력 시 취소
				printf("취소되었습니다. \n");
				return;
			}
			if (state != video[i].state) { //1번 입력, 대여가능한 상태면 대여진행
				printf("비디오대여를 위해 회원정보를 확인합니다. \n");
				printf("이름:");
				scanf("%s", Rname); //이름을 입력받고
				for (int q = 0; q < user_m; q++) {
					if (strcmp(Rname, user[q].name) == 0) { //일치하는 회원정보가 존재할 시
						printf("\"1\"번을 누르시면 완료됩니다. \n");
						printf("입력:");
						scanf("%d", &video[i].state); //비디오의 state값에 1을 입력하며 완료된다.
						printf("\"%s\"님감사합니다. \"%s\"를 대여했습니다. \n", user[q].name, video[i].video);
						fpp = fopen("rent.txt", "at"); //rent.txt.파일을 at권한으로 열고
						fprintf(fpp, "%s:%s", user[q].name, video[i].video); //새로운 대여내역을 txt파일에 작성
						strcpy(rent[rent_m].u_name, user[q].name); //새로운 대여내역을 구조체에 복사
						strcpy(rent[rent_m].v_name, video[i].video); //새로운 대여내역을 구조체에 복사
						rent_m++; //rent_m 값 1 증가
						fclose(fpp); //파일을 닫는다.
						NewDataSave(); //새로운 데이터 최신화
					}
				}
				return;
			}
			return;
		}
		else { //비디오 검색하고 검색결과가 없다면 else로 오고 return 된다.
			printf("검색결과가 없습니다. \n");
			return;
		}
	}
}

void Return() { //비디오 반납하기
	printf("비디오반납을 진행합니다. \n");
	printf("반납할 비디오를 입력해주세요. \n");
	printf("입력:");
	scanf("%s", Rname); //반납할 비디오를 입력, Rname에 저장
	for (int i = 0; i < rent_m; i++) {
		if (strcmp(Rname, rent[i].v_name) == 0) { //대여중인 비디오와 일치하는 정보가 있는지 확인
			printf("%s", rent[i].v_name); //일치할 시 반납을 진행하고
			printf("\"0\"번을 입력해 반납을 완료합니다. \n");
			printf("입력:");
			scanf("%d", &video[i].state); //video의 state값에 0을 입력하며 완료
			printf("반납이 완료되었습니다. 이용해주셔서 감사합니다. \n");
			for (int a = i; a < rent_m; a++) //rent a에 i 값을 삽입하고
				rent[a] = rent[a + 1]; //a값에 a+1 값을 덮어쓰기, 이를 rent_m 값만큼 반복한다.
			rent_m--; //그래서 한칸씩 앞으로 당기며 덮어쓰기되며 마지막 rent_m값은 NULL값이 될것이기에 -1 해준다.
			NewDataSave(); //새로운 데이터를 최신화
			return;
		}
	}
}

void SignUp() { //회원가입

	fpp = fopen("user.txt", "at"); //user.txt 파일을 at 권한으로 연다.

	printf("회원가입을 진행합니다. \n");
	printf("이름:");
	scanf("%s", user[user_m].name); //입력된 이름을 user 구조체 name에 저장
	printf("연락처:");
	scanf("%s", user[user_m].number); //입력된 연락처를 user 구조체 number에 저장
	printf("%s:%s 회원가입을 축하합니다. \n", user[user_m].name, user[user_m].number);

	fprintf(fpp, "%s:%s\n", user[user_m].name, user[user_m].number); //금방 입력받은 값들을 txt파일에 프린트
	fclose(fpp); //txt파일 닫는다.
	user_m++; //user_m 값 1 증가
}

void Administrator() { //관리자모드
	printf("관리자모드를 진행합니다. \n");
	printf("비밀번호:");
	scanf("%d", &password);
	if (password == 123) { //입력받은 비밀번호가 123과 일치해야 입장가능.
		printf("1. 회원관리 \n2. 비디오입고 \n3. 대여현황 \n");
		printf("입력:");
		scanf("%d", &select_f);
		switch (select_f) {
		case 1: //1. 회원관리를 선택했다면 Administrator_member 함수가 활성화된다.
			Administrator_member(); //이건 코드가 너무 길어서 함수로 따로 뺐다.
			break;
		case 2: //비디오입고를 선택할 시 아래 코드가 진행된다.
			fpp = fopen("video.txt", "at"); //video.txt 파일을 at 권한으로 개방

			printf("비디오입고를 진행합니다. \n");
			printf("비디오명:");
			scanf("%s", video[video_m].video); //입력받은 비디오명을 video 구조체 video에 저장
			printf("\"0\"을 입력하여 완료해주세요. \n");
			printf("입력:");
			scanf("%d", &video[video_m].state); //0을 입력해 state값에 0을 넣으며 마무리
			fprintf(fpp, "%s:%d\n", video[video_m].video, video[video_m].state); //방금 추가된 내역을 txt파일에 프린트
			fclose(fpp); //txt파일 닫기
			printf("\"%s\"가 추가되었습니다. \n", video[video_m].video);
			video_m++; //video_m 값 1 증가
			break;
		case 3: //대여현황
			printf("비디오 대여현황입니다. \n");
			for (int i = 0; i < rent_m; i++) {  //대여정보를 저장하는 rent구조체의 정보를 모두 출력시킨다.
				printf("%s:%s\n", rent[i].u_name, rent[i].v_name);
			}
		}
	}
	if (password != 123) { //비밀번호가 일치하지 않을경우return
		printf("비밀번호가 일치하지 않습니다. \n");
		return;
	}
}



void Administrator_member() { //관리자모드 -> 회원관리
	printf("회원관리를 진행합니다. \n");
	printf("1. 회원목록 \n2. 회원검색 \n");
	printf("입력:");
	scanf("%d", &select_f);
	switch (select_f) {
	case 1: //회원목록
		printf("전체회원의 목록을 노출합니다. \n");
		for (int i = 0, list = 1; i < user_m; i++, list++) //i를 user_m만큼 1씩 증가를 반복 (list는 번호나열용도)
			printf("%d. %s:%s\n", list, user[i].name, user[i].number); //user구조체에 저장되있는 정보를 모두 출력
		printf("회원번호를 입력해주세요, 삭제/수정을 진행합니다. \n되돌아가려면 \"0\"을 입력해주세요. \n");
		printf("입력:");
		scanf("%d", &choice); //list로 보여지는 숫자를보고 choice에 입력
		if (choice != 0) { //0이 취소니까 0이 아닐 시 삭제/수정을 진행
			printf("\"%s\"님이 선택되었습니다, 무엇을 하시겠습니까? \n", user[choice - 1].name);
			printf("1. 삭제 \n2. 수정 \n"); //list=1, i=0 부터 증가를 시작했기 때문에 list를 보고 선택한 choice에는 -1을 붙여줘야 i값과 일치
			scanf("%d", &select_f);
			switch (select_f) {
			case 1: //삭제하기
				printf("\"%s\"님을 삭제하였습니다. \n", user[choice - 1].name);
				for (int i = choice - 1; i < user_m; i++) { //i 값에 choice-1값을 입력 그 상태에서 user_m만큼 i++ 를 반복
					user[i] = user[i + 1]; //i = i+1 을 함으로써 한칸씩 당겨서 덮어쓰기
				}
				user_m--; //맨 뒤는 NULL값이 될 것이기에 --;
				NewDataSave(); //새로운 데이터를 최신화
				break;
			case 2: //수정하기
				printf("회원수정을 진행합니다. \n");
				printf("이름:");
				scanf("%s", user[choice - 1].name); //수정사항을 선택한 정보에 덮어쓰기
				printf("연락처:");
				scanf("%s", user[choice - 1].number); //수정사항을 선택한 정보에 덮어쓰기
				printf("%s:%s로 수정이 완료되었습니다. \n", user[choice - 1].name, user[choice - 1].number);
				NewDataSave(); //새 데이터를 최신화
				break;
			}
		}
		if (choice == 0) { //되돌아가기 0번 입력 시 return
			return;
		}
		break;
	case 2: //회원검색
		printf("회원검색을 진행합니다. \n");
		printf("검색:");
		scanf("%s", search); //검색입력값을 search에 저장
		for (int i = 0; i < user_m; i++) { //i를 user_m만큼 1씩 증가 반복
			if (strcmp(search, user[i].name) == 0) { //search값과 user 구조체의 name 에서 일치하는 데이터가 있을 시 아래로 진행
				printf("\"%s\"에 대한 검색결과입니다. \n", user[i].name);
				printf("%s:%s 무엇을 하시겠습니까? \n", user[i].name, user[i].number);
				printf("1. 삭제 \n2. 수정 \n");
				printf("입력:");
				scanf("%d", &select_f);
				switch (select_f) {
				case 1: //회원삭제
					printf("\"%s\"님을 삭제하였습니다. \n", user[i].name);
					for (int u = i; u < user_m; u++) { //u 에 i값을 대입
						user[u] = user[u + 1]; //u 에 u + 1 값을 대입 이것을 user_m만큼 반복
					}							//한칸씩 당기며 덮어쓰기되는 효과
					user_m--; //마지막 값은 NULL값이 될 것 이기에 --;
					NewDataSave(); //새로운 데이터를 최신화
					break;
				case 2: //회원수정
					printf("회원수정을 진행합니다. \n");
					printf("이름:");
					scanf("%s", user[i].name); //입력받은 값을 덮어쓰기
					printf("연락처:");
					scanf("%s", user[i].number); //입력받은 값을 덮어쓰기
					printf("%s:%s로 수정이 완료되었습니다. \n", user[i].name, user[i].number);
					NewDataSave(); //새로운 데이터를 최신화
					break;
				}
				break;
			}
			else { //search 값과 user구조체의 name에서 일치하는 데이터가 없을 시 아래결과를 프린트
				printf("검색결과가 없습니다. \n");
				break;
			}
		}
		break;
	}
}

void DataLoad() {
	fpp = fopen("user.txt", "rt"); //user.txt 파일을 rt  권한으로 오픈
	if (fpp == NULL) { //만약 없을 시
		fpp = fopen("user.txt", "at"); //생성해서 열고
		fclose(fpp); //닫는다
	}
	char line[100]; //txt파일에 입력된 데이터를 한 줄 씩 구분해서 읽어오는 용도
	char* ptr; //캐릭터형 포인터변수 ptr 선언
	int word_cnt; //해당 줄에 있는 데이터를 구분해서 구조체에 저장하는 용도

	while (fscanf(fpp, "%s", line) > 0) { //txt파일에 있는 문자를 line에 담는다 이 때 스캔된 문자가 있으면 fscanf에 정수값이 반환되므로
		word_cnt = 0;						//0보다 클때=받아온문자가 있을때까지 while문을 반복
		ptr = strtok(line, ":"); //line에 저장된 문자를 클론 기준으로 잘라서 ptr에 저장
		while (ptr != NULL) {  //ptr에 문자를 저장할 때 갯수만큼 정수형태로 반환됨 NULL값이 아닐 때 반복
			word_cnt++; //반복하는 횟수만큼 word_cnt 가 증가됨
			switch (word_cnt) { //첫번째 반복시 case1 두번째 반복시 case2로 이동
			case 1:
				strcpy(user[user_m].name, ptr); //ptr값을 user구조체의 name에 저장
				break;
			case 2:
				strcpy(user[user_m].number, ptr); //ptr값을 user 구조체의 number에 저장
				break;
			}
			ptr = strtok(NULL, ":"); // 클론을 기준으로 ptr에 NULL값을 반환
		}
		user_m++; //문자열카피가 끝나면 user_m 를 1 증가
	}
	fclose(fpp); //파일을 닫는다

	//여기서부터는 주석의 내용이 모두 유사하기때문에 특이점이 없을 시 생략한다.
	fpp = fopen("video.txt", "rt");
	if (fpp == NULL) {
		fpp = fopen("video.txt", "at");
		fclose(fpp);
	}
	while (fscanf(fpp, "%s", line) > 0) {
		word_cnt = 0;
		ptr = strtok(line, ":");
		while (ptr != NULL) {
			word_cnt++;
			switch (word_cnt) {
			case 1:
				strcpy(video[video_m].video, ptr);
				break;
			case 2:
				video[video_m].state = atoi(ptr); //ptr에 저장된 주소값에서 문자열을 정수형태로 반환 후 video구조체의 state에 대입
				break;
			}
			ptr = strtok(NULL, ":");
		}
		video_m++;
	}
	fclose(fpp);

	fpp = fopen("rent.txt", "rt");
	if (fpp == NULL) {
		fpp = fopen("rent.txt", "at");
		fclose(fpp);
	}
	while (fscanf(fpp, "%s", line) > 0) {
		word_cnt = 0;
		ptr = strtok(line, ":");
		while (ptr != NULL) {
			word_cnt++;
			switch (word_cnt) {
			case 1:
				strcpy(rent[rent_m].u_name, ptr);
				break;
			case 2:
				strcpy(rent[rent_m].v_name, ptr);
				break;
			}
			ptr = strtok(NULL, ":");
		}
		rent_m++;
	}
	fclose(fpp);
}

void NewDataSave() { //새로운 데이터를 최신화한다.
	fpp = fopen("user.txt", "wt"); //user.txt.파일을 wt 권한으로 연다.
	for (int i = 0; i < user_m; i++) { //i 를 user_m 만큼 1씩 증가 반복
		fprintf(fpp, "%s:%s\n", user[i].name, user[i].number); //txt파일에 구조체에 저장된 내역을 모두 프린팅한다.
	}
	fclose(fpp); //파일을 닫는다.

	//이하동문
	fpp = fopen("video.txt", "wt");
	for (int i = 0; i < video_m; i++) {
		fprintf(fpp, "%s:%d\n", video[i].video, video[i].state);
	}
	fclose(fpp);

	fpp = fopen("rent.txt", "wt");
	for (int i = 0; i < rent_m; i++) {
		fprintf(fpp, "%s:%s\n", rent[i].u_name, rent[i].v_name);
	}
	fclose(fpp);
}
```