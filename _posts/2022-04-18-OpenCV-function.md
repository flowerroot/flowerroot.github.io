---
layout: single
title: "OpenCV 주요함수."
categories: OpenCV-C++
tag: [OpenCV, Cpp]
toc: true
toc_sticky: true
---
### imread()  
```c++
Mat imread(const String& filename, int flags = IMREAD_COLOR);
```
- filename : 불러올 영상 파일 이름  
- flags : 영상 파일 불러오기 옵션 플래그. ImreadModes 열거형 상수를 지정합니다.  
- 반환값 : 불러온 영상 데이터(Mat 객체)  
```c++
IMREAD_UNCHANGED
// 입력 파일에 지정된 그대로의 컬러 속성을 사용합니다.  
// 투명한 PNG 또는 TIFF 파일의 경우, 알파 채널까지 이용하여 4채널 영상으로 불러옵니다.  

IMREAD_GRAYSCALE
// 1채널 그레이스케일 영상으로 변환하여 불러옵니다.  

IMREAD_COLOR
// 3채널 BGR 컬러 영상으로 변환하여 불러옵니다.  

IMREAD_REDUCED_GRAYSCALE_2
// 크기를 1/2로 줄인 1채널 그레이스케일 영상으로 변환합니다.  

IMREAD_REDUCED_COLOR_2
// 크기를 1/2로 줄인 3채널 BGR 영상으로 변환합니다.  

IMREAD_IGNORE_ORIENTATION
// EXIF에 저장된 방향 정보를 사용하지 않습니다.  
```
### imwrite()  
```c++
bool imwrite(const String& filename, InputArray img, const std::vector<int>& params = std::vector<int>());
```
- filename : 저장할 영상 파일 이름
- img : 저장할 영상 데이터(Mat 객체)
- params : 저장할 영상 파일 형식에 의존적인 파라미터(플래그 & 값) 쌍  
(paramId_1, paramValue_1, paramId_2, paramValue_2, ...)
- 반환값 : 정상적으로 저장하면 true, 실패하면 false를 반환합니다.  

### empty()  
```c++
bool Mat::empty() const
```
- 반환값 : 행렬의 rows 또는 cols 멤버 변수가 0이거나, 또는 data 멤버 변수가 NULL이면 true를 반환합니다.  

### namedWindow()  
```c++
void namedWindow(const String& winname, int flags = WINDOW_AUTOSIZE);
```
- winname : 영상 출력 창 상단에 출력되는 창 고유 이름. 이 문자열로 창을 구분합니다.  
- flags : 생성되는 창의 속성을 지정하는 플래그. WindowFlags 열거형 상수를 지정합니다.  
```c++
WINDOW_NORMAL
// 영상 출력 창의 크기에 맞게 영상 크기가 변경되어 출력됩니다.  
// 사용자가 자유롭게 창 크기를 변경할 수 있습니다.  

WINDOW_AUTOSIZE
// 출력하는 영상 크기에 맞게 창 크기가 자동으로 변경됩니다.  
// 사용자가 임의로 창 크기를 변경할 수 없습니다.  

WINDOW_OPENGL
// OpenGL을 지원합니다.  
```
### destroyWindow()  
```c++
void destroyWindow(const String& winname);
void destroyAllWindows();
```
- winname : 소멸시킬 창 이름  

### moveWindow()
```c++
void moveWindow(const String& winname, int x, int y);
```
- winname : 위치를 이동할 창 이름  
- x : 창이 이동할 위치의 x 좌표  
- y : 창이 이동할 위치의 y 좌표  

### resizeWindow()
```c++
void resizeWindow(const String& winname, int width, int height);
```
- winname : 크기를 변경할 창 이름  
- width : 창의 가로 크기  
- height : 창의 세로 크기  

### imshow()
```c++
void imshow(const String& winname, InputArray mat);
```
- winname : 영상을 출력할 대상 창 이름  
- mat : 출력할 영상 데이터(Mat 객체)  

### waitKey()
```c++
int waitKey(int delay = 0);
```
- delay : 키 입력을 기다릴 시간(밀리초 단위). delay <= 0 이면 무한히 기다립니다.  
- 반환값 : 눌린 키 값. 지정한 시간 동안 키가 눌리지 않았으면 -1을 반환합니다.  