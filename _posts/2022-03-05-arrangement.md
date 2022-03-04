---
layout: single
title: "Arrangement and repetition."
categories: JAVA
tag: [JAVA]
toc: true
---

[문제] 

배열과 반복문을 이용하여 프로그램을 작성해보자. 키보드에서 정수로 된 돈의 액수를 입력받아 오만 원권, 만 원권, 천 원권, 500원짜리 동전, 100원짜리 동전, 50원짜리 동전, 10원짜리 동전, 1원짜리 동전이 각 몇 개로 변환되는지 예시와 같이 출력하라. 이때 반드시 다음 배열을 이용하고 반복문으로 작성하라.


## 코드

```java
import java.util.Scanner;
public class Practice_problem_06 {
   public static void main(String[] args) {
      Scanner scan = new Scanner(System.in);
      int [] unit = {50000, 10000, 1000, 500, 100, 50, 10, 1};
      int num;
      
      System.out.print("입력:");
      num = scan.nextInt();
      
      for(int i=0; i<unit.length; i++) {
    	  System.out.println(unit[i]+"원 짜리 : "+num/unit[i]);
         num=num-num/unit[i]*unit[i];
      }      
      scan.close();
   }
}
```

## 실행결과

```java
입력:12345
50000원 짜리 : 0
10000원 짜리 : 1
1000원 짜리 : 2
500원 짜리 : 0
100원 짜리 : 3
50원 짜리 : 0
10원 짜리 : 4
1원 짜리 : 5
```