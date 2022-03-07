---
layout: single
title: "4view system."
categories: OpenGL
tag: [OpenGL, CG]
toc: true
toc_sticky: true
---

2019년도 2학기 **컴퓨터 그래픽스** 수업 과제물 **4view system** 입니다.  
WireTeapot을 다양한 각도에서 확인하고 SolidTeapot에 조명효과를 주어 확인할 수 있습니다.

### 코드

```c++
#include <GL/glut.h>

int w, h;

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
	glDisable(GL_DEPTH_TEST);
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
	glColor3f(0.5, 0.5, 0.5);
	glPushMatrix();
	glTranslatef(0, -1, 0);
	glBegin(GL_QUADS);
	glVertex3f(2, 0, 2);
	glVertex3f(2, 0, -2);
	glVertex3f(-2, 0, -2);
	glVertex3f(-2, 0, 2);
	glEnd();
	glPopMatrix();
	glEnable(GL_LIGHTING);
	glPushMatrix();
	glTranslatef(0, 0, -0.5);
	glutSolidTeapot(1.2);
	glPopMatrix();
}

void DrawScene() {
	glDisable(GL_LIGHTING);
	glColor3f(0.3, 0.3, 0.7);
	glPushMatrix();
	glTranslatef(0, 0, 0);
	glutWireTeapot(0.5);
	glPopMatrix();
}

void MyDisplay() {
	glClearColor(1.0, 1.0, 1.0, 0.1);
	glClear(GL_COLOR_BUFFER_BIT);
	glColor3f(0.1, 0.1, 0.8);

	glViewport(0, 0, 300, 300);
	glPushMatrix();
	gluLookAt(0, 0, 0.01, 0, 0, 0, 0, 0.01, 0);
	DrawScene();
	glPopMatrix();

	glViewport(300, 0, 300, 300);
	glPushMatrix();
	gluLookAt(0.1, 0, 0, 0, 0, 0, 0, 0.1, 0);
	DrawScene();
	glPopMatrix();

	glViewport(0, 300, 300, 300);
	glPushMatrix();
	gluLookAt(0, 0.1, 0, 0, 0, 0, 0, 0, -0.1);
	DrawScene();
	glPopMatrix();

	glViewport(300, 300, 300, 300);
	glMatrixMode(GL_PROJECTION);
	glPushMatrix();
	glLoadIdentity();
	gluPerspective(40, 1.25, 1, 50);
	glMatrixMode(GL_MODELVIEW);
	glPushMatrix();
	gluLookAt(5, 5, 5, 0, 0, 0, 0, 0.1, 0);
	Draw();
	glPopMatrix();
	glMatrixMode(GL_PROJECTION);
	glPopMatrix();
	glFlush();
	glutSwapBuffers();
}

int main(int argc, char** argv) {
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_RGB | GLUT_DOUBLE | GLUT_DEPTH);
	glutInitWindowSize(650, 650);
	glutInitWindowPosition(0, 0);
	glutCreateWindow("Yeongjin");
	InitLight();
	glutDisplayFunc(MyDisplay);
	glutMainLoop();
}
```

### 실행결과

![4view-system](../../images/2022-03-05-4view-system/4view-system.png)
