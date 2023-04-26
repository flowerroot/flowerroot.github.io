---
layout: single
title: "[Python OpenCV] Edge Detection."
categories: OpenCV-python
tag: [OpenCV, python]
toc: true
toc_sticky: true
---
### 소스코드  
```python
import cv2

src = cv2.imread("Image/wheat.jpg", cv2.IMREAD_COLOR)
gray = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)

# 가장자리 검출 알고리즘 세 가지
# Sobel, Laplacian, Canny

# cv2.Sobel(입력이미지, 출력이미지정밀도, x방향미분차수, y방향미분차수, 커널크기)
sobel = cv2.Sobel(gray, cv2.CV_8U, 1, 0, 3)

# cv2.Laplacian(입력이미지, 출력이미지정밀도, 커널크기)
laplacian = cv2.Laplacian(gray, cv2.CV_8U, ksize=3)

# cv2.Canny(입력이미지, 하위임곗값, 상위임곗값)
canny = cv2.Canny(src, 100, 255)

cv2.imshow("src", src)
cv2.imshow("gray", gray)
cv2.imshow("sobel", sobel)
cv2.imshow("laplacian", laplacian)
cv2.imshow("canny", canny)
cv2.waitKey()
cv2.destroyAllWindows()
```
### 실행결과

![image-20220327211201030](../../images/2022-03-27-13-EdgeDetection/image-20220327211201030.png)

![image-20220327211223922](../../images/2022-03-27-13-EdgeDetection/image-20220327211223922.png)

![image-20220327211240051](../../images/2022-03-27-13-EdgeDetection/image-20220327211240051.png)

![image-20220327211251602](../../images/2022-03-27-13-EdgeDetection/image-20220327211251602.png)

![image-20220327211300703](../../images/2022-03-27-13-EdgeDetection/image-20220327211300703.png)
