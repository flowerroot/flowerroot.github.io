---
layout: single
title: "[C++] Defect Type Priority."
categories: Cpp
tag: [Cpp]
toc: true
toc_sticky: true
---

### 우선순위 반영 프로그램
중국 후이저우로 출장을 가서 업무를 업무에 필요한 매크로 프로그램을 간단히 만들어보았다.

최초 Raw File은 하나의 제품에 대해서 여러개의 Defect이 검출되었다는 목록의 정보만 갖고 있었는데, 나에게는 Defect의 우선순위에 따라서 하나의 제품에 대해 하나의 대표 Defect정보만 갖고있는 데이터가 필요했다.

따라서 해당 제품의 대표 Defect이 뭔지 구분하여 파일을 정리해주는 매크로 프로그램을 만들어 업무용도로 사용했다.

```c++
/*
제    목: 우선순위좀 반영해줘~~
기    능: DspApp LOT 파일 우선순위 반영해서 다시 맹글어줌.
파일이름: Defect Type Priority
수정날짜: 2023.12.06
작 성 자: 김영진
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
	string ID;
	string Type;	
	string result;

	int priority; // 우선순위.
}DefectInfo;

/*DefectType 우선순위*/
enum class DefectType {
	/*1위 Line성 불량*/
	LD_Horizontal_Bright_Single = 1,
	LD_Horizontal_Bright_Multi = 1,
	LD_Horizontal_Bright_Peak = 1,

	LD_Vertical_Bright_Single = 1,
	LD_Vertical_Bright_Multi = 1,
	LD_Vertical_Bright_Peak = 1,

	LD_Horizontal_Dark_Single = 1,
	LD_Horizontal_Dark_Multi = 1,
	LD_Horizontal_Dark_Peak = 1,

	LD_Vertical_Dark_Single = 1,
	LD_Vertical_Dark_Multi = 1,
	LD_Vertical_Dark_Peak = 1,

	/*2위 휘점*/
	PdW_Normal = 2,
	PdW_NearBy = 2,
	PdW_Multi = 2,
	PdW_PanelEdge = 2,

	/*3위 암점*/
	PdB = 3,

	/*4위 얼룩*/
	BLU_Black = 4,
	BLU_White = 4,

	Mura_Dot_Bright_Small = 4,
	Mura_Dot_Bright_Big = 4,
	Mura_Dot_Dark_Small = 4,
	Mura_Dot_Dark_Big = 4,

	YellowSpot_Block = 4,
	YellowSpot_Layer = 4,
	YellowSpot_Wave = 4	
};


int main(void) {
	DefectInfo _DefectInfo_Temp = { "ID","Type","Date,Time,Cam,ID,PTN,Defect,Type,Grid,Data,Gate,ImageX,ImageY,Color,Pd_Area,Pd_SizeX,Pd_SizeY,Pd_Avr,Pd_Sum,Pd_Cr,Pd_Sd,Pd_Focus,Pd_Aspect,Pd_Area*cr,Pd_MaxGv,Pd_MinGv,Pd_MaxGvRatio,Pd_AvrRatio,Pd_Omit%,PdW_130_Up,PdW_160_Up,PdB_25_Down,PdB_30_Down,Pd_Item,Blu_Area,Blu_Aspect,Blu_Area.R,Blu_HL,Blu_News,Blu_Overlap,Blu_Sd,Blu_Focus,Blu_Contrast,Blu_Omit.Crm(Max-Mean),Blu_Omit.%,Blu_Omit.Cnt,Blu_Omit.Line.Limit.Cnt,Blu_Omit.SameLine,Blu_Omit.SameLine.Angle,Blu_Omit.Circle.%,Blu_Omit.ParticleNoise.Area,Blu_Omit.ParticleNoise.Cnt,Blu_Omit.ParticleNoiseSection.Area,Blu_Omit.ParticleNoiseSection.Cnt,Blu_Image,Blu_Item,Ld_Amplitude,Ld_WaveLength,Ld_Connect.Cnt,Ld_Line.Cnt,Ld_WavePointDiff,WS_AREA,WS_LENGTH,WS_HEIGHT,WS_EDGE.OVER.CNT,WS_ECCENTRICITY,WS_BOUND.AREA.RATIO,WS_ASPECT.RATIO,WS_OMIT.RATIO,WS_OMIT.AREA,WS_OMIT.DIST,WS_BG.GV.RATIO,WS_HISTO.GV,WS_HISTO.DIFF.LAYER,WS_HISTO.DIFF.LAYER.CNT,BS_AREA,BS_LENGTH,BS_HEIGHT,BS_EDGE.OVER.CNT,BS_ECCENTRICITY,BS_BOUND.AREA.RATIO,BS_ASPECT.RATIO,BS_OMIT.RATIO,BS_OMIT.AREA,BS_OMIT.DIST,BS_BG.GV.RATIO,BS_HISTO.GV,BS_HISTO.DIFF.LAYER,BS_HISTO.DIFF.LAYER.CNT,YS_WAVE.AMP,YS_WAVE.LENGTH,YS_BLOCK.DIFF.GV,YS_BLOCK.DIFF.GV2,YS_LAYER.AREA,YS_LAYER.LENGTH,YS_LAYER.HEIGHT,YS_LAYER.EDGE.OVER.CNT,YS_LAYER.ECCENTRICITY,YS_LAYER.BOUND.AREA.RATIO,YS_LAYER.ASPECT.RATIO,YS_LAYER.OMIT.RATIO,YS_LAYER.BG.GV.RATIO,YS_LAYER.HISTO.GV,YS_LAYER.HISTO.DIFF.LAYER,YS_ABS.TH.RELATIVE.GV,Resize_Area,Resize_Aspect,Resize_MeanRatio,Resize_Omit.%,Resize_Omit.Cnt,Resize_Item",0 };

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
				case 4:
					_DefectInfo_Temp.ID = result;
					break;
				case 7:
					_DefectInfo_Temp.Type = result;
					// LD
					if (_DefectInfo_Temp.Type == "LD Horizontal Bright-Single") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Horizontal_Bright_Single;
					}
					else if (_DefectInfo_Temp.Type == "LD Horizontal Bright-Multi") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Horizontal_Bright_Multi;
					}
					else if (_DefectInfo_Temp.Type == "LD Horizontal Bright-Peak") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Horizontal_Bright_Peak;
					}
					else if (_DefectInfo_Temp.Type == "LD Vertical Bright-Single") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Vertical_Bright_Single;
					}
					else if (_DefectInfo_Temp.Type == "LD Vertical Bright-Multi") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Vertical_Bright_Multi;
					}
					else if (_DefectInfo_Temp.Type == "LD Vertical Bright-Peak") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Vertical_Bright_Peak;
					}
					else if (_DefectInfo_Temp.Type == "LD Horizontal Dark-Single") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Horizontal_Dark_Single;
					}
					else if (_DefectInfo_Temp.Type == "LD Horizontal Dark-Multi") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Horizontal_Dark_Multi;
					}
					else if (_DefectInfo_Temp.Type == "LD Horizontal Dark-Peak") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Horizontal_Dark_Peak;
					}
					else if (_DefectInfo_Temp.Type == "LD Vertical Dark-Single") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Vertical_Dark_Single;
					}
					else if (_DefectInfo_Temp.Type == "LD Vertical Dark-Multi") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Vertical_Dark_Multi;
					}
					else if (_DefectInfo_Temp.Type == "LD Vertical Dark-Peak") {
						_DefectInfo_Temp.priority = (int)DefectType::LD_Vertical_Dark_Peak;
					}
					// 휘점
					else if (_DefectInfo_Temp.Type == "PdW_Normal") {
						_DefectInfo_Temp.priority = (int)DefectType::PdW_Normal;
					}
					else if (_DefectInfo_Temp.Type == "PdW_NearBy") {
						_DefectInfo_Temp.priority = (int)DefectType::PdW_NearBy;
					}
					else if (_DefectInfo_Temp.Type == "PdW_Multi") {
						_DefectInfo_Temp.priority = (int)DefectType::PdW_Multi;
					}
					else if (_DefectInfo_Temp.Type == "PdW_PanelEdge") {
						_DefectInfo_Temp.priority = (int)DefectType::PdW_PanelEdge;
					}
					// 암점
					else if (_DefectInfo_Temp.Type == "PdB") {
						_DefectInfo_Temp.priority = (int)DefectType::PdB;
					}
					// 얼룩
					else if (_DefectInfo_Temp.Type == "BLU_Black") {
						_DefectInfo_Temp.priority = (int)DefectType::BLU_Black;
					}
					else if (_DefectInfo_Temp.Type == "BLU_White") {
						_DefectInfo_Temp.priority = (int)DefectType::BLU_White;
					}
					/*else if (_DefectInfo_Temp.Type == "Mura Dot Bright Small") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Dot_Bright_Small;
					}
					else if (_DefectInfo_Temp.Type == "Mura Dot Bright Big") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Dot_Bright_Big;
					}
					else if (_DefectInfo_Temp.Type == "Mura Dot Dark Small") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Dot_Dark_Small;
					}
					else if (_DefectInfo_Temp.Type == "Mura Dot Dark Big") {
						_DefectInfo_Temp.priority = (int)DefectType::Mura_Dot_Dark_Big;
					}*/
					else if (_DefectInfo_Temp.Type == "YellowSpot_Block") {
						_DefectInfo_Temp.priority = (int)DefectType::YellowSpot_Block;
					}
					else if (_DefectInfo_Temp.Type == "YellowSpot_Layer") {
						_DefectInfo_Temp.priority = (int)DefectType::YellowSpot_Layer;
					}
					else if (_DefectInfo_Temp.Type == "YellowSpot_Wave") {
						_DefectInfo_Temp.priority = (int)DefectType::YellowSpot_Wave;
					}
					else if (_DefectInfo_Temp.Type == "Type") {
						// 아무것도 안할꺼지롱
					}
					else {
						cout << "에러발생! 적합한 불량코드명을 찾을 수 없습니다." << endl;
						cout << "Panel ID: " << _DefectInfo_Temp.ID << " 의 불량코드명을 확인해주세요." << endl;
						return 0;
					}
					break;
				default:
					break;
				}
			}
			//string result = line.substr(cur_position);

			if (_DefectInfo[DefectCnt].ID != _DefectInfo_Temp.ID) { // 읽어들인 패널아이디가 가장최근것과 일치하지 않을 때
				_DefectInfo.push_back(_DefectInfo_Temp); // push back
				DefectCnt++;
				cout << "Panel ID: " << _DefectInfo_Temp.ID << " 정리 중..." << endl;
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
	snprintf(writeFileName, sizeof(writeFileName), "(%02d%02d%02d)_FOX_RESULT_DATA(우선순위반영).csv", nYear, t->tm_mon + 1, t->tm_mday);
	
	// writeFile open
	ofstream writeFile;
	writeFile.open(writeFileName);

	// 벡터에 담긴 파일을 새로 작성하는 과정.
	for (auto i : _DefectInfo) {

		cout << "Panel ID: " << i.ID << " 기입 중..." << endl;
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