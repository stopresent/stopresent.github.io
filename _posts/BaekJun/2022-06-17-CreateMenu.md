---
title: "BaekJun Programing : CreateMenu Final Project"

categories:
  - BaekJun
tags:
  - BaekJun

author_profile: false

sidebar:
  nav: "docs"

date: 2022-06-17
last_modified_at: 2022-06-17
---

<br>

## 🙇‍♀️CreateMenu


<br>

### 🪐CreateMenu


```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <conio.h>
#include <windows.h>

typedef struct node
{
   int childCount;
   char name[50];
   struct node* parentNode;
   struct node* childNode[10];
}node;

int dirDeepth = 0;


void showMenu(node* node); // childNode를 출력하고 상위메뉴, 메뉴입력, 종료 출력
void AddMenu(node* node); // 동적할당 해주기(노드 생성)
void EnterMenu(node* childNode); // childNode로 들어가기
void BackMenu(node* node); // 부모님 노드로 이동
void InputMenu(node* node);
void exitPrg(); // 프로그램 종료
void ModfMenu(node* node);
void DelMenu(node* node);


int main()
{
   int input;


   //node* rootNode = {0, };
   struct node* rootNode = (struct node*)malloc(sizeof(struct node));
   memset(rootNode, 0, sizeof(struct node)); // 구조체 초기화 인터넷에서 찾음
   strcpy(rootNode->name, "Root");

   while (1)
   {
      showMenu(rootNode);
   }

}

void showMenu(node* node)
{
   system("cls");

   if (dirDeepth == 0)
      printf("================ 루트 디렉토리 입니다 ================\n");
   else
      printf("============== %d 수준 메뉴 시스템입니다 ==============\n", dirDeepth);

   int i = 1;
   //for (i; i < len(node.childNode); i++)
   for (i; i < 10; i++)
   {
      // childNode name 다 출력
      if (node->childNode[i - 1] == 0) break;
      printf("%d. %s\n", i, node->childNode[i - 1]->name);

   }

   printf("%d. 상위 메뉴로\n", i + 0);
   printf("%d. 메뉴 입력\n", i + 1);
   printf("%d. 종료\n", i + 2);
   printf("원하는 동작을 입력하세요. >>\n");

   int input;
   scanf("%d", &input);


   if (input < i)
   {
      EnterMenu(node->childNode[input - 1]);
   }
   else if (input == i + 0)
   {
      BackMenu(node);
   }
   else if (input == i + 1)
   {
      // 메뉴 입력
      InputMenu(node);
   }
   else if (input == i + 2)
   {
      // 종료
      exitPrg();
   }
}

void AddMenu(node* node)
{
   //struct node* newNode = {0, };
   struct node* newNode = (struct node*)malloc(sizeof(struct node));
   memset(newNode, 0, sizeof(struct node)); // 구조체 초기화 인터넷에서 찾음

   printf("현재 메뉴 수준은 %d 입니다\n", dirDeepth);

   if (dirDeepth == 0)
   {
      printf("입력하시는 메뉴은 루트 메뉴입니다");
   }
   else
   {
      // todo
      printf("입력하시는 상위 메뉴은 %d 수준의 입니다", dirDeepth - 1);
   }

   printf("원하시는 메뉴의 이름을 입력하세요\n");

   char name[50];
   scanf(" %[^\n]s", name);
   strcpy(newNode->name, name);

   newNode->parentNode = node;


   for (int i = 0; i < 10; i++)
      if (node->childNode[i] == NULL)
      {
         node->childNode[i] = newNode;
         break;
      }

   //node->childNode[0] = newNode;

   //dirDeepth++;

   showMenu(node);
}

void EnterMenu(node* childNode)
{
   dirDeepth++;

   showMenu(childNode);
}

void BackMenu(node* node)
{
   // 상위메뉴
   if (dirDeepth == 0)
   {
      printf("최상위 디렉토리 입니다\n");
      printf("3초뒤 돌아갑니다\n");
      Sleep(3000); // 화면 3초 정지?
      return;
   }

   dirDeepth--;

   showMenu(node->parentNode);
}

void InputMenu(node* node)
{
   printf("원하는 동작을 입력하세요.\n");
   printf(">> 1. 메뉴 추가  2. 메뉴 수정  3. 메뉴 삭제\n");
   printf(">> ");


   int input;
   scanf("%d", &input);

   switch (input)
   {
   case 1:
      AddMenu(node);
      break;
   case 2:
      ModfMenu(node);
      break;
   case 3:
      DelMenu(node);
      break;
   default:
      break;
   }
}

void exitPrg()
{
   printf("프로그램을 종료합니다");
   exit(-1);
}


void ModfMenu(node* node)
{
   // TODO
   int index;
   for (int i = 1; i < 10; i++)
   {
      // childNode index 출력
      if (node->childNode[i - 1] == 0) break;
      printf("menu[%d].name=\"%s\"\n", i, node->childNode[i - 1]->name);
   }
   printf("수정하고 싶은 배열의 숫자를 입력하세요\n");
   scanf("%d", &index);

   printf("새로 지을 이름을 입력하세요\n");
   char newName[50];
   scanf(" %[^\n]s", newName);
   strcpy(node->childNode[index - 1]->name, newName);
   
}

void DelMenu(node* node)
{
   int input;
   for (int i = 1; i < 10; i++)
   {
      // childNode index 출력
      if (node->childNode[i - 1] == 0) break;
      printf("menu[%d].name=\"%s\"\n", i, node->childNode[i - 1]->name);
   }
   printf("진행하시겠습니까? 1. 진행 2. 취소\n");
   scanf("%d", &input);

   if (input == 1)
   {
      printf("삭제하고 싶은 배열의 숫자를 입력하세요\n");
      int index;
      scanf("%d", &index);
      printf("선택한 배열은 %d 입니다\n", index);
      // TODO 예외 처리 필요
      printf("맞으면 1, 틀리면 2를 입력해주세요\n");

      int input2;
      scanf("%d", &input2);
      if (input2 == 1)
      {
         // 메뉴의 인덱스에 맞게 선택하면 삭제

         // free 생각
         node->childNode[index - 1] = NULL;
         showMenu(node);
      }
      else if (input2 == 2)
      {
         showMenu(node);
      }
      else
      {
         printf("1 혹은 2를 선택해 주세요");

      }
   }
   else if (input == 2)
   {
      // cancle
      showMenu(node);
   }
   else
   {
      printf("1 혹은 2를 선택해 주세요\n");
   }


}
```
