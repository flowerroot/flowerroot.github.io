---
layout: single
title: "[C++] Radiant ResultData Merge."
categories: Cpp
tag: [Cpp]
toc: true
toc_sticky: true
---

### txt 파일들을 하나의 csv로 merge해주는 매크로 프로그램
비슷한 이름을 가진 여러개의 txt 파일을 하나의 csv 파일로 모을 일이 생겼다. 

워낙 수량도 많고 당분간 자주 반복해야할 업무라서 매크로 프로그램을 하나 작성해서 업무를 진행했다.


```c++
/*
제    목: Radiant ResultData 합치기
기    능: Rediant ResultData(.txt) 를 csv파일 하나에 다 모아줌
파일이름: Radiant ResultData Merge
수정날짜: 2023.11.20
작 성 자: 김영진
*/

#define _CRT_SECURE_NO_WARNINGS

//#define PanelSizeX 5120 // X2085 (26.9")
//#define PanelSizeY 2880

#define PanelSizeX 4480 // X2479 And X2854 (23.5")
#define PanelSizeY 2520

#define ImageX 10640

#include <iostream>
#include <ctime>

#include <stdio.h>
#include <windows.h>
#include <string.h>

using namespace std;

typedef struct {
	int nNO;
	char cJUDGE[10];
	int nCAM_NO;
	char cDET_PTN[10];
	char cNG_NAME[30];
	float fFOCUS_SCORE;
	int nIMAGE_X;
	int nIMAGE_Y;
	int nPNL_X;
	int nPNL_Y;
	int nStep;
	char cSection[20];
	float fAve_Luminance;
	float fMax_Luminance;
	float fAspect_Ratio;
	float fCR;
	float fMaxCR;
	float fValue;
	float fPixel;
	float fShapes;
	float fLength;
	float fWidth;
	float fHeight;
}MyStruct;

int main(void) {

	int GridSizeX = PanelSizeX / 8;
	int GridSizeY = PanelSizeY / 6;

	// CSV 파일 검색경로 설정.
	MyStruct CSVInfo = { };
	// 경로를 절대참조 하는 방법.
	//const wchar_t* inputDirectory = L"D:\\07. AMI Huizhou\\0. Radiant ResultData Merge\\Project1\\";
	WIN32_FIND_DATA findFileData;
	HANDLE hFind = FindFirstFile((/*inputDirectory + */std::wstring(L"*.txt")).c_str(), &findFileData);

	// Log용 변수
	int PanelCount = 0;

	// 현재시각 추출
	time_t timer = time(NULL);
	struct tm* t = localtime(&timer);

	// 파일명에 현재시각 기입
	char fileName[35] = "";
	int nYear = t->tm_year + 1900; // 2023년에서
	nYear %= 100; // 앞에 두개떼고 23만 남기기
	snprintf(fileName, sizeof(fileName), "(%02d%02d%02d)_Radiant_RESULT_DATA.csv", nYear, t->tm_mon + 1, t->tm_mday);

	// 저장할 파일생성 및 Open
	FILE* ffpWrite = fopen(fileName, "w");

	// Column 항목 기입.
	::fprintf(ffpWrite, "S/N,GRID,NO,JUDGE,CAM_NO,DET_PTN,NG_NAME,FOCUS_SCORE,IMAGE_X,IMAGE_X(DspApp),IMAGE_Y,PNL_X,PNL_Y,Step,Section,Ave_Luminance,Max_Luminence,Aspect_Ratio,CR,MaxCR,Value,Pixel,Shapes,Length,Width,Height \n");

	do {
		if (!(findFileData.dwFileAttributes & FILE_ATTRIBUTE_DIRECTORY)) {
			wprintf(L"파일 이름: %s \n", findFileData.cFileName); // Log
			FILE* ffpRead = _wfopen(findFileData.cFileName, L"r"); // txt 파일 "r" 권한으로 open.

			// 예외처리
			if (ffpRead == NULL) {
				printf("txt파일을 여는데 실패했습니다. \n");
				return 0;
			}

			char line[10000] = { }; // 여기에 한줄씩 쭉 담고
			int word_cnt = 0;
			char* ptr = nullptr;
			while (fgets(line, sizeof(line), ffpRead) != NULL) {
				word_cnt = 0;
				ptr = strtok(line, ","); // 여기에 "," 기준으로 짤라서 구분하고
				while (ptr != NULL) { // 짜른거 다 쓸때까지 반복할건데
					switch (++word_cnt) { // 순서대로 구분해서 struct에 담는다.
					case 1:
						CSVInfo.nNO = atoi(ptr);
						break;
					case 2:
						strcpy(CSVInfo.cJUDGE, ptr);
						break;
					case 3:
						CSVInfo.nCAM_NO = atoi(ptr);
						break;
					case 4:
						strcpy(CSVInfo.cDET_PTN, ptr);
						break;
					case 5:
						strcpy(CSVInfo.cNG_NAME, ptr);
						break;
					case 6:
						CSVInfo.fFOCUS_SCORE = (float)atof(ptr);
						break;
					case 7:
						CSVInfo.nIMAGE_X = atoi(ptr);
						break;
					case 8:
						CSVInfo.nIMAGE_Y = atoi(ptr);
						break;
					case 9:
						CSVInfo.nPNL_X = atoi(ptr);
						break;
					case 10:
						CSVInfo.nPNL_Y = atoi(ptr);
						break;
					case 11:
						CSVInfo.nStep = atoi(ptr);
						break;
					case 12:
						strcpy(CSVInfo.cSection, ptr);
						break;
					case 13:
						CSVInfo.fAve_Luminance = (float)atof(ptr);
						break;
					case 14:
						CSVInfo.fMax_Luminance = (float)atof(ptr);
						break;
					case 15:
						CSVInfo.fAspect_Ratio = (float)atof(ptr);
						break;
					case 16:
						CSVInfo.fCR = (float)atof(ptr);
						break;
					case 17:
						CSVInfo.fMaxCR = (float)atof(ptr);
						break;
					case 18:
						CSVInfo.fValue = (float)atof(ptr);
						break;
					case 19:
						CSVInfo.fPixel = (float)atof(ptr);
						break;
					case 20:
						CSVInfo.fShapes = (float)atof(ptr);
						break;
					case 21:
						CSVInfo.fLength = (float)atof(ptr);
						break;
					case 22:
						CSVInfo.fWidth = (float)atof(ptr);
						break;
					case 23:
						CSVInfo.fHeight = (float)atof(ptr);
						break;
					default:
						break;
					}
					ptr = strtok(NULL, ",");
				}

				// 패널정보가 아닌 Column을 읽어왔을 때 예외처리
				if (CSVInfo.cJUDGE[0] == 'J') { // strcut judge의 0번째 배열이 'J' 라면
					continue; // Column 이므로 continue.
				}

				// Data, Gate 좌표에서 GRID 정보를 추출.
				char GridNum = ' ';
				char GridChar = ' ';
				// Grid 알파벳
				if (CSVInfo.nPNL_X < GridSizeX) {
					GridNum = '1';
				}
				else if (CSVInfo.nPNL_X < GridSizeX * 2) {
					GridNum = '2';
				}
				else if (CSVInfo.nPNL_X < GridSizeX * 3) {
					GridNum = '3';
				}
				else if (CSVInfo.nPNL_X < GridSizeX * 4) {
					GridNum = '4';
				}
				else if (CSVInfo.nPNL_X < GridSizeX * 5) {
					GridNum = '5';
				}
				else if (CSVInfo.nPNL_X < GridSizeX * 6) {
					GridNum = '6';
				}
				else if (CSVInfo.nPNL_X < GridSizeX * 7) {
					GridNum = '7';
				}
				else if (CSVInfo.nPNL_X < GridSizeX * 8) {
					GridNum = '8';
				}
				// Grid Number
				if (CSVInfo.nPNL_Y < GridSizeY) {
					GridChar = 'A';
				}
				else if (CSVInfo.nPNL_Y < GridSizeY * 2) {
					GridChar = 'B';
				}
				else if (CSVInfo.nPNL_Y < GridSizeY * 3) {
					GridChar = 'C';
				}
				else if (CSVInfo.nPNL_Y < GridSizeY * 4) {
					GridChar = 'D';
				}
				else if (CSVInfo.nPNL_Y < GridSizeY * 5) {
					GridChar = 'E';
				}
				else if (CSVInfo.nPNL_Y < GridSizeY * 6) {
					GridChar = 'F';
				}

				// Filename CSV에 기입할껀데 WCHAR 형태라서 char* 로 형변환.
				int size = WideCharToMultiByte(CP_ACP, 0, findFileData.cFileName, -1, NULL, 0, NULL, NULL);
				char* cpFileName = new char[size];
				WideCharToMultiByte(CP_ACP, 0, findFileData.cFileName, -1, cpFileName, size, NULL, NULL);
				char* cpFileName_DotDelete = strtok(cpFileName, "."); // .txt 자르기

				//  ImageX 값을 DspApp Viewer 값과 맞게 변경.
				int nDspAppViewerImageX = CSVInfo.nIMAGE_X;
				while (nDspAppViewerImageX > ImageX) { // Radiant 의 ImageX 값이 10640 보다 크다면
					nDspAppViewerImageX -= ImageX; // 10640 을 뺀다.
				}


				//Log
				::printf("PANEL ID: %s \n", cpFileName_DotDelete);

				::fprintf(ffpWrite, "%s,%c%c,%d,%s,%d,%s,%s,%f,%d,%d,%d,%d,%d,%d,%s,%f,%f,%f,%f,%f,%f,%f,%f,%f,%f,%f \n", //"%d,%s,%n,%s,%s,%f,%d,%d,%d,%d,%d,%s,%f,%f,%f,%f,%f,%f,%f,%f,%f,%f,%f,%s \n"
					cpFileName_DotDelete, // 1 [S/N]
					GridChar, // 2~ [GRID]
					GridNum, // ~~ [GRID]
					CSVInfo.nNO, // 3
					CSVInfo.cJUDGE, // 4
					CSVInfo.nCAM_NO, // 5
					CSVInfo.cDET_PTN, // 6
					CSVInfo.cNG_NAME, // 7
					CSVInfo.fFOCUS_SCORE, // 8
					CSVInfo.nIMAGE_X, // 9
					nDspAppViewerImageX, // 10 ImageX 보정값.
					CSVInfo.nIMAGE_Y, // 11
					CSVInfo.nPNL_X,  // 12
					CSVInfo.nPNL_Y, // 13
					CSVInfo.nStep, // 14
					CSVInfo.cSection, // 15
					CSVInfo.fAve_Luminance, // 16 
					CSVInfo.fMax_Luminance, // 17
					CSVInfo.fAspect_Ratio, // 18
					CSVInfo.fCR, // 19
					CSVInfo.fMaxCR, // 20 
					CSVInfo.fValue, // 21
					CSVInfo.fPixel, // 22
					CSVInfo.fShapes, // 23
					CSVInfo.fLength, // 24
					CSVInfo.fWidth, // 25
					CSVInfo.fHeight); // 26

				free(cpFileName);
			}

			::fclose(ffpRead);

			::printf("%d 번째 패널 완료 \n", ++PanelCount);

		}
	} while (FindNextFile(hFind, &findFileData) != 0);

	::fclose(ffpWrite);

	FindClose(hFind);
	::printf("완료");
	return 0;
}
```

끝.