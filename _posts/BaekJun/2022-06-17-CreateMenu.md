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

## ๐โโ๏ธCreateMenu


<br>

### ๐ชCreateMenu


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


void showMenu(node* node); // childNode๋ฅผ ์ถ๋ ฅํ๊ณ  ์์๋ฉ๋ด, ๋ฉ๋ด์๋ ฅ, ์ข๋ฃ ์ถ๋ ฅ
void AddMenu(node* node); // ๋์ ํ ๋น ํด์ฃผ๊ธฐ(๋ธ๋ ์์ฑ)
void EnterMenu(node* childNode); // childNode๋ก ๋ค์ด๊ฐ๊ธฐ
void BackMenu(node* node); // ๋ถ๋ชจ๋ ๋ธ๋๋ก ์ด๋
void InputMenu(node* node);
void exitPrg(); // ํ๋ก๊ทธ๋จ ์ข๋ฃ
void ModfMenu(node* node);
void DelMenu(node* node);


int main()
{
   int input;


   //node* rootNode = {0, };
   struct node* rootNode = (struct node*)malloc(sizeof(struct node));
   memset(rootNode, 0, sizeof(struct node)); // ๊ตฌ์กฐ์ฒด ์ด๊ธฐํ ์ธํฐ๋ท์์ ์ฐพ์
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
      printf("================ ๋ฃจํธ ๋๋ ํ ๋ฆฌ ์๋๋ค ================\n");
   else
      printf("============== %d ์์ค ๋ฉ๋ด ์์คํ์๋๋ค ==============\n", dirDeepth);

   int i = 1;
   //for (i; i < len(node.childNode); i++)
   for (i; i < 10; i++)
   {
      // childNode name ๋ค ์ถ๋ ฅ
      if (node->childNode[i - 1] == 0) break;
      printf("%d. %s\n", i, node->childNode[i - 1]->name);

   }

   printf("%d. ์์ ๋ฉ๋ด๋ก\n", i + 0);
   printf("%d. ๋ฉ๋ด ์๋ ฅ\n", i + 1);
   printf("%d. ์ข๋ฃ\n", i + 2);
   printf("์ํ๋ ๋์์ ์๋ ฅํ์ธ์. >>\n");

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
      // ๋ฉ๋ด ์๋ ฅ
      InputMenu(node);
   }
   else if (input == i + 2)
   {
      // ์ข๋ฃ
      exitPrg();
   }
}

void AddMenu(node* node)
{
   //struct node* newNode = {0, };
   struct node* newNode = (struct node*)malloc(sizeof(struct node));
   memset(newNode, 0, sizeof(struct node)); // ๊ตฌ์กฐ์ฒด ์ด๊ธฐํ ์ธํฐ๋ท์์ ์ฐพ์

   printf("ํ์ฌ ๋ฉ๋ด ์์ค์ %d ์๋๋ค\n", dirDeepth);

   if (dirDeepth == 0)
   {
      printf("์๋ ฅํ์๋ ๋ฉ๋ด์ ๋ฃจํธ ๋ฉ๋ด์๋๋ค");
   }
   else
   {
      // todo
      printf("์๋ ฅํ์๋ ์์ ๋ฉ๋ด์ %d ์์ค์ ์๋๋ค", dirDeepth - 1);
   }

   printf("์ํ์๋ ๋ฉ๋ด์ ์ด๋ฆ์ ์๋ ฅํ์ธ์\n");

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
   // ์์๋ฉ๋ด
   if (dirDeepth == 0)
   {
      printf("์ต์์ ๋๋ ํ ๋ฆฌ ์๋๋ค\n");
      printf("2์ด๋ค ๋์๊ฐ๋๋ค\n");
      Sleep(2000); // ํ๋ฉด 2์ด ์ ์ง?
      return;
   }

   dirDeepth--;

   showMenu(node->parentNode);
}

void InputMenu(node* node)
{
   printf("์ํ๋ ๋์์ ์๋ ฅํ์ธ์.\n");
   printf(">> 1. ๋ฉ๋ด ์ถ๊ฐ  2. ๋ฉ๋ด ์์   3. ๋ฉ๋ด ์ญ์ \n");
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
   printf("ํ๋ก๊ทธ๋จ์ ์ข๋ฃํฉ๋๋ค");
   exit(-1);
}


void ModfMenu(node* node)
{
   // TODO
   int index;
   for (int i = 1; i < 10; i++)
   {
      // childNode index ์ถ๋ ฅ
      if (node->childNode[i - 1] == 0) break;
      printf("menu[%d].name=\"%s\"\n", i, node->childNode[i - 1]->name);
   }
   printf("์์ ํ๊ณ  ์ถ์ ๋ฐฐ์ด์ ์ซ์๋ฅผ ์๋ ฅํ์ธ์\n");
   scanf("%d", &index);

   printf("์๋ก ์ง์ ์ด๋ฆ์ ์๋ ฅํ์ธ์\n");
   char newName[50];
   scanf(" %[^\n]s", newName);
   strcpy(node->childNode[index - 1]->name, newName);
   
}

void DelMenu(node* node)
{
   int input;
   for (int i = 1; i < 10; i++)
   {
      // childNode index ์ถ๋ ฅ
      if (node->childNode[i - 1] == 0) break;
      printf("menu[%d].name=\"%s\"\n", i, node->childNode[i - 1]->name);
   }
   printf("์งํํ์๊ฒ ์ต๋๊น? 1. ์งํ 2. ์ทจ์\n");
   scanf("%d", &input);

   if (input == 1)
   {
      printf("์ญ์ ํ๊ณ  ์ถ์ ๋ฐฐ์ด์ ์ซ์๋ฅผ ์๋ ฅํ์ธ์\n");
      int index;
      scanf("%d", &index);
      printf("์ ํํ ๋ฐฐ์ด์ %d ์๋๋ค\n", index);
      // TODO ์์ธ ์ฒ๋ฆฌ ํ์
      printf("๋ง์ผ๋ฉด 1, ํ๋ฆฌ๋ฉด 2๋ฅผ ์๋ ฅํด์ฃผ์ธ์\n");

      int input2;
      scanf("%d", &input2);
      if (input2 == 1)
      {
         // ๋ฉ๋ด์ ์ธ๋ฑ์ค์ ๋ง๊ฒ ์ ํํ๋ฉด ์ญ์ 

         // free ์๊ฐ
         node->childNode[index - 1] = NULL;
         showMenu(node);
      }
      else if (input2 == 2)
      {
         showMenu(node);
      }
      else
      {
         printf("1 ํน์ 2๋ฅผ ์ ํํด ์ฃผ์ธ์");

      }
   }
   else if (input == 2)
   {
      // cancle
      showMenu(node);
   }
   else
   {
      printf("1 ํน์ 2๋ฅผ ์ ํํด ์ฃผ์ธ์\n");
   }


}
```
