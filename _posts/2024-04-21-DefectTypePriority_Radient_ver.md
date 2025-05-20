---
layout: single
title: "[C++] Defect Type Priority."
categories: Cpp
tag: [Cpp]
toc: true
toc_sticky: true
---

### 우선순위 반영 프로그램 Radiant 버전
이전 게시물에서 작성한 프로그램을 Radiant사 Format에 맞춰 수정한 버전.

```c++
/*
제    목: 우선순위좀 반영해줘~~
기    능: DspApp LOT 파일 우선순위 반영해서 다시 맹글어줌.
파일이름: Defect Type Priority
수정날짜: 2023.12.06
작 성 자: 김영진

2024.04.21 김영진
Radiant Foramt으로 변경.
*/

#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <fstream>
#include <string>
#include <ctime>
#include <vector>
#include <windows.h>

using namespace std;

typedef struct {
	string SerialNumber;
	string NG_NAME;	
	string result;

	int priority; // 우선순위.
}DefectInfo;

/*DefectType 우선순위*/
enum class DefectType {
	PD_Bright = 0,
	POL_Bright = 1,
	PD_Dark = 2,
	LD_Horizontal_Bright = 3,
	LD_Vertical_Bright = 4,
	LD_Horizontal_Dark = 5,
	LD_Vertical_Dark = 6,
	Mura_Dot_Bright_Big = 7,
	Mura_Dot_Dark_Big = 8,
	Mura_Dot_Bright_Small = 9,
	Mura_Dot_Dark_Small = 10,
	Mura_Horizontal_Bright = 11,
	Mura_Horizontal_Dark = 12,
	Mura_Vertical_Bright = 13,
	Mura_Vertical_Dark = 14,
	PG_FAIL = 15,
	CAL_FAIL = 16,
	ROI_OUT = 17,
	ETC = 18
};


int main(void) {

	DefectInfo _DefectInfo_Temp = { "S/N","NG_NAME","S/N,GRID,NO,JUDGE,CAM_NO,DET_PTN,NG_NAME,FOCUS_SCORE,IMAGE_X,IMAGE_X(DspApp),IMAGE_Y,PNL_X,PNL_Y,Step,Section,Ave_Luminence,Max_Luminence,Aspect_Ratio,CR,MaxCR,Value,Pixel,Shapes,Length,Width,Height",0 };

	vector<DefectInfo> _DefectInfo = { }; // Defect을 벡터에 담기위해
	_DefectInfo.push_back(_DefectInfo_Temp);
	int DefectCnt = 0;

	// 읽어올 파일명을 획득.
	WIN32_FIND_DATA readFileName;
	HANDLE hFind = FindFirstFile(wstring(L"*.csv").c_str(), &readFileName);

	// readFile open
	ifstream readFile;
	readFile.open(readFileName.cFileName);

	// LOT 파일에서 데이터를 정리해 벡터에 담는과정.
	if (readFile.is_open()) { // 잘 열렸는지 확인하기
		while (!readFile.eof()) { // 파일의 마지막 줄까지 읽는다.
			string line;
			getline(readFile, line); // 줄 간격으로 여기에 담는다.

			int cur_position = 0;
			int position;
			int word_cnt = 0;

			while ((position = (int)line.find(',', cur_position)) != string::npos) {
				int len = position - cur_position;
				string result = line.substr(cur_position, len);
				cur_position = position + 1;

				_DefectInfo_Temp.result = line;

				switch (++word_cnt) {
				case 1:
					_DefectInfo_Temp.SerialNumber = result;
					break;
				case 7:
					_DefectInfo_Temp.NG_NAME = result;
					// PD
					if (_DefectInfo_Temp.NG_NAME == "PD Bright") {
						_DefectInfo_Temp.priority = (int)DefectType::PD_Bright;
					}
					else if (_DefectInfo_Temp.NG_NAME == "POL Bright") {
						_DefectInfo_Temp.priority = (int)DefectType::POL_Bright;
					}
					else if (_DefectInfo_Temp.NG_NAME == "PD Dark") {
						_DefectInfo_Temp.priority = (int)DefectType::PD_Dark;
					}
					// LD
					else if (_DefectInfo_Temp.NG_NAME == "LD Horizontal Bright") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Horizontal_Bright;
					}
					else if (_DefectInfo_Temp.NG_NAME == "LD Vertical Bright") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Vertical_Bright;
					}
					else if (_DefectInfo_Temp.NG_NAME == "LD Horizontal Dark") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Horizontal_Dark;
					}
					else if (_DefectInfo_Temp.NG_NAME == "LD Vertical Dark") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Vertical_Dark;
					}
					// Mura Dot
					else if (_DefectInfo_Temp.NG_NAME == "Mura Dot Bright_Big") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Dot_Bright_Big;
					}
					else if (_DefectInfo_Temp.NG_NAME == "Mura Dot Dark_Big") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Dot_Dark_Big;
					}
					else if (_DefectInfo_Temp.NG_NAME == "Mura Dot Bright_Small") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Dot_Bright_Small;
					}
					else if (_DefectInfo_Temp.NG_NAME == "Mura Dot Dark_Small") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Dot_Dark_Small;
					}
					// Mura 선형,띠형
					else if (_DefectInfo_Temp.NG_NAME == "Mura Horizontal Bright") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Horizontal_Bright;
					}
					else if (_DefectInfo_Temp.NG_NAME == "Mura Horizontal Dark") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Horizontal_Dark;
					}
					else if (_DefectInfo_Temp.NG_NAME == "Mura Vertical Bright") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Vertical_Bright;
					}
					else if (_DefectInfo_Temp.NG_NAME == "Mura Vertical Dark") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Vertical_Dark;
					}
					// 구동이상
					else if (_DefectInfo_Temp.NG_NAME == "PG FAIL") {
						_DefectInfo_Temp.priority = (int)DefectType::PG_FAIL;
					}
					else if (_DefectInfo_Temp.NG_NAME == "CAL FAIL") {
						_DefectInfo_Temp.priority = (int)DefectType::CAL_FAIL;
					}
					else if (_DefectInfo_Temp.NG_NAME == "ROI OUT") {
						_DefectInfo_Temp.priority = (int)DefectType::ROI_OUT;
					}
					else if (_DefectInfo_Temp.NG_NAME == "ETC") {
						_DefectInfo_Temp.priority = (int)DefectType::ETC;
					}
					// Default 값
					else if (_DefectInfo_Temp.NG_NAME == "NG_NAME") {
						// 아무것도 안할꺼지롱
					}
					// 예외처리.
					else {
						cout << "에러발생! 적합한 불량코드명을 찾을 수 없습니다." << endl;
						cout << "Panel ID: " << _DefectInfo_Temp.SerialNumber << " 의 불량코드명을 확인해주세요." << endl;
						return 0;
					}
					break;
				default:
					break;
				}
			}
			//string result = line.substr(cur_position);

			if (_DefectInfo[DefectCnt].SerialNumber != _DefectInfo_Temp.SerialNumber) { // 읽어들인 패널아이디가 가장최근것과 일치하지 않을 때
				_DefectInfo.push_back(_DefectInfo_Temp); // push back
				DefectCnt++;
				cout << "S/N: " << _DefectInfo_Temp.SerialNumber << " 정리 중..." << endl;
			}
			else { // 일치한다면
				// 새로운 정보의 우선순위를 비교한다.
				if (_DefectInfo[DefectCnt].priority > _DefectInfo_Temp.priority) { // 새로운 정보의 우선순위가 더 우선이라면
					_DefectInfo[DefectCnt] = _DefectInfo_Temp; // 덮어씌운다.
				}
			}
		}
	}
	readFile.close();


	// 작성할 파일명을 생성.
	time_t timer = time(NULL); // 현재시각 획득하기
	struct tm* t = localtime(&timer);
	char writeFileName[45] = "";
	int nYear = t->tm_year + 1900; // 2023년에서
	nYear %= 100; // 앞에 두개떼고 23만 남기기
	snprintf(writeFileName, sizeof(writeFileName), "(%02d%02d%02d)_KRT_RESULT_DATA(우선순위반영).csv", nYear, t->tm_mon + 1, t->tm_mday);
	
	// writeFile open
	ofstream writeFile;
	writeFile.open(writeFileName);

	// 벡터에 담긴 파일을 새로 작성하는 과정.
	for (auto i : _DefectInfo) {

		cout << "Panel ID: " << i.SerialNumber << " 기입 중..." << endl;
		writeFile.write(i.result.c_str(), i.result.size());

		writeFile.write("\n", 1);
	}
	writeFile.close();

	cout << "성공적으로 마무리 되었습니다." << endl;

	while (true) {
		int nTemp = 1;
		cout << "종료하려면 '0'을 입력하세요." << endl;
		cout << "입력 >> ";
		cin >> nTemp;

		if (nTemp == 0) {
			break;
		}
	}
	return 0;
}
```

끝.