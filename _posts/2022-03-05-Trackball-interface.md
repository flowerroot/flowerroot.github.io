---
layout: single
title: "Trackball interface."
categories: OpenGL
tag: [OpenGL, CG]
toc: true
---

2019년도 2학기 컴퓨터 그래픽스 수업 과제물 **Trackball interface** 입니다.

마우스를 사용해 SolidTeapot을 x축과 y축으로 회전시킬 수 있습니다.

### 코드

```c++
#include <iostream>
#include <GL/glut.h>
#define INIT_WIN_X 800
#define INIT_WIN_Y 800

GLint TopLeftX, TopLeftY;

void InitLight() {
	GLfloat mat_diffuse[] = { 0.5,0.5,0.6,1.0 };
	GLfloat mat_specular[] = { 1.0,1.0,1.0,1.0 };
	GLfloat mat_ambient[] = { 0.2,0.2,0.2,1.0 };
	GLfloat mat_shininess[] = { 15.0 };
	GLfloat light_specular[] = { 1.0,1.0,1.0,1.0 };
	GLfloat light_diffuse[] = { 0.8,0.8,0.8,1.0 };
	GLfloat light_ambidnt[] = { 0.3,0.3,0.3,1.0 };
	GLfloat light_position[] = { -3,6, 3.0,0.0 };
	glShadeModel(GL_SMOOTH);
	glEnable(GL_DEPTH_TEST);
	glEnable(GL_LIGHTING);
	glEnable(GL_LIGHT0);
	glLightfv(GL_LIGHT0, GL_POSITION, light_position);
	glLightfv(GL_LIGHT0, GL_DIFFUSE, light_diffuse);
	glLightfv(GL_LIGHT0, GL_SPECULAR, light_specular);
	glLightfv(GL_LIGHT0, GL_AMBIENT, light_ambidnt);

	glMaterialfv(GL_FRONT, GL_SHININESS, mat_shininess);
	glMaterialfv(GL_FRONT, GL_DIFFUSE, mat_diffuse);
	glMaterialfv(GL_FRONT, GL_SPECULAR, mat_specular);
	glMaterialfv(GL_FRONT, GL_AMBIENT, mat_ambient);
}

void Draw() {
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();
	gluLookAt(0.0, 0.0, 0.0, 0.0, 0.0, -1.0, 0.0, 1.0, 0.0);
	glRotatef(TopLeftX, 1, 0, 0);
	glRotatef(TopLeftY, 0, 1, 0);
	glutSolidTeapot(0.5);
	glutSwapBuffers();
}

void processMouse(int button, int state, int x, int y) {
	if (state == GLUT_DOWN)
		TopLeftX = x, TopLeftY = y;

	glutPostRedisplay();
}

void processDragMouse(int x, int y) {	
	TopLeftX = x;
	TopLeftY = y;	
	glutPostRedisplay();
}

void MyReshape(int NewWidth, int NewHeight) {
	glViewport(0, 0, NewWidth, NewHeight);
	GLfloat WidthFactor = (GLfloat)NewWidth / (GLfloat)INIT_WIN_X;
	GLfloat HeightFactor = (GLfloat)NewHeight / (GLfloat)INIT_WIN_Y;
	glMatrixMode(GL_PROJECTION);
	glLoadIdentity();

	glOrtho(-1.0 * WidthFactor, 1.0 * WidthFactor, -1.0 * HeightFactor, 1.0 * HeightFactor, -1.0, 1.0);
}

int main(int argc, char** argv) {
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_RGB | GLUT_DOUBLE | GLUT_DEPTH);
	glutInitWindowSize(INIT_WIN_X, INIT_WIN_Y);
	glutInitWindowPosition(0, 0);
	glutCreateWindow("Yeongjin");

	glClearColor(0.4, 0.4, 0.4, 0.0);
	InitLight();

	glutMouseFunc(processMouse);
	glutMotionFunc(processDragMouse);
	glutDisplayFunc(Draw);
	glutReshapeFunc(MyReshape);
	glutMainLoop();

	return 0;
}
```

### 실행결과

![Trackball-interface-01](../../images/2022-03-05-Trackball-interface/Trackball-interface-01.png)

![Trackball-interface-02](../../images/2022-03-05-Trackball-interface/Trackball-interface-02.png)

![Trackball-interface-03](../../images/2022-03-05-Trackball-interface/Trackball-interface-03.png)

![Trackball-interface-04](../../images/2022-03-05-Trackball-interface/Trackball-interface-04.png)
