---
layout: single
title: "[C++] 이미지 자동 생성기."
categories: OpenCV-C++
tag: [OpenCV, Cpp]
toc: true
toc_sticky: true
---
## [C++] OpenCV를 활용한 이미지 자동 생성기

디스플레이의 결함을 머신비전으로 검사하기 위해선  
일정한 패턴의 점등이 필요하다.  

보통은 Gray127, Red, Green, Blue, Black 등의 패턴을 띄워  
디스플레이에 결함은 없는지 검사를 진행하지만.  
이번엔 패턴이 임의로 결함을 발생시켜  
검사프로그램의 성능을 테스트해야 하는 상황이었다.  

따라서 결함시료를 제작해야 했는데..  
그림판으로 하나하나 그리기엔 너무나도 양이 많아서 프로그래밍으로  
만들어보고자 다음과 같은 프로그램을 개발했다.  

C++ 언어로 OpenCV를 활용하였고  
패턴별로 결함의 종류와 위치를 변경해가며 이미지를 생성한 뒤.  
저장까지 구현해보았다.  

소스코드는 다음과 같다.  

```C++
/*
제   목 : 어느 세월에 그림판으로 다 그리고 앉아있어~!~!
기   능 : Auto_Curved(국책과제) 촬상용 이미지 (결함시료) 자동으로 그려줌
파일이름 : HelloCV
수정날짜 : 2023-03-20
작 성 자 : 김영진
*/

#include <iostream>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/imgproc.hpp>
#include <direct.h>

using namespace std;
using namespace cv;

#define PanelSizeX 2940 // 캔버스 x축 px
#define PanelSizeY 816 // 캔버스 y축 px

enum {
	// Point Defect 127 수준별 밝기값 설정.
	PD_127_Bright_LEVEL_1 = 139,
	PD_127_Bright_LEVEL_2 = 152,
	PD_127_Bright_LEVEL_3 = 165,
	PD_127_Bright_LEVEL_4 = 177,
	PD_127_Bright_LEVEL_5 = 190,
	PD_127_Dark_LEVEL_1 = 115,
	PD_127_Dark_LEVEL_2 = 102,
	PD_127_Dark_LEVEL_3 = 89,
	PD_127_Dark_LEVEL_4 = 77,
	PD_127_Dark_LEVEL_5 = 64,

	// Point Defect BLACK 수준별 밝기값 설정.
	PD_BLACK_Bright_LEVEL_1 = 3,
	PD_BLACK_Bright_LEVEL_2 = 13,
	PD_BLACK_Bright_LEVEL_3 = 23,
	PD_BLACK_Bright_LEVEL_4 = 33,
	PD_BLACK_Bright_LEVEL_5 = 43,

	// Point Defect G32 수준별 밝기값 설정.
	PD_G32_Bright_LEVEL_1 = 35,
	PD_G32_Bright_LEVEL_2 = 38,
	PD_G32_Bright_LEVEL_3 = 41,
	PD_G32_Bright_LEVEL_4 = 44,
	PD_G32_Bright_LEVEL_5 = 48,
	PD_G32_Dark_LEVEL_1 = 29,
	PD_G32_Dark_LEVEL_2 = 26,
	PD_G32_Dark_LEVEL_3 = 23,
	PD_G32_Dark_LEVEL_4 = 20,
	PD_G32_Dark_LEVEL_5 = 16,

	// Point Defect G64 수준별 밝기값 설정.
	PD_G64_Bright_LEVEL_1 = 70,
	PD_G64_Bright_LEVEL_2 = 76,
	PD_G64_Bright_LEVEL_3 = 83,
	PD_G64_Bright_LEVEL_4 = 89,
	PD_G64_Bright_LEVEL_5 = 96,
	PD_G64_Dark_LEVEL_1 = 58,
	PD_G64_Dark_LEVEL_2 = 52,
	PD_G64_Dark_LEVEL_3 = 45,
	PD_G64_Dark_LEVEL_4 = 39,
	PD_G64_Dark_LEVEL_5 = 32,

	// Point Defect WHITE 수준별 밝기값 설정.
	PD_WHITE_Dark_LEVEL_1 = 230,
	PD_WHITE_Dark_LEVEL_2 = 204,
	PD_WHITE_Dark_LEVEL_3 = 179,
	PD_WHITE_Dark_LEVEL_4 = 153,
	PD_WHITE_Dark_LEVEL_5 = 128,

	// Line Defect 127 수준별 밝기값 설정.
	LD_127_Bright_LEVEL_1 = 130,
	LD_127_Bright_LEVEL_2 = 143,
	LD_127_Bright_LEVEL_3 = 156,
	LD_127_Bright_LEVEL_4 = 168,
	LD_127_Bright_LEVEL_5 = 181,
	LD_127_Dark_LEVEL_1 = 124,
	LD_127_Dark_LEVEL_2 = 111,
	LD_127_Dark_LEVEL_3 = 98,
	LD_127_Dark_LEVEL_4 = 86,
	LD_127_Dark_LEVEL_5 = 73,

	// Line Defect BLACK 수준별 밝기값 설정.
	LD_BLACK_Bright_LEVEL_1 = 1,
	LD_BLACK_Bright_LEVEL_2 = 3,
	LD_BLACK_Bright_LEVEL_3 = 7,
	LD_BLACK_Bright_LEVEL_4 = 10,
	LD_BLACK_Bright_LEVEL_5 = 13,

	// Line Defect G32 수준별 밝기값 설정.
	LD_G32_Bright_LEVEL_1 = 33,
	LD_G32_Bright_LEVEL_2 = 36,
	LD_G32_Bright_LEVEL_3 = 39,
	LD_G32_Bright_LEVEL_4 = 42,
	LD_G32_Bright_LEVEL_5 = 45,
	LD_G32_Dark_LEVEL_1 = 31,
	LD_G32_Dark_LEVEL_2 = 28,
	LD_G32_Dark_LEVEL_3 = 25,
	LD_G32_Dark_LEVEL_4 = 22,
	LD_G32_Dark_LEVEL_5 = 19,

	// Line Defect G64 수준별 밝기값 설정.
	LD_G64_Bright_LEVEL_1 = 65,
	LD_G64_Bright_LEVEL_2 = 72,
	LD_G64_Bright_LEVEL_3 = 78,
	LD_G64_Bright_LEVEL_4 = 85,
	LD_G64_Bright_LEVEL_5 = 91,
	LD_G64_Dark_LEVEL_1 = 63,
	LD_G64_Dark_LEVEL_2 = 56,
	LD_G64_Dark_LEVEL_3 = 50,
	LD_G64_Dark_LEVEL_4 = 43,
	LD_G64_Dark_LEVEL_5 = 37,

	// Line Defect WHITE 수준별 밝기값 설정.
	LD_WHITE_Dark_LEVEL_1 = 230,
	LD_WHITE_Dark_LEVEL_2 = 204,
	LD_WHITE_Dark_LEVEL_3 = 179,
	LD_WHITE_Dark_LEVEL_4 = 153,
	LD_WHITE_Dark_LEVEL_5 = 128,

	// Stain Defect 수준별 밝기값 설정은 밑에 따로있습니다.
};

int PD_127_Bright_LEVEL[] = { PD_127_Bright_LEVEL_1,
								PD_127_Bright_LEVEL_2,
								PD_127_Bright_LEVEL_3,
								PD_127_Bright_LEVEL_4,
								PD_127_Bright_LEVEL_5 };
int PD_127_Dark_LEVEL[] = { PD_127_Dark_LEVEL_1,
								PD_127_Dark_LEVEL_2,
								PD_127_Dark_LEVEL_3,
								PD_127_Dark_LEVEL_4,
								PD_127_Dark_LEVEL_5 };
int PD_BLACK_Bright_LEVEL[] = { PD_BLACK_Bright_LEVEL_1,
								PD_BLACK_Bright_LEVEL_2,
								PD_BLACK_Bright_LEVEL_3,
								PD_BLACK_Bright_LEVEL_4,
								PD_BLACK_Bright_LEVEL_5 };
int PD_G32_Bright_LEVEL[] = { PD_G32_Bright_LEVEL_1,
								PD_G32_Bright_LEVEL_2,
								PD_G32_Bright_LEVEL_3,
								PD_G32_Bright_LEVEL_4,
								PD_G32_Bright_LEVEL_5 };
int PD_G32_Dark_LEVEL[] = { PD_G32_Dark_LEVEL_1,
							PD_G32_Dark_LEVEL_2,
							PD_G32_Dark_LEVEL_3,
							PD_G32_Dark_LEVEL_4,
							PD_G32_Dark_LEVEL_5 };
int PD_G64_Bright_LEVEL[] = { PD_G64_Bright_LEVEL_1,
								PD_G64_Bright_LEVEL_2,
								PD_G64_Bright_LEVEL_3,
								PD_G64_Bright_LEVEL_4,
								PD_G64_Bright_LEVEL_5 };
int PD_G64_Dark_LEVEL[] = { PD_G64_Dark_LEVEL_1,
							PD_G64_Dark_LEVEL_2,
							PD_G64_Dark_LEVEL_3,
							PD_G64_Dark_LEVEL_4,
							PD_G64_Dark_LEVEL_5 };
int PD_WHITE_Dark_LEVEL[] = { PD_WHITE_Dark_LEVEL_1,
								PD_WHITE_Dark_LEVEL_2,
								PD_WHITE_Dark_LEVEL_3,
								PD_WHITE_Dark_LEVEL_4,
								PD_WHITE_Dark_LEVEL_5 };
int LD_127_Bright_LEVEL[] ={ LD_127_Bright_LEVEL_1,
								LD_127_Bright_LEVEL_2,
								LD_127_Bright_LEVEL_3,
								LD_127_Bright_LEVEL_4,
								LD_127_Bright_LEVEL_5 };
int LD_127_Dark_LEVEL[] = { LD_127_Dark_LEVEL_1,
								LD_127_Dark_LEVEL_2,
								LD_127_Dark_LEVEL_3,
								LD_127_Dark_LEVEL_4,
								LD_127_Dark_LEVEL_5 };
int LD_BLACK_Bright_LEVEL[] = { LD_BLACK_Bright_LEVEL_1,
								LD_BLACK_Bright_LEVEL_2,
								LD_BLACK_Bright_LEVEL_3,
								LD_BLACK_Bright_LEVEL_4,
								LD_BLACK_Bright_LEVEL_5 };
int LD_G32_Bright_LEVEL[] = { LD_G32_Bright_LEVEL_1,
								LD_G32_Bright_LEVEL_2,
								LD_G32_Bright_LEVEL_3,
								LD_G32_Bright_LEVEL_4,
								LD_G32_Bright_LEVEL_5 };
int LD_G32_Dark_LEVEL[] = { LD_G32_Dark_LEVEL_1,
							LD_G32_Dark_LEVEL_2,
							LD_G32_Dark_LEVEL_3,
							LD_G32_Dark_LEVEL_4,
							LD_G32_Dark_LEVEL_5 };
int LD_G64_Bright_LEVEL[] = { LD_G64_Bright_LEVEL_1,
								LD_G64_Bright_LEVEL_2,
								LD_G64_Bright_LEVEL_3,
								LD_G64_Bright_LEVEL_4,
								LD_G64_Bright_LEVEL_5 };
int LD_G64_Dark_LEVEL[] = { LD_G64_Dark_LEVEL_1,
							LD_G64_Dark_LEVEL_2,
							LD_G64_Dark_LEVEL_3,
							LD_G64_Dark_LEVEL_4,
							LD_G64_Dark_LEVEL_5 };
int LD_WHITE_Dark_LEVEL[] = { LD_WHITE_Dark_LEVEL_1,
								LD_WHITE_Dark_LEVEL_2,
								LD_WHITE_Dark_LEVEL_3,
								LD_WHITE_Dark_LEVEL_4,
								LD_WHITE_Dark_LEVEL_5 };

// Stain Defect 수준별 밝기값 설정. 1 2 3 4 5 수준
// ex)G127 1수준 3% = (127 * 0.03) / 30 = 0.127
// 30 은 Stain Drawing 시 for문 반복횟수.
// 흑백 얼룩불량 수준
float Stain_127_Bright_LEVEL[] = { 0.127, 0.55, 0.9736, 1.397, 1.82 };
float Stain_127_Dark_LEVEL[] = { -0.127, -0.55, -0.96, -1.397, -1.82 };
float Stain_BLACK_Bright_LEVEL[] = { 0.03, 0.13, 0.23, 0.33, 0.43 };
float Stain_G32_Bright_LEVEL[] = { 0.03, 0.13, 0.24, 0.35, 0.45 };
float Stain_G32_Dark_LEVEL[] = { -0.03, -0.13, -0.24, -0.35, -0.45 };
float Stain_G64_Bright_LEVEL[] = { 0.064, 0.277, 0.49, 0.704, 0.9173 };
float Stain_G64_Dark_LEVEL[] = { -0.064, -0.277, -0.49, -0.704, -0.9173 };
float Stain_WHITE_Dark_LEVEL[] = { -0.255, -1.105, -1.955, -2.805, -3.655 };
// 컬러 얼룩불량 수준
float Stain_Color_WHITE_Dark_LEVEL[] = { -0.255, -1.105, -1.955, -2.805, -3.655 };

void Canvas_Clear(Mat RED, Mat GREEN, Mat BLUE, Mat BLACK, Mat G32, Mat G64, Mat G127, Mat WHITE, Mat Color_WHITE_red, Mat Color_WHITE_green, Mat Color_WHITE_blue) {
	RED = Scalar(0, 0, 127); // Scalar 함수를 이용해 배경을 다시 덧칠한다.
	GREEN = Scalar(0, 127, 0);
	BLUE = Scalar(127, 0, 0);
	BLACK = Scalar(0, 0, 0);
	G32 = Scalar(32, 32, 32);
	G64 = Scalar(64, 64, 64);
	G127 = Scalar(127, 127, 127);
	WHITE = Scalar(255, 255, 255);

	Color_WHITE_red = Scalar(255, 255, 255);
	Color_WHITE_green = Scalar(255, 255, 255);
	Color_WHITE_blue = Scalar(255, 255, 255);
}
void Point_Stain_Defect_Position_Select(int* PosX, int* Dark_PosY, int* Bright_PosY, int* Interval, int* Position, bool* AutoRun, int*Cycle) {
	int select = 0;
	cout << "------------------------------------" << endl;
	cout << "결함의 위치를 선택해주세요. (1 ~ 8)" << endl;
	cout << "------------------------------------" << endl;
	cout << "1) 기본값" << endl
		<< "2) x축 간격 -100px & x축 시작위치 +300" << endl
		<< "3) x축 간격 -200px & x축 시작위치 +700" << endl
		<< "4) y축 간격 -200px" << endl
		<< "5) y축 간격 -200px & x축 간격 -100px & x축 시작위치 +300" << endl
		<< "6) y축 간격 -200px & x축 간격 -200px & x축 시작위치 +700" << endl
		<< "7) x축 시작위치 -100px" << endl
		<< "8) x축 시작위치 +100px" << endl;
	cout << "입력 >> ";
	if (*AutoRun == true) {
		select = *Cycle;
	}
	else {
		cin >> select;
	}
	cout << select << "번째 위치가 설정되었습니다." << endl;
	switch (select) {
	case 1: // 초기 설정값을 그대로 사용.
		*PosX = 300;
		*Dark_PosY = 200;
		*Bright_PosY = 600;
		*Interval = 350;
		*Position = 1;
		break;
	case 2: // 간격을 350 → 250 변경 & x축 시작위치 +300
		*PosX = 600;
		*Dark_PosY = 200;
		*Bright_PosY = 600;
		*Interval = 250;
		*Position = 2;
		break;
	case 3: // 간격을 350 → 150 변경 & x축 시작위치 +700
		*PosX = 1000;
		*Dark_PosY = 200;
		*Bright_PosY = 600;
		*Interval = 150;
		*Position = 3;
		break;
	case 4: // 간격을 그대로, 암점과 휘점의 Y축 거리를 200px만큼 더 가깝게
		*PosX = 300;
		*Dark_PosY = 300;
		*Bright_PosY = 500;
		*Interval = 350;
		*Position = 4;
		break;
	case 5: // 간격을 350 → 250 변경, 암점과 휘점의 Y축 거리를 200px만큼 더 가깝게 & x축 시작위치 +300
		*PosX = 600;
		*Dark_PosY = 300;
		*Bright_PosY = 500;
		*Interval = 250;
		*Position = 5;
		break;
	case 6: // 간격을 350 → 150 변경, 암점과 휘점의 Y축 거리를 200px만큼 더 가깝게 & x축 시작위치 +700
		*PosX = 1000;
		*Dark_PosY = 300;
		*Bright_PosY = 500;
		*Interval = 150;
		*Position = 6;
		break;
	case 7: // x축 시작위치를 -100px.
		*PosX = 200;
		*Dark_PosY = 200;
		*Bright_PosY = 600;
		*Interval = 350;
		*Position = 7;
		break;
	case 8: // x축 시작위치를 +100px.
		*PosX = 400;
		*Dark_PosY = 200;
		*Bright_PosY = 600;
		*Interval = 350;
		*Position = 8;
		break;
	default:
		cout << "잘못된 입력입니다 메인 화면으로 돌아갑니다..." << endl;
		return;
		break;
	}
}
void Line_Defect_Position_Select(int* Dark_PosX, int* Bright_PosX, int* Interval, int* Position, bool* AutoRun, int* Cycle) {
	int select = 0;

	cout << "------------------------------------" << endl;
	cout << "결함의 위치를 선택해주세요. (1 ~ 8)" << endl;
	cout << "1) 기본값" << endl
		<< "2) x축 간격 +50px & x축 시작위치 -100" << endl
		<< "3) x축 간격 -50px & x축 시작위치 +100" << endl
		<< "4) 휘선암선 시작위치 변경" << endl
		<< "5) 휘선암선 시작위치 변경, x축 간격 +50px & x축 시작위치 -100" << endl
		<< "6) 휘선암선 시작위치 변경, x축 간격 -50px & x축 시작위치 +100" << endl
		<< "7) 시작위치 암200 휘 350 간격 300 & x축 시작위치 +200" << endl
		<< "8) 시작위치 암200 휘 350 간격 400 & x축 시작위치 +150" << endl;
	cout << "------------------------------------" << endl;
	cout << "입력 >> ";
	if (*AutoRun == true) {
		select = *Cycle;
	}
	else {
		cin >> select;
	}
	cout << select << "번째 위치가 설정되었습니다." << endl;
	switch (select) {
	case 1: // 기본값 그대로 사용.
		*Dark_PosX = 200;
		*Bright_PosX = 1100;
		*Interval = 150;
		*Position = 1;
		break;
	case 2: // 간격 150 → 200 변경. & x축 시작위치 -100
		*Dark_PosX = 100;
		*Bright_PosX = 1000;
		*Interval = 200;
		*Position = 2;
		break;
	case 3: // 간격 150 → 100 변경. & x축 시작위치 +100
		*Dark_PosX = 300;
		*Bright_PosX = 1200;
		*Interval = 100;
		*Position = 3;
		break;
	case 4: // 휘선암선 시작위치 변경 간격 그대로
		*Dark_PosX = 1100;
		*Bright_PosX = 200;
		*Interval = 150;
		*Position = 4;
		break;
	case 5: // 휘선암선 시작위치 변경 간격 150 → 200 & x축 시작위치 -100
		*Dark_PosX = 1000;
		*Bright_PosX = 100;
		*Interval = 200;
		*Position = 5;
		break;
	case 6: // 휘선암선 시작위치 변경 간격 150 → 100 & x축 시작위치 +100
		*Dark_PosX = 1200;
		*Bright_PosX = 300;
		*Interval = 100;
		*Position = 6;
		break;
	case 7: // 시작위치 암200 휘 350 간격 300 & x축 시작위치 +200
		*Dark_PosX = 400;
		*Bright_PosX = 550;
		*Interval = 300;
		*Position = 7;
		break;
	case 8: // 시작위치 암200 휘 350 간격 400 & x축 시작위치 +150
		*Dark_PosX = 350;
		*Bright_PosX = 700;
		*Interval = 400;
		*Position = 8;
		break;
	default:
		cout << "잘못된 입력입니다 메인화면으로 돌아갑니다..." << endl;
		return;
		break;
	}
}
void Draw_Point(Mat RED, Mat GREEN, Mat BLUE, Mat BLACK, Mat G32, Mat G64, Mat G127, Mat WHITE, int *Defect_Type, int *Position, bool *AutoRun, int *Cycle) { // PD
	*Defect_Type = 1;
	*Position = 0;
	int PosX = 300;
	int Dark_PosY = 200;
	int Bright_PosY = 600;
	int Interval = 350; // 간격
	Point_Stain_Defect_Position_Select(&PosX, &Dark_PosY, &Bright_PosY, &Interval, Position, AutoRun, Cycle);
	
	for (int i = 0; i < 5; i++, PosX += Interval) { // Interval 값 PosX에 더해 불량의 간격을 증가.
		// 2*2 사각형으로 Point Defect 를 그린다.
		// RED
		rectangle(RED, Point(PosX, Dark_PosY), Point(PosX + 1, Dark_PosY + 1), Scalar(0, 0, PD_127_Dark_LEVEL[i]), 1);
		rectangle(RED, Point(PosX, Bright_PosY), Point(PosX + 1, Bright_PosY + 1), Scalar(0, 0, PD_127_Bright_LEVEL[i]), 1);
		// GREEN
		rectangle(GREEN, Point(PosX, Dark_PosY), Point(PosX + 1, Dark_PosY + 1), Scalar(0, PD_127_Dark_LEVEL[i], 0), 1);
		rectangle(GREEN, Point(PosX, Bright_PosY), Point(PosX + 1, Bright_PosY + 1), Scalar(0, PD_127_Bright_LEVEL[i], 0), 1);
		// BLUE
		rectangle(BLUE, Point(PosX, Dark_PosY), Point(PosX + 1, Dark_PosY + 1), Scalar(PD_127_Dark_LEVEL[i], 0, 0), 1);
		rectangle(BLUE, Point(PosX, Bright_PosY), Point(PosX + 1, Bright_PosY + 1), Scalar(PD_127_Bright_LEVEL[i], 0, 0), 1);
		// BLACK
		rectangle(BLACK, Point(PosX, Bright_PosY), Point(PosX + 1, Bright_PosY + 1), Scalar(PD_BLACK_Bright_LEVEL[i], PD_BLACK_Bright_LEVEL[i], PD_BLACK_Bright_LEVEL[i]), 1);
		// G32
		rectangle(G32, Point(PosX, Dark_PosY), Point(PosX + 1, Dark_PosY + 1), Scalar(PD_G32_Dark_LEVEL[i], PD_G32_Dark_LEVEL[i], PD_G32_Dark_LEVEL[i]), 1);
		rectangle(G32, Point(PosX, Bright_PosY), Point(PosX + 1, Bright_PosY + 1), Scalar(PD_G32_Bright_LEVEL[i], PD_G32_Bright_LEVEL[i], PD_G32_Bright_LEVEL[i]), 1);
		// G64
		rectangle(G64, Point(PosX, Dark_PosY), Point(PosX + 1, Dark_PosY + 1), Scalar(PD_G64_Dark_LEVEL[i], PD_G64_Dark_LEVEL[i], PD_G64_Dark_LEVEL[i]), 1);
		rectangle(G64, Point(PosX, Bright_PosY), Point(PosX + 1, Bright_PosY + 1), Scalar(PD_G64_Bright_LEVEL[i], PD_G64_Bright_LEVEL[i], PD_G64_Bright_LEVEL[i]), 1);
		// G127
		rectangle(G127, Point(PosX, Dark_PosY), Point(PosX + 1, Dark_PosY + 1), Scalar(PD_127_Dark_LEVEL[i], PD_127_Dark_LEVEL[i], PD_127_Dark_LEVEL[i]), 1);
		rectangle(G127, Point(PosX, Bright_PosY), Point(PosX + 1, Bright_PosY + 1), Scalar(PD_127_Bright_LEVEL[i], PD_127_Bright_LEVEL[i], PD_127_Bright_LEVEL[i]), 1);
		// WHITE
		rectangle(WHITE, Point(PosX, Dark_PosY), Point(PosX + 1, Dark_PosY + 1), Scalar(PD_WHITE_Dark_LEVEL[i], PD_WHITE_Dark_LEVEL[i], PD_WHITE_Dark_LEVEL[i]), 1);

		/*
		// RED 선형 불량의 시작 y값과 종료 y값을 동일하게 설정해 1px 크기의 점형 불량을 그린다.
		// RED
		line(RED, Point(PosX, Dark_PosY), Point(PosX, Dark_PosY), Scalar(0, 0, PD_127_Dark_LEVEL[i]), 1);
		line(RED, Point(PosX, Bright_PosY), Point(PosX, Bright_PosY), Scalar(0, 0, PD_127_Bright_LEVEL[i]), 1);
		// GREEN
		line(GREEN, Point(PosX, Dark_PosY), Point(PosX, Dark_PosY), Scalar(0, PD_127_Dark_LEVEL[i], 0), 1);
		line(GREEN, Point(PosX, Bright_PosY), Point(PosX, Bright_PosY), Scalar(0, PD_127_Bright_LEVEL[i], 0), 1);
		// BLUE
		line(BLUE, Point(PosX, Dark_PosY), Point(PosX, Dark_PosY), Scalar(PD_127_Dark_LEVEL[i], 0, 0), 1);
		line(BLUE, Point(PosX, Bright_PosY), Point(PosX, Bright_PosY), Scalar(PD_127_Bright_LEVEL[i], 0, 0), 1);
		// BLACK
		line(BLACK, Point(PosX, Bright_PosY), Point(PosX, Bright_PosY), Scalar(PD_BLACK_Bright_LEVEL[i], PD_BLACK_Bright_LEVEL[i], PD_BLACK_Bright_LEVEL[i]), 1);
		// G32
		line(G32, Point(PosX, Dark_PosY), Point(PosX, Dark_PosY), Scalar(PD_G32_Dark_LEVEL[i], PD_G32_Dark_LEVEL[i], PD_G32_Dark_LEVEL[i]), 1);
		line(G32, Point(PosX, Bright_PosY), Point(PosX, Bright_PosY), Scalar(PD_G32_Bright_LEVEL[i], PD_G32_Bright_LEVEL[i], PD_G32_Bright_LEVEL[i]), 1);
		// G64
		line(G64, Point(PosX, Dark_PosY), Point(PosX, Dark_PosY), Scalar(PD_G64_Dark_LEVEL[i], PD_G64_Dark_LEVEL[i], PD_G64_Dark_LEVEL[i]), 1);
		line(G64, Point(PosX, Bright_PosY), Point(PosX, Bright_PosY), Scalar(PD_G64_Bright_LEVEL[i], PD_G64_Bright_LEVEL[i], PD_G64_Bright_LEVEL[i]), 1);
		// G127
		line(G127, Point(PosX, Dark_PosY), Point(PosX, Dark_PosY), Scalar(PD_127_Dark_LEVEL[i], PD_127_Dark_LEVEL[i], PD_127_Dark_LEVEL[i]), 1);
		line(G127, Point(PosX, Bright_PosY), Point(PosX, Bright_PosY), Scalar(PD_127_Bright_LEVEL[i], PD_127_Bright_LEVEL[i], PD_127_Bright_LEVEL[i]), 1);
		// WHITE
		line(WHITE, Point(PosX, Dark_PosY), Point(PosX, Dark_PosY), Scalar(PD_WHITE_Dark_LEVEL[i], PD_WHITE_Dark_LEVEL[i], PD_WHITE_Dark_LEVEL[i]), 1);
		*/
	}
	cout << "------------------------------------" << endl;
	cout << "Point Defect Drawing Complete!" << endl;
	cout << "------------------------------------" << endl;
}
void Draw_Line(Mat RED, Mat GREEN, Mat BLUE, Mat BLACK, Mat G32, Mat G64, Mat G127, Mat WHITE, int* Defect_Type, int* Position, bool* AutoRun, int* Cycle) { // LD
	*Defect_Type = 2;
	*Position = 0;

	int Dark_PosX = 200; // 암선 x축 위치
	int Bright_PosX = 1100; // 휘선 y축 위치
	int Interval = 150;

	Line_Defect_Position_Select(&Dark_PosX, &Bright_PosX, &Interval, Position, AutoRun, Cycle);

	for (int i = 0; i < 5; i++, Dark_PosX += Interval, Bright_PosX += Interval) { // x축 150px 씩 증가
		// RED
		line(RED, Point(Dark_PosX, 0), Point(Dark_PosX, 2940), Scalar(0, 0, LD_127_Dark_LEVEL[i]), 1);
		line(RED, Point(Bright_PosX, 0), Point(Bright_PosX, 2940), Scalar(0, 0, LD_127_Bright_LEVEL[i]), 1);
		// GREEN
		line(GREEN, Point(Dark_PosX, 0), Point(Dark_PosX, 2940), Scalar(0, LD_127_Dark_LEVEL[i], 0), 1);
		line(GREEN, Point(Bright_PosX, 0), Point(Bright_PosX, 2940), Scalar(0, LD_127_Bright_LEVEL[i], 0), 1);
		// BLUE
		line(BLUE, Point(Dark_PosX, 0), Point(Dark_PosX, 2940), Scalar(LD_127_Dark_LEVEL[i], 0, 0), 1);
		line(BLUE, Point(Bright_PosX, 0), Point(Bright_PosX, 2940), Scalar(LD_127_Bright_LEVEL[i], 0, 0), 1);
		// BLACK
		line(BLACK, Point(Bright_PosX, 0), Point(Bright_PosX, 2940), Scalar(LD_BLACK_Bright_LEVEL[i], LD_BLACK_Bright_LEVEL[i], LD_BLACK_Bright_LEVEL[i]), 1);
		// G32
		line(G32, Point(Dark_PosX, 0), Point(Dark_PosX, 2940), Scalar(LD_G32_Dark_LEVEL[i], LD_G32_Dark_LEVEL[i], LD_G32_Dark_LEVEL[i]), 1);
		line(G32, Point(Bright_PosX, 0), Point(Bright_PosX, 2940), Scalar(LD_G32_Bright_LEVEL[i], LD_G32_Bright_LEVEL[i], LD_G32_Bright_LEVEL[i]), 1);
		// G64
		line(G64, Point(Dark_PosX, 0), Point(Dark_PosX, 2940), Scalar(LD_G64_Dark_LEVEL[i], LD_G64_Dark_LEVEL[i], LD_G64_Dark_LEVEL[i]), 1);
		line(G64, Point(Bright_PosX, 0), Point(Bright_PosX, 2940), Scalar(LD_G64_Bright_LEVEL[i], LD_G64_Bright_LEVEL[i], LD_G64_Bright_LEVEL[i]), 1);
		// G127
		line(G127, Point(Dark_PosX, 0), Point(Dark_PosX, 2940), Scalar(LD_127_Dark_LEVEL[i], LD_127_Dark_LEVEL[i], LD_127_Dark_LEVEL[i]), 1);
		line(G127, Point(Bright_PosX, 0), Point(Bright_PosX, 2940), Scalar(LD_127_Bright_LEVEL[i], LD_127_Bright_LEVEL[i], LD_127_Bright_LEVEL[i]), 1);
		// WHITE
		line(WHITE, Point(Dark_PosX, 0), Point(Dark_PosX, 2940), Scalar(LD_WHITE_Dark_LEVEL[i], LD_WHITE_Dark_LEVEL[i], LD_WHITE_Dark_LEVEL[i]), 1);
	}
	cout << "------------------------------------" << endl;
	cout << "Line Defect Drawing Complete!" << endl;
	cout << "------------------------------------" << endl;
}
void Draw_Stain(Mat RED, Mat GREEN, Mat BLUE, Mat BLACK, Mat G32, Mat G64, Mat G127, Mat WHITE, int* Defect_Type, int* Position, bool* AutoRun, int* Cycle) {
	*Defect_Type = 3;
	*Position = 0;

	int PosX = 300;
	int Dark_PosY = 200;
	int Bright_PosY = 600;
	int Interval = 350; // 간격
	int Radius = 30;
	Point_Stain_Defect_Position_Select(&PosX, &Dark_PosY, &Bright_PosY, &Interval, Position, AutoRun, Cycle);

	for (int i = 0; i < 5; i++, PosX += Interval) {
		// RED
		for (int j = 1; j <= 30; j++, Radius -= 1) { // Circle 함수를 반복실행하여 그라데이션 효과를 준다.
			circle(RED, Point(PosX, Dark_PosY), Radius, Scalar(0, 0, 127 + Stain_127_Dark_LEVEL[i] * j), -1);
			circle(RED, Point(PosX, Bright_PosY), Radius, Scalar(0, 0, 127 + Stain_127_Bright_LEVEL[i] * j), -1);
		}
		Radius = 30;
		// GREEN
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(GREEN, Point(PosX, Dark_PosY), Radius, Scalar(0, 127 + Stain_127_Dark_LEVEL[i] * j, 0), -1);
			circle(GREEN, Point(PosX, Bright_PosY), Radius, Scalar(0, 127 + Stain_127_Bright_LEVEL[i] * j, 0), -1);
		}
		Radius = 30;
		// BLUE
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(BLUE, Point(PosX, Dark_PosY), Radius, Scalar(127 + Stain_127_Dark_LEVEL[i] * j, 0, 0), -1);
			circle(BLUE, Point(PosX, Bright_PosY), Radius, Scalar(127 + Stain_127_Bright_LEVEL[i] * j, 0, 0), -1);
		}
		Radius = 30;
		// BLACK
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(BLACK, Point(PosX, Bright_PosY), Radius, Scalar(Stain_BLACK_Bright_LEVEL[i] * j, Stain_BLACK_Bright_LEVEL[i] * j, Stain_BLACK_Bright_LEVEL[i] * j), -1);
		}
		Radius = 30;
		// G32
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(G32, Point(PosX, Dark_PosY), Radius, Scalar(32 + Stain_G32_Dark_LEVEL[i] * j, 32 + Stain_G32_Dark_LEVEL[i] * j, 32 + Stain_G32_Dark_LEVEL[i] * j), -1);
			circle(G32, Point(PosX, Bright_PosY), Radius, Scalar(32 + Stain_G32_Bright_LEVEL[i] * j, 32 + Stain_G32_Bright_LEVEL[i] * j, 32 + Stain_G32_Bright_LEVEL[i] * j), -1);
		}
		Radius = 30;
		// G64
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(G64, Point(PosX, Dark_PosY), Radius, Scalar(64 + Stain_G64_Dark_LEVEL[i] * j, 64 + Stain_G64_Dark_LEVEL[i] * j, 64 + Stain_G64_Dark_LEVEL[i] * j), -1);
			circle(G64, Point(PosX, Bright_PosY), Radius, Scalar(64 + Stain_G64_Bright_LEVEL[i] * j, 64 + Stain_G64_Bright_LEVEL[i] * j, 64 + Stain_G64_Bright_LEVEL[i] * j), -1);
		}
		Radius = 30;
		// G127
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(G127, Point(PosX, Dark_PosY), Radius, Scalar(127 + Stain_127_Dark_LEVEL[i] * j, 127 + Stain_127_Dark_LEVEL[i] * j, 127 + Stain_127_Dark_LEVEL[i] * j), -1);
			circle(G127, Point(PosX, Bright_PosY), Radius, Scalar(127 + Stain_127_Bright_LEVEL[i] * j, 127 + Stain_127_Bright_LEVEL[i] * j, 127 + Stain_127_Bright_LEVEL[i] * j), -1);
		}
		Radius = 30;
		// WHITE
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(WHITE, Point(PosX, Dark_PosY), Radius, Scalar(255 + Stain_WHITE_Dark_LEVEL[i] * j, 255 + Stain_WHITE_Dark_LEVEL[i] * j, 255 + Stain_WHITE_Dark_LEVEL[i] * j), -1);
		}
		Radius = 30;
	}
	cout << "------------------------------------" << endl;
	cout << "Stain Defect Drawing Complete!" << endl;
	cout << "------------------------------------" << endl;
}
void Draw_Stain_Color(Mat Color_WHITE_red, Mat Color_WHITE_green, Mat Color_WHITE_blue, int* Defect_Type, int* Position, bool* AutoRun, int* Cycle) {
	*Defect_Type = 3;
	*Position = 0;

	int PosX = 300;
	int Dark_PosY = 200;
	int Bright_PosY = 600;
	int Interval = 350; // 간격
	int Radius = 30;
	Point_Stain_Defect_Position_Select(&PosX, &Dark_PosY, &Bright_PosY, &Interval, Position, AutoRun, Cycle);

	for (int i = 0; i < 5; i++, PosX += Interval) {
		// RED
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(Color_WHITE_red, Point(PosX, Dark_PosY), Radius, Scalar(255, 255, 255 + Stain_Color_WHITE_Dark_LEVEL[i] * j), -1);
			circle(Color_WHITE_red, Point(PosX, Bright_PosY), Radius, Scalar(255, 255, 255 + Stain_Color_WHITE_Dark_LEVEL[i] * j), -1);
		}
		Radius = 30;
		// GREEN
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(Color_WHITE_green, Point(PosX, Dark_PosY), Radius, Scalar(255, 255 + Stain_Color_WHITE_Dark_LEVEL[i] * j, 255), -1);
			circle(Color_WHITE_green, Point(PosX, Bright_PosY), Radius, Scalar(255, 255 + Stain_Color_WHITE_Dark_LEVEL[i] * j, 255), -1);
		}
		Radius = 30;
		// BLUE
		for (int j = 1; j <= 30; j++, Radius -= 1) {
			circle(Color_WHITE_blue, Point(PosX, Dark_PosY), Radius, Scalar(255 + Stain_Color_WHITE_Dark_LEVEL[i] * j, 255, 255), -1);
			circle(Color_WHITE_blue, Point(PosX, Bright_PosY), Radius, Scalar(255 + Stain_Color_WHITE_Dark_LEVEL[i] * j, 255, 255), -1);
		}
		Radius = 30;
	}
	cout << "------------------------------------" << endl;
	cout << "Stain Defect Drawing Complete!" << endl;
	cout << "------------------------------------" << endl;
}
void Show_Image(Mat RED, Mat GREEN, Mat BLUE, Mat BLACK, Mat G32, Mat G64, Mat G127, Mat WHITE) {
	// While 문 안에서 작동 안 됨!!!
	imshow("RED", RED);
	waitKey(0);
	imshow("GREEN", GREEN);
	waitKey(0);
	imshow("BLUE", BLUE);
	waitKey(0);
	imshow("BLACK", BLACK);
	waitKey(0);
	imshow("G32", G32);
	waitKey(0);
	imshow("G64", G64);
	waitKey(0);
	imshow("G127", G127);
	waitKey(0);
	imshow("WHITE", WHITE);
	waitKey(0);
}
void Mono_Save_Image(Mat RED, Mat GREEN, Mat BLUE, Mat BLACK, Mat G32, Mat G64, Mat G127, Mat WHITE, int* Defect_Type, int* Position) {

	// 저장 경로
	//string save_path = "D:\\05.Auto_Curved(국책)\\이미지 시료C";
	string save_path = "D:\\Auto_Curved(국책)_그래픽_이미지";
	char* Create_Directory = const_cast<char*>(save_path.c_str());
	_mkdir(Create_Directory);
	string Defect_Type_path;
	string Position_path;

	switch (*Position) {
	case 1:
		Position_path = "\\01_";
		break;
	case 2:
		Position_path = "\\02_";
		break;
	case 3:
		Position_path = "\\03_";
		break;
	case 4:
		Position_path = "\\04_";
		break;
	case 5:
		Position_path = "\\05_";
		break;
	case 6:
		Position_path = "\\06_";
		break;
	case 7:
		Position_path = "\\07_";
		break;
	case 8:
		Position_path = "\\08_";
		break;
	default:
		cout << "error! 저장실패!" << endl;
		return;
		break;
	}

	switch (*Defect_Type) {
	case 1:
		Defect_Type_path = "PD_";
		break;
	case 2:
		Defect_Type_path = "LD_";
		break;
	case 3:
		Defect_Type_path = "Stain_";
		break;
	default:
		cout << "error! 저장실패!" << endl;
		return;
		break;
	}

	string RED_path = save_path + Position_path + Defect_Type_path + "RED.bmp";
	string GREEN_path = save_path + Position_path + Defect_Type_path + "GREEN.bmp";
	string BLUE_path = save_path + Position_path + Defect_Type_path + "BLUE.bmp";
	string BLACK_path = save_path + Position_path + Defect_Type_path + "BLACK.bmp";
	string G32_path = save_path + Position_path + Defect_Type_path + "G32.bmp";
	string G64_path = save_path + Position_path + Defect_Type_path + "G64.bmp";
	string G127_path = save_path + Position_path + Defect_Type_path + "G127.bmp";
	string WHITE_path = save_path + Position_path + Defect_Type_path + "WHITE.bmp";

	imwrite(RED_path, RED);
	imwrite(GREEN_path, GREEN);
	imwrite(BLUE_path, BLUE);
	imwrite(BLACK_path, BLACK);
	imwrite(G32_path, G32);
	imwrite(G64_path, G64);
	imwrite(G127_path, G127);
	imwrite(WHITE_path, WHITE);
	cout << "------------------------------------" << endl;
	cout << "흑백 이미지 저장 완료!" << endl;
	cout << "------------------------------------" << endl;
}
void Color_Save_Image(Mat Color_WHITE_red, Mat Color_WHITE_green, Mat Color_WHITE_blue, int* Defect_Type, int* Position) {

	// 저장 경로
	//string save_path = "D:\\05.Auto_Curved(국책)\\이미지 시료C";
	string save_path = "D:\\Auto_Curved(국책)_그래픽_이미지";
	char* Create_Directory = const_cast<char*>(save_path.c_str());
	_mkdir(Create_Directory);
	string Defect_Type_path;
	string Position_path;

	switch (*Position) {
	case 1:
		Position_path = "\\01_";
		break;
	case 2:
		Position_path = "\\02_";
		break;
	case 3:
		Position_path = "\\03_";
		break;
	case 4:
		Position_path = "\\04_";
		break;
	case 5:
		Position_path = "\\05_";
		break;
	case 6:
		Position_path = "\\06_";
		break;
	case 7:
		Position_path = "\\07_";
		break;
	case 8:
		Position_path = "\\08_";
		break;
	default:
		cout << "error! 저장실패!" << endl;
		return;
		break;
	}

	switch (*Defect_Type) {
	case 3:
		Defect_Type_path = "Stain_";
		break;
	default:
		cout << "error! 저장실패!" << endl;
		return;
		break;
	}

	string Color_WHITE_red_path = save_path + Position_path + Defect_Type_path + "WHITE_red.bmp";
	string Color_WHITE_green_path = save_path + Position_path + Defect_Type_path + "WHITE_green.bmp";
	string Color_WHITE_blue_path = save_path + Position_path + Defect_Type_path + "WHITE_blue.bmp";

	imwrite(Color_WHITE_red_path, Color_WHITE_red);
	imwrite(Color_WHITE_green_path, Color_WHITE_green);
	imwrite(Color_WHITE_blue_path, Color_WHITE_blue);

	cout << "------------------------------------" << endl;
	cout << "컬러 이미지 저장 완료!" << endl;
	cout << "------------------------------------" << endl;
}

int main(int argc, char** argv) {
	bool AutoRun = false;
	int Cycle = 1;

	int Defect_Type = 0;
	int Position = 0;

	// 흑백 카메라 촬영용 이미지
	Mat RED(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(0, 0, 127));
	Mat GREEN(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(0, 127, 0));
	Mat BLUE(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(127, 0, 0));
	Mat BLACK(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(0, 0, 0));
	Mat G32(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(32, 32, 32));
	Mat G64(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(64, 64, 64));
	Mat G127(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(127, 127, 127));
	Mat WHITE(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(255, 255, 255));
	// 컬러 카메라 촬영용 이미지
	Mat Color_WHITE_red(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(255, 255, 255));
	Mat Color_WHITE_green(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(255, 255, 255));
	Mat Color_WHITE_blue(PanelSizeY, PanelSizeX, CV_8UC3, Scalar(255, 255, 255));

	int select = 0;
	while (true) {
		AutoRun = false;
		Cycle = 1;
		cout << "------------------------------------" << endl;
		cout << "1) 점형 불량 그리기 (흑백)" << endl
			<< "2) 선형 불량 그리기 (흑백)" << endl
			<< "3) 얼룩 불량 그리기 (흑백)" << endl
			<< "4) 얼룩 불량 그리기 (컬러)" << endl
			<< "5) 흑백 저장 (1~3번 항목)" << endl
			<< "6) 컬러 저장 (4번 항목)" << endl
			<< "100) Auto Run" << endl
			<< "0) 종료" << endl;
		cout << "------------------------------------" << endl;
		cout << "입력 >> ";
		cin >> select;
		switch (select) {
		case 0:
			cout << "------------------------------------" << endl;
			cout << "프로그램을 종료합니다." << endl;
			cout << "------------------------------------" << endl;
			return 0;
			break;
		case 1: // Point Defect Drawing
			Draw_Point(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position, &AutoRun, &Cycle);
			break;
		case 2: // Line Defecet Drawing
			Draw_Line(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position, &AutoRun, &Cycle);
			break;
		case 3: // Stain Defect Drawing
			Draw_Stain(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position, &AutoRun, &Cycle);
			break;
		case 4: // Stain (Color) Defect Drawing
			Draw_Stain_Color(Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue, &Defect_Type, &Position, &AutoRun, &Cycle);
			break;
		case 5: // Mono Image Save & Canvas Clear
			Mono_Save_Image(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position);
			Canvas_Clear(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue);
			break;
		case 6: // Color Image Save & Canvas Clear
			Color_Save_Image(Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue, &Defect_Type, &Position);
			Canvas_Clear(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue);
			break;
		case 100: // Auto Run
			AutoRun = true;
			for (int i = 0; i < 8; i++) { // PD 자동 생성
				Draw_Point(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position, &AutoRun, &Cycle);
				Mono_Save_Image(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position);
				Canvas_Clear(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue);
				Cycle++;
			}
			Cycle = 1;
			for (int i = 0; i < 8; i++) { // LD 자동 생성
				Draw_Line(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position, &AutoRun, &Cycle);
				Mono_Save_Image(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position);
				Canvas_Clear(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue);
				Cycle++;
			}
			Cycle = 1;
			for (int i = 0; i < 8; i++) { // Stain 자동 생성
				Draw_Stain(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position, &AutoRun, &Cycle);
				Mono_Save_Image(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, &Defect_Type, &Position);
				Canvas_Clear(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue);
				Cycle++;
			}
			Cycle = 1;
			for (int i = 0; i < 8; i++) { // Stain 자동 생성
				Draw_Stain_Color(Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue, &Defect_Type, &Position, &AutoRun, &Cycle);
				Color_Save_Image(Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue, &Defect_Type, &Position);
				Canvas_Clear(RED, GREEN, BLUE, BLACK, G32, G64, G127, WHITE, Color_WHITE_red, Color_WHITE_green, Color_WHITE_blue);
				Cycle++;
			}
			Cycle = 1;
			break;
		default:
			cout << "------------------------------------" << endl;
			cout << "잘못된 입력입니다." << endl;
			cout << "------------------------------------" << endl;
			break;
		}
	}
	return 0;
}

```
당장 급하게 사용해야 하는 프로그램이라서 일단 콘솔로 짰다 ㅋㅋ  
오랜만에 검은 CDM 화면을 보면서 숫자를 입력하고 있자니  
학부 시절 전화번호부 만들던 때가 생각났다.  

얼룩 결함을 구현하는 데 가장 어려움이 있었다.  
얼룩 결함은 중앙부와 외곽부가 그라데이션 진 모습으로 증상을 보이는데  
그림판에서 어떠한 툴을 사용하는 것이 아닌  
OpenCV 에서 순수 드로잉 함수만으로 그라데이션의 모습을 표현하기는 꽤나 골치아팠다.  

그러던 중 직장동료의 조언으로!!  
그냥 Circle 함수를 반복해서 적용하면 어떤가라는? 아이디어를 받았고  
바로 적용해본 결과 꽤 만족스러운 결과물이 나왔다 ㅋㅋ  

끝!