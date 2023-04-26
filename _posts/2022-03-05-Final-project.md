---
layout: single
title: "[C OpenGL] Final project."
categories: OpenGL
tag: [OpenGL, CG]
toc: true
toc_sticky: true
---

2019년도 2학기 **컴퓨터 그래픽스** 수업 마지막 과제물 입니다.  
사람형태의 모형이 뛰어가는 애니메이션을 구현했으며 'a', 'd' key를 활용하여 시점을 변경할 수 있습니다.

### 코드

```c++
#include<gl\glut.h>
#include<time.h>
#include<math.h>

#define	WINX 600
#define	WINY 400
#define	PI 3.141592

GLUquadricObj* cylinder;
GLfloat RightArm_x = 0;
GLfloat RightArm_y = 0;
GLfloat LeftArm_x = 0;
GLfloat LeftArm_y = 0;
GLfloat RightLeg_x = 0;
GLfloat RightLeg_y = 0;
GLfloat LeftLeg_x = 0;
GLfloat LeftLeg_y = 0;

//---------------------------------------------
float eyeX = 20.0, eyeZ = 00.0;
int theta = 0;
//---------------------------------------------
GLvoid drawScene(GLvoid);
GLvoid Reshape(int w, int h);
void KeyBoard(unsigned char key, int x, int y);
void myTimer(int value);
static double time1 = 0;
void InitLight();

// 몸통
void DrawBody(int x, int a, int b, int c) {
	glPushMatrix();
	cylinder = gluNewQuadric();
	glRotatef(90.0, 1.0f, 0.0f, 0.0f);
	glRotatef(x, a, b, c);
	gluCylinder(cylinder, 1.5, 1.5, 2.5, 50, 1);
	glPopMatrix();
}

// 머리
void DrawHead() {
	glTranslatef(0.0, 1.4, 0.0);
	glPushMatrix();
	cylinder = gluNewQuadric();
	gluSphere(cylinder, 1.3, 30, 10);
	glPopMatrix();
}

//오른쪽 팔
void DrawRightArm(int x, int rx, int ry, int rz) {
	cylinder = gluNewQuadric();
	glRotatef(x, rx, ry, rz);
	glRotatef(90.0, 1.0f, 0.0f, 0.0f);
	glTranslatef(2.0, 0.0, 0.0);
	glRotatef(15.0, 0.0, 1.0, 0.0);
	gluCylinder(cylinder, 0.5, 0.5, 2, 50, 1);
}

// 오른쪽 손
void DrawRightHand(int y, int rx, int ry, int rz) {
	glPushMatrix();
	cylinder = gluNewQuadric();
	glTranslatef(0.0, 0.0, 2.0);
	glRotatef(y, rx, ry, rz);
	gluCylinder(cylinder, 0.5, 0.5, 2, 50, 1);
	glPopMatrix();
}

// 왼쪽 팔
void DrawLeftArm(int x, int rx, int ry, int rz) {
	cylinder = gluNewQuadric();
	glRotatef(x, rx, ry, rz);
	glRotatef(90.0, 1.0f, 0.0f, 0.0f);
	glTranslatef(-2.0, 0.0, 0.0);
	glRotatef(-15.0, 0.0, 1.0, 0.0);
	gluCylinder(cylinder, 0.5, 0.5, 2, 50, 1);
}

// 왼쪽 손
void DrawLeftHand(int y, int rx, int ry, int rz) {
	glPushMatrix();
	cylinder = gluNewQuadric();
	glTranslatef(0.0, 0.0, 2.0);
	glRotatef(y, rx, ry, rz);
	gluCylinder(cylinder, 0.5, 0.5, 2, 50, 1);
	glPopMatrix();
}

// 오른쪽 다리
void DrawRightLeg(int x, int rx, int ry, int rz) {
	cylinder = gluNewQuadric();
	glRotatef(90.0, 1.0f, 0.0f, 0.0f);
	glTranslatef(-1.0, 0.0, 4.0);
	glRotatef(x, rx, ry, rz);
	gluCylinder(cylinder, 0.5, 0.5, 2.0, 50, 1);
}

// 오른쪽 발
void DrawRightFoot(int y, int rx, int ry, int rz) {
	glPushMatrix();
	cylinder = gluNewQuadric();
	glTranslatef(0.0, 0.0, 2.0);
	glRotatef(y, rx, ry, rz);
	gluCylinder(cylinder, 0.5, 0.5, 2.0, 50, 1);
	glPopMatrix();
}

// 왼쪽 다리
void DrawLeftLeg(int x, int rx, int ry, int rz) {
	cylinder = gluNewQuadric();
	glRotatef(90.0, 1.0f, 0.0f, 0.0f);
	glTranslatef(1.0, 0.0, 4.0);
	glRotatef(x, rx, ry, rz);
	gluCylinder(cylinder, 0.5, 0.5, 2.0, 50, 1);
}
// 왼쪽 발
void DrawLeftFoot(int y, int rx, int ry, int rz) {
	glPushMatrix();
	cylinder = gluNewQuadric();
	glTranslatef(0.0, 0.0, 2.0);
	glRotatef(y, rx, ry, rz);
	gluCylinder(cylinder, 0.5, 0.5, 2.0, 50, 1);
	glPopMatrix();
}

void DrawObject() {
	DrawBody(0, 0, 0, 0);
	DrawHead();

	glPushMatrix();
	glTranslatef(0.0, -1.5, 0.0);

	glPushMatrix();
	DrawRightArm(RightArm_x, 1, 0, 0);
	DrawRightHand(RightArm_y, 1, 0, 0);
	glPopMatrix();

	glPushMatrix();
	DrawLeftArm(LeftArm_x, 1, 0, 0);
	DrawLeftHand(LeftArm_y, 1, 0, 0);
	glPopMatrix();

	glPopMatrix();

	glPushMatrix();
	DrawRightLeg(RightLeg_x, 1, 0, 0);
	DrawRightFoot(RightLeg_y, 1, 0, 0);
	glPopMatrix();

	glPushMatrix();
	DrawLeftLeg(LeftLeg_x, 1, 0, 0);
	DrawLeftFoot(LeftLeg_y, 1, 0, 0);
	glPopMatrix();
}


void main(int argc, char* argv[]) {
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGBA | GLUT_DEPTH); 
	glutInitWindowPosition(100, 200);
	glutInitWindowSize(600, 400);
	glutCreateWindow("컴퓨터 그래픽스 기말과제 201622821_김영진");
	glutReshapeFunc(Reshape);
	glutDisplayFunc(drawScene);
	glutKeyboardFunc(KeyBoard);
	glutTimerFunc(50, myTimer, 1);
	glutMainLoop();
}

GLvoid drawScene(GLvoid) {
	glClearColor(0.0f, 0.0f, 0.0f, 1.0f);
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
	glMatrixMode(GL_PROJECTION);
	glLoadIdentity();

	gluPerspective(60, WINX / WINY, 1, 800);
	gluLookAt(eyeX, 0, eyeZ, 0, 0, 0, 0, 1, 0);

	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();

	InitLight();

	LeftArm_x = sin(time1) * 70;
	RightArm_x = -LeftArm_x;
	
	RightArm_y = (sin(time1) * 70 + 50);
	LeftArm_y = (-sin(time1) * 70 + 50);

	LeftLeg_x = sin(time1) * 50;
	RightLeg_x = -LeftLeg_x;
	RightLeg_y = (-sin(time1) * 50 - 30);
	LeftLeg_y = (sin(time1) * 50 - 30);

	glRotatef(-230.0, 0, 1, 0);
	glRotatef((sin(time1) * 15), 1, 0, 0);
	glRotatef(sin(time1) * 15, 0, 1, 0);

	float i = 0;
	i = (sin(time1) * 0.8);
	
	glPushMatrix();
	glTranslatef(0.0, i, 0.0);
	glTranslatef(0.0, 0.5, 0.0);
	DrawObject();
	glPopMatrix();

	glutSwapBuffers();
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

GLvoid Reshape(int w, int h) {
	GLdouble nRange = 200.0f;

	glViewport(0, 0, w, h);
}

void KeyBoard(unsigned char key, int x, int y) {
	switch (key) {
	case 'a':
		theta++;
		eyeX = cos(theta * PI / 180) * 20;
		eyeZ = -sin(theta * PI / 180) * 20;
		break;
	case 'd':
		theta--;
		eyeX = cos(theta * PI / 180) * 20;
		eyeZ = -sin(theta * PI / 180) * 20;
		break;
	default:
		break;
	}
	glutPostRedisplay();
}

void myTimer(int value) {
	time1 += 0.1;
	glutPostRedisplay();
	glutTimerFunc(50, myTimer, 1);
}
```

### 실행결과

![Final-project-01](../../images/2022-03-05-Final-project/Final-project-01.png)

![Final-project-02](../../images/2022-03-05-Final-project/Final-project-02.png)
