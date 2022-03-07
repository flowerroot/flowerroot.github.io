---
layout: single
title: "Driving simulation."
categories: OpenGL
tag: [OpenGL, CG]
toc: true
toc_sticky: true
---

2019년도 2학기 컴퓨터 그래픽스 수업 과제물 **Driving simulation** 입니다.

핸들역할을 하는 Torus는 z축을 중심으로 Rotatef 함수를 사용해서 좌, 우로 회전합니다. 전방시점과 사이드미러는 glBegin(GL_LINES) 함수에 for문을 사용해 구현했습니다.

'w', 's', 'a', 'd' key를 활용해 상, 하, 좌, 우 이동이 가능합니다.

일단 과제라 만들긴 했는데.. 너무 어려워서 완성도 못하고 많이 허접합니다. ㅠㅠ

### 코드

```c++
#include <GL/glut.h>
#define WIN_X 650
#define WIN_Y 650

GLfloat degree = 0.0f;

static double kx = 0;
static double kz = 0;

void KeyBoard(unsigned char key, int x, int y) {
	switch (key) {
	case 'w':
		kz -= 0.05;
		break;
	case 's':
		kz += 0.05;
		break;
	case 'a':
		kx -= 0.05;
		degree += 5;
		break;
	case 'd':
		kx += 0.05;
		degree -= 5;
		break;
	case 'x':
		exit(0);
		break;
	default:
		break;
	}
	glutPostRedisplay();
}

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
	glClearColor(0.4, 0.4, 0.4, 0);
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

	glViewport(160, 0, 260, 200);
	glPushMatrix();
	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();
	gluLookAt(0, 0.8, 0.3, 0, 0, -1, 0, 1, 0);
	glRotated(degree, 0, 0, 3);
	glutSolidTorus(0.25, 0.5, 15, 15);
	glPopMatrix();

	glViewport(0, 180, 650, 650);
	glPushMatrix();
	gluLookAt(0, 0.1, 0, 0, 0, 0, 0, 0, 1.0);
	glBegin(GL_LINES);
	for (double i = -50; i < 100; i += 0.3) {
		glVertex3f(kx - 50, 0.1, kz + i);
		glVertex3f(kx + 50, 0.1, kz + i);
	}
	for (double i = -50; i < 100; i += 0.3) {
		glVertex3f(kx + i, 0.1, kz - 50);
		glVertex3f(kx + i, 0.1, kz + 50);
	}
	glEnd();
	glPopMatrix();

	glViewport(0, -50, 160, 200);
	glPushMatrix();
	gluLookAt(0, 0.1, 0, 0, 0, 0, 0, 0, -3);
	glBegin(GL_LINES);
	for (double i = -50; i < 100; i += 0.3) {
		glVertex3f(kx - 50, 0.2, kz + i);
		glVertex3f(kx + 50, 0.2, kz + i);
	}
	for (double i = -50; i < 100; i += 0.3) {
		glVertex3f(kx + i, 0.2, kz - 50);
		glVertex3f(kx + i, 0.2, kz + 50);
	}
	glEnd();
	glPopMatrix();

	glViewport(430, -50, 180, 200);
	glPushMatrix();
	gluLookAt(0, 0.1, 0, 0, 0, 0, 0, 0, -3);
	glBegin(GL_LINES);
	for (double i = -50; i < 100; i += 0.3) {
		glVertex3f(kx - 50, 0.2, kz + i);
		glVertex3f(kx + 50, 0.2, kz + i);
	}
	for (double i = -50; i < 100; i += 0.3) {
		glVertex3f(kx + i, 0.2, kz - 50);
		glVertex3f(kx + i, 0.2, kz + 50);
	}
	glEnd();
	glPopMatrix();

	glutSwapBuffers();
}

void MyReshape(int w, int h) {
	GLfloat W = (GLfloat)w / (GLfloat)WIN_X;
	GLfloat H = (GLfloat)h / (GLfloat)WIN_Y;
	glMatrixMode(GL_PROJECTION);
	glLoadIdentity();

	glOrtho(-1.0 * W, 1.0 * W, -1.0 * H, 1.0 * H, -1.0, 1.0);
}

int main(int argc, char** argv) {
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_RGB | GLUT_DOUBLE | GLUT_DEPTH);
	glutInitWindowSize(WIN_X, WIN_Y);
	glutInitWindowPosition(0, 0);
	glutCreateWindow("OpenGL CAMERA TEST");

	InitLight();

	glutKeyboardFunc(KeyBoard);
	glutDisplayFunc(Draw);
	glutReshapeFunc(MyReshape);
	glutMainLoop();

	return 0;
}
```

### 실행결과

![Driving](../../images/2022-03-05-Driving-simulation/Driving.png)