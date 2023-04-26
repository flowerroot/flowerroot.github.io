---
layout: single
title: "[C++ OpenCV] Gray Scale에 대한 고찰"
categories: OpenCV-C++
tag: [OpenCV, Cpp]
toc: true
toc_sticky: true
---
### Gray Scale
**grayscale**을 적용시키는 데는 다양한 방법이 있다.  
오늘은 대표적으로 세 가지 방법을 소개하려고 한다.  

**첫 번째** **IMREAD_GRAYSCALE**  
```c++
cv::Mat imread_gray = cv::imread("gomduri.png", cv::IMREAD_GRAYSCALE);
```
image 호출 시 grayscale 형태로 호출하는 방법이다.  
변환 속도가 가장 빠르다는 장점이 있다.  

**두 번째** **cvtColor grayscale**
```c++
cv::Mat cvt_gray;
cvtColor(source, cvt_gray, cv::COLOR_BGR2GRAY);
```
color 형태로 불러진 image를 cvtColor 함수를 통해 grayscale로 변환하는 방법이다.  
속도가 빠른 편이지만 IMREAD 함수로 불러낼 때 보다 근소하게 뒤쳐진다.  

**세 번째** **직접 구현**  
```c++
cv::Mat self_gray(source.rows, source.cols, CV_8UC1);

	/* grayscale 적용 code */
	for (int y = 0; y < source.rows; y++) {
		for (int x = 0; x < source.cols; x++) {
			int avg_val = (source.at<cv::Vec3b>(y, x)[0] +
				source.at<cv::Vec3b>(y, x)[1] +
				source.at<cv::Vec3b>(y, x)[2]) / 3;
			self_gray.at<uchar>(y, x) = avg_val;
		}
	}
```
grayscale을 직접 구현하는 방법으로 속도는 가장 느리다.  
웬만하면 이미 만들어진 Library 함수를 잘 이용하는 편이 좋을 것 같다.  

### 소스코드  
```c++

#include <opencv2/opencv.hpp>

// grayscale을 적용시키는 다양한 방법
int main(int ac, char** av) {

	// color image 호출
	cv::Mat source = cv::imread("gomduri.png", cv::IMREAD_COLOR);
	// grayscale image 호출 // 가장 빠름
	cv::Mat imread_gray = cv::imread("gomduri.png", cv::IMREAD_GRAYSCALE);
	// color image를 cvtColor grayscale 적용 // 두번째로 빠름 (큰 차이 없음)
	cv::Mat cvt_gray;
	cvtColor(source, cvt_gray, cv::COLOR_BGR2GRAY);
	// 직접 code로 구현해 grayscale 적용 // 가장 느림
	cv::Mat self_gray(source.rows, source.cols, CV_8UC1);

	/* grayscale 적용 code */
	for (int y = 0; y < source.rows; y++) {
		for (int x = 0; x < source.cols; x++) {
			int avg_val = (source.at<cv::Vec3b>(y, x)[0] +
				source.at<cv::Vec3b>(y, x)[1] +
				source.at<cv::Vec3b>(y, x)[2]) / 3;
			self_gray.at<uchar>(y, x) = avg_val;
		}
	}

	cv::imshow("source", source);
	cv::imshow("imread_gray", imread_gray);
	cv::imshow("cvt_gray", cvt_gray);
	cv::imshow("self_gray", self_gray);

	cv::waitKey(0);
	return 0;
}
```
### 실행결과

![image-20220328214321504](../../images/2022-03-28-grayscale/image-20220328214321504.png)  
**source**

![image-20220328214334268](../../images/2022-03-28-grayscale/image-20220328214334268.png)  
**imread_gray**

![image-20220328214346620](../../images/2022-03-28-grayscale/image-20220328214346620.png)  
**cvt_gray**

![image-20220328214404399](../../images/2022-03-28-grayscale/image-20220328214404399.png)  
**self_gray**

각 각 적용된 **grayscale** image는 육안으로 확인할 때 차이점을 못 느낄수도 있다.  
그러나 gray scale이 적용되는 방식과 원리는 같을지 몰라도 실제 적용된 scale에서 pixel값의 차이가 근소하게 발생하기 때문에 정밀한 계산을 요구하는 작업에 있어선 이들을 혼용해선 안된다.  