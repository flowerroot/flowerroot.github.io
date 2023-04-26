---
layout: single
title: "[Python OpenCV] color-space transformation."
categories: OpenCV-python
tag: [OpenCV, python]
toc: true
toc_sticky: true
---
### 소스코드  
```python
import cv2

# 이미지를 COLOR형식으로 불러와서 src에 저장
src = cv2.imread("Image/gomduri.png", cv2.IMREAD_COLOR)
# src에 GRAY스케일을 적용해 dst에 저장
dst = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)

cv2.imshow("src", src)
cv2.imshow("dst", dst)
cv2.waitKey()
cv2.destroyAllWindows()
```
### 실행결과

![image-20220327181516153](../../images/2022-03-27-09-color-space transformation/image-20220327181516153.png)

![image-20220327181522182](../../images/2022-03-27-09-color-space transformation/image-20220327181522182.png)
