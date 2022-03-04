---
layout: single
title: "이미지 RGB출력 및 조합."
categories: OpenCV-python
tag: [OpenCV, python, machine-learning, video-processing]
toc: true
---

2021년도 1학기 기계학습기반 영상처리 8주차 출석용 과제물입니다.
**하나의 이미지**에 다양한 형태의 **RGB**를 적용시키는 코드입니다.

### 코드

```python
"""
제   목 : 기계학습기반영상처리 8주차 출석용 과제물
기   능 : 이미지 RGB출력 및 조합
파일이름 : 기계학습기반영상처리_8주차출석용과제물_201622821_김영진
수정날짜 : 2021-04-30
작 성 자 :소프트웨어미디어융합전공 4학년 201622821 김영진
"""

import numpy as np, cv2

image = cv2.imread("C:/Users/Yeongjin/Desktop/Practice/pythonProject/chap05/images/image.png", cv2.IMREAD_COLOR)
if image is None: raise Exception("영상 파일 읽기 오류 발생")

B, G, R = cv2.split(image)

zeros = np.zeros((image.shape[0], image.shape[1]), dtype="uint8")
B = cv2.merge([B, zeros, zeros])
G = cv2.merge([zeros, G, zeros])
R = cv2.merge([zeros, zeros, R])

cv2.imshow("RGB",image)
cv2.imshow("B", B)
cv2.imshow("G", G)
cv2.imshow("R", R)

cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 실행결과

![스크린샷(137)2](../../images/2022-03-05-8weeks-HW copy/스크린샷(137)2.png)
