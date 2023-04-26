---
layout: single
title: "[JAVA] Draw with a Mouse."
categories: JAVA
tag: [JAVA]
toc: true
toc_sticky: true
---

[문제]  
마우스로 점을 찍으면 점들을 계속 연결하여 폐다각형으로 그려지도록 프로그램을 작성하라.


## 코드

```java
import javax.swing.*;
import java.awt.*;
import java.util.*;
import java.awt.event.*;

public class Week_12_HW extends JFrame {
	private MyPanel panel = new MyPanel();
	
	public Week_12_HW() {
		setTitle("Java 12주차 실습과제");
		setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		setContentPane(panel);
		
		setSize(300,300);
		setVisible(true);
	}

	public static void main(String[] args) {
		new Week_12_HW();

	}
	
	class MyPanel extends JPanel {
		private Vector<Point> v = new Vector<Point>();
		
		public MyPanel() {
			addMouseListener(new MouseAdapter() {
				public void mousePressed(MouseEvent e) {
					Point P = e.getPoint();
					v.add(P);
					repaint();
				}				
			});
		}
		
		public void paintComponent(Graphics g) {
			super.paintComponent(g);
			if(v.size() == 2) {
				g.drawLine(v.get(0).x, v.get(0).y, v.get(1).x, v.get(1).y);
			}
			else {
				int x[] = new int[v.size()];
				int y[] = new int[v.size()];
				
				for(int i=0;i<x.length;i++) {
					x[i] = v.get(i).x;
					y[i] = v.get(i).y;
				}
				g.drawPolygon(x,y,v.size());
			}
			g.setColor(Color.BLUE);
			
		}
	}

}

```

## 실행결과

![draw-01](../../images/2022-03-05-draw-mouse/draw-01.png)

![draw-02](../../images/2022-03-05-draw-mouse/draw-02.png)

![draw-03](../../images/2022-03-05-draw-mouse/draw-03.png)
