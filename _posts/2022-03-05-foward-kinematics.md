---
layout: single
title: "[C OpenGL] foward kinematics."
categories: C_OpenGL
tag: [OpenGL, C]
toc: true
toc_sticky: true
---

2019년도 2학기 **컴퓨터 그래픽스** 수업 과제물 **foward kinematics** 입니다.  
2개의 polygon과 2개의 joint로 구성되었으며 'q', 'w', 'a', 's' key를 사용해 조작이 가능합니다.

### 코드

```c++
#include <GL/glut.h>
#define WIN_X 600
#define WIN_Y 600

static int joint_one = 0, joint_two = 0;

void Draw() {
	glClearColor(0, 0, 0, 1);
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();
	gluLookAt(0.0, 0.0, 0.0, 0.0, 0.0, -1.0, 0.0, 1.0, 0.0);

	//------------하단관절
	glPushMatrix();
	glRotatef((GLfloat)joint_one, 0, 0, 1);
	glColor3f(0.5, 0.6, 0.7);
	glutWireSphere(0.08, 20, 16);
	glPopMatrix();

	//------------1번 joint
	glPushMatrix();
	glRotatef((GLfloat)joint_one, 0, 0, 1);
	glTranslatef(0.7, 0, 0);
	glColor3f(0.5, 0.6, 0.7);
	glBegin(GL_POLYGON);
	glVertex3f(-0.7, 0.05, 0);
	glVertex3f(-0.7, -0.05, 0);
	glVertex3f(0, -0.05, 0);
	glVertex3f(0, 0.05, 0);
	glEnd();

	//------------상단관절
	glPushMatrix();
	glRotatef((GLfloat)joint_two, 0, 0, 1);
	glColor3f(0.9, 0.8, 0.2);
	glutWireSphere(0.08, 20, 16);
	glPopMatrix();

	//------------2번 joint
	glRotatef((GLfloat)joint_two, 0, 0, 1);
	glTranslatef(0.2, 0, 0);
	glColor3f(0.9, 0.8, 0.2);
	glBegin(GL_POLYGON);
	glVertex3f(-0.2, 0.05, 0);
	glVertex3f(-0.2, -0.05, 0);
	glVertex3f(0.35, -0.05, 0);
	glVertex3f(0.35, 0.05, 0);
	glEnd();
	glPopMatrix();
	glPopMatrix();

	glutSwapBuffers();
}

void MyReshape(int w, int h) {
	glViewport(0, 0, w, h);
	GLfloat W = (GLfloat)w / (GLfloat)WIN_X;
	GLfloat H = (GLfloat)h / (GLfloat)WIN_Y;
	glMatrixMode(GL_PROJECTION);
	glLoadIdentity();

	glOrtho(-1.0 * W, 1.0 * W, -1.0 * H, 1.0 * H, -1.0, 1.0);
}

void KeyBoard(unsigned char key, int x, int y) {
	switch (key) {
	case 'q':
		joint_one = (joint_one + 10) % 360;
		glutPostRedisplay();
		break;
	case 'a':
		joint_one = (joint_one - 10) % 360;
		glutPostRedisplay();
		break;
	case 'w':
		joint_two = (joint_two + 10) % 360;
		glutPostRedisplay();
		break;
	case 's':
		joint_two = (joint_two - 10) % 360;
		glutPostRedisplay();
		break;
	default:
		break;
	}
}
int main(int argc, char** argv) {
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_RGB | GLUT_DOUBLE | GLUT_DEPTH);
	glutInitWindowSize(WIN_X, WIN_Y);
	glutInitWindowPosition(0, 0);
	glutCreateWindow("forward kinematics");	

	glutKeyboardFunc(KeyBoard);	
	glutDisplayFunc(Draw);
	glutReshapeFunc(MyReshape);
	glutMainLoop();
}
```

### 실행결과

![foward-kinematics-01](../../images/2022-03-05-foward-kinematics/foward-kinematics-01.png)

![foward-kinematics-02](../../images/2022-03-05-foward-kinematics/foward-kinematics-02.png)

![foward-kinematics-03](../../images/2022-03-05-foward-kinematics/foward-kinematics-03.png)

![foward-kinematics-04](../../images/2022-03-05-foward-kinematics/foward-kinematics-04.png)
