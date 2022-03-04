---
layout: single
title: "두근두근 도넛."
categories: Computer-Graphics
tag: [OpenGL, CG]
toc: true
---

2019년도 2학기 컴퓨터 그래픽스 수업 과제물 **두근두근 도넛** 입니다.

WireTorus가 좌우로 팽창, 수축을 반복하는 형태입니다.

### 코드1

```c++
#include <iostream>
#include <GL/glut.h>

GLfloat Size1 = 0;
GLfloat Size2 = 0;

void Draw()
{
	glClear(GL_COLOR_BUFFER_BIT);

	if (Size1 <= 600)
		glViewport(100, 50, 600 + Size1, 600);
	else
	{
		glViewport(100, 50, 1200 - Size2, 600);
	}

	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();
	gluLookAt(0.0, 0.0, 0.0, 0.0, 0.0, -1.0, 0.0, 1.0, 0.0);
	glutWireTorus(0.2, 0.4, 60, 60);
	glutSwapBuffers();

}

void Timer(int a)
{
	if (Size1 <= 600)
		Size1 = Size1 + 1;
	else
	{
		if (Size2 <= 600)
			Size2 = Size2 + 1;
		if (Size1 > 600)
		{
			if (Size2 > 600)
			{
				Size1 = 0;
				Size2 = 0;
			}
		}
	}

	glutPostRedisplay();
	glutTimerFunc(1, Timer, 1);
}

int main(int argc, char** argv)
{
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_RGB);
	glutInitWindowSize(1200, 600);
	glutInitWindowPosition(0, 0);
	glutCreateWindow("Yeongjin");

	glClearColor(0.4, 0.4, 0.4, 0.0);

	glutDisplayFunc(Draw);
	glutTimerFunc(40, Timer, 1);
	glutMainLoop();

	return 0;
}
```

### 실행결과1

![Torus-01](../../images/2022-03-05-palpitating-donuts-code/Torus-01.png)

![Torus-02](../../images/2022-03-05-palpitating-donuts-code/Torus-02-16464104876179.png)

다음은 함께 제출했던 **proejct1**입니다.

### 코드2
```c++
#include <GL/glut.h>
#include <GL/glu.h>
#include <GL/gl.h>

int FaltShaded = 0;
int Wirerframed = 0;
int ViewX = 0;
int ViewY = 0;

void InitLight()
{
	GLfloat mat_diffuse[] = { 0.5, 0.4, 0.3, 1.0 };
	GLfloat mat_specular[] = { 1.0, 1.0, 1.0, 1.0 };
	GLfloat mat_ambient[] = { 0.5, 0.4, 0.3, 1.0 };
	GLfloat mat_shininess[] = { 15.0 };
	GLfloat light_specular[] = { 1.0, 1.0, 1.0, 1.0 };
	GLfloat light_diffuse[] = { 0.8, 0.8, 0.8, 1.0 };
	GLfloat light_ambient[] = { 0.3, 0.3, 0.3, 1.0 };
	GLfloat light_position[] = { -3, 6, 3, 0.0 };
	glShadeModel(GL_SMOOTH);
	glEnable(GL_DEPTH_TEST);
	glEnable(GL_LIGHTING);
	glEnable(GL_LIGHT0);
	glLightfv(GL_LIGHT0, GL_POSITION, light_position);
	glLightfv(GL_LIGHT0, GL_DIFFUSE, light_diffuse);
	glLightfv(GL_LIGHT0, GL_SPECULAR, light_specular);
	glLightfv(GL_LIGHT0, GL_AMBIENT, light_ambient);
	glMaterialfv(GL_FRONT, GL_SPECULAR, mat_specular);
	glMaterialfv(GL_FRONT, GL_AMBIENT, mat_ambient);
	glMaterialfv(GL_FRONT, GL_SHININESS, mat_shininess);
}

void MyMouseMove(GLint X, GLint Y,int A,int Z) //마우스 움직임 X, Y를 전역 변수인 ViewX, ViewY에 할당
{
	glutPostRedisplay();
}

void MyKeyboard(unsigned char key, int x, int y)
{
	GLint FlatShaded = 0;
	switch (key)
	{
	case 'q': case 'Q': case '\033':
		exit(0);
		break;
	case 's':
		if (FlatShaded)
		{
			glShadeModel(GL_SMOOTH);
		}

		else
		{
			FlatShaded = 1;
			glShadeModel(GL_FLAT);
		}
		glutPostRedisplay();
		break;
	}
}

void MyDisplay()
{
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();

	gluLookAt(0.0, 0.0, 0.0, 0.0, 0.0, -1.0, 1.0, 1.0, 0.0);

	glutSolidTeapot(0.2);
	glFlush();
}



void MyReshape(int w, int h)
{
	glViewport(0, 0, (GLsizei)w, (GLsizei)h);
	glMatrixMode(GL_PROJECTION);
	glLoadIdentity();
	glOrtho(-1.0, 1.0, -1.0, 1.0, -1.0, 1.0);
}

int main(int argc, char** argv)
{
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_SINGLE | GLUT_RGBA | GLUT_DEPTH);
	glutInitWindowSize(400, 400);
	glutInitWindowPosition(0, 0);
	glutCreateWindow("OenGL Sample Drawing");
	glClearColor(0.4, 0.4, 0.4, 0.0);
	InitLight();
	glutDisplayFunc(MyDisplay);
	glutKeyboardFunc(MyKeyboard);
	glutMouseFunc(MyMouseMove);
	glutReshapeFunc(MyReshape);
	glutMainLoop();
}
```

### 실행결과2

![Teapot](../../images/2022-03-05-palpitating-donuts-code/Teapot.png)
