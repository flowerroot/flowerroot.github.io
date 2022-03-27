---
layout: single
title: "Camera."
categories: OpenCV-python
tag: [OpenCV, python]
toc: true
toc_sticky: true
---
### 소스코드  
```python
import cv2

# 카메라로부터 정보를 받아온다.
# 0번째 카메라에 연결한다.
capture = cv2.VideoCapture(0)

# 너비(width)와 높이(height)를 조정
capture.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
capture.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)

# 33ms 마다 while문 반복
while cv2.waitKey(33) < 0:
    # ret은 카메라의 상태가 저장되며
    # 정상일 경우 true 작동하지 않을 경우 false를 반환
    # frame에는 현재 시점의 프레임이 저장된다.
    ret, frame = capture.read()
    # windows에 image를 띄운다.
    # 이름은 VideoFrame
    # frame으로부터 할당할 이미지를 받아온다.
    cv2.imshow("VideoFrame", frame)

# 카메라로부터 받아온 메모리를 해제
capture.release()
# 모든 windows 종료.
cv2.destroyAllWindows()
```