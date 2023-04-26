---
layout: single
title: "[Python OpenCV] Binaryization."
categories: OpenCV-python
tag: [OpenCV, python]
toc: true
toc_sticky: true
---
### 소스코드  
```python
import cv2

# 이미지파일을 COLOR형식으로 불러와 src에 저장
src = cv2.imread("Image/geese.jpg", cv2.IMREAD_COLOR)

# src에 GRAY스케일을 대입해 gray에 저장
gray = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)

# cv2.threshold(입력이미지, 임곗값, 최댓값, 임곗값형식)
# 픽셀의 값이 100을 초과할 때 255
# 픽셀의 값이 100 미만일 때 0
ret, dst = cv2.threshold(gray, 100, 255, cv2.THRESH_BINARY)

cv2.imshow("dst", dst)
cv2.waitKey()
cv2.destroyAllWindows()

# 한 마디로 컬러를 흑백으로 바꾸고
# 흑백을 극단적 흑백으로 바꾼다는 뜻
```
### 실행결과

![image-20220327203902240](../../images/2022-03-27-12-Binaryization/image-20220327203902240.png)

![image-20220327203906054](../../images/2022-03-27-12-Binaryization/image-20220327203906054.png)
