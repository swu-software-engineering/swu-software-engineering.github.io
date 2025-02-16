---
layout:     post
title:      "【学生管理系统】"
subtitle:   "\"实现学生管理系统\""
date:       2024-11-7 00:00:00
author:     
header-img: "img/post-bg-2015.jpg"
catalog: true
---

## 功能要求

根据附件的代码。实现一个学生管理系统。

### 必做功能：

1. 根据姓名查找学生；
2. 不允许重复ID；
3. 统计学生年龄平均值；
4. 查找ID最小的学生。

### 选做功能

输入密码，账户信息；给学生年龄排序；给学生ID排序；增加学生成绩等等。（100分）

### 基础代码

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 定义学生结构体
typedef struct Student {
    int id;
    char name[50];
    int age;
    struct Student* next; // 指向下一个学生节点的指针
} Student;

// 添加学生信息到链表
Student* addStudent(Student* head, int id, const char* name, int age) {
    Student* newStudent = (Student*)malloc(sizeof(Student));
    if (newStudent == NULL) {
        printf("内存分配失败！\n");
        return head;
    }
    newStudent->id = id;
    strcpy(newStudent->name, name);
    newStudent->age = age;
    newStudent->next = NULL;

    if (head == NULL) {
        return newStudent;
    } else {
        Student* temp = head;
        while (temp->next != NULL) {
            temp = temp->next;
        }
        temp->next = newStudent;
    }
    return head;
}

// 根据姓名查找学生。

// 显示所有学生信息
void displayStudents(Student* head) {
    Student* temp = head;
    while (temp != NULL) {
        printf("ID: %d, Name: %s, Age: %d\n", temp->id, temp->name, temp->age);
        temp = temp->next;
    }
}

// 查找学生信息
Student* findStudent(Student* head, int id) {
    Student* temp = head;
    while (temp != NULL) {
        if (temp->id == id) {
            return temp;
        }
        temp = temp->next;
    }
    return NULL;
}

// 更新学生信息
void updateStudent(Student* head, int id, const char* newName, int newAge) {
    Student* student = findStudent(head, id);
    if (student != NULL) {
        strcpy(student->name, newName);
        student->age = newAge;
        printf("更新成功！\n");
    } else {
        printf("未找到ID为%d的学生！\n", id);
    }
}

// 删除学生信息
Student* deleteStudent(Student* head, int id) {
    Student* temp = head;
    Student* prev = NULL;

    while (temp != NULL && temp->id != id) {
        prev = temp;
        temp = temp->next;
    }

    if (temp == NULL) {
        printf("未找到ID为%d的学生！\n", id);
        return head;
    }

    if (prev == NULL) {
        head = temp->next;
    } else {
        prev->next = temp->next;
    }
    free(temp);
    printf("删除成功！\n");
    return head;
}

// 释放链表的内存
void freeList(Student* head) {
    Student* temp;
    while (head != NULL) {
        temp = head;
        head = head->next;
        free(temp);
    }
}

int main() {
    Student* head = NULL;
    int choice, id, age;
    char name[50];

    while (1) {
        printf("\n学生管理系统\n");
        printf("1. 添加学生\n");
        printf("2. 显示所有学生\n");
        printf("3. 查找学生\n");
        printf("4. 更新学生信息\n");
        printf("5. 删除学生\n");
        printf("6. 退出\n");
        printf("请输入选择: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                printf("输入ID: ");
                scanf("%d", &id);
                printf("输入姓名: ");
                scanf("%s", name);
                printf("输入年龄: ");
                scanf("%d", &age);
                head = addStudent(head, id, name, age);
                break;
            case 2:
                displayStudents(head);
                break;
            case 3:
                printf("输入要查找的学生ID: ");
                scanf("%d", &id);
                Student* found = findStudent(head, id);
                if (found != NULL) {
                    printf("ID: %d, Name: %s, Age: %d\n", found->id, found->name, found->age);
                } else {
                    printf("未找到该学生！\n");
                }
                break;
            case 4:
                printf("输入要更新的学生ID: ");
                scanf("%d", &id);
                printf("输入新的姓名: ");
                scanf("%s", name);
                printf("输入新的年龄: ");
                scanf("%d", &age);
                updateStudent(head, id, name, age);
                break;
            case 5:
                printf("输入要删除的学生ID: ");
                scanf("%d", &id);
                head = deleteStudent(head, id);
                break;
            case 6:
                freeList(head);
                printf("退出系统。\n");
                return 0;
            default:
                printf("无效选择，请重新输入！\n");
        }
    }
}

```

>新增文件读写功能

```c
#define FILENAME "students.txt" // 保存学生信息的文件名
void saveToFile(Student* head) {
    FILE* file = fopen(FILENAME, "w");
    if (file == NULL) {
        printf("无法打开文件保存数据！\n");
        return;
    }
    Student* temp = head;
    while (temp != NULL) {
        fprintf(file, "%d %s %d\n", temp->id, temp->name, temp->age);
        temp = temp->next;
    }
    fclose(file);
}
Student* loadFromFile() {
    FILE* file = fopen(FILENAME, "r");
    if (file == NULL) {
        printf("文件不存在，将创建一个新的学生列表。\n");
        return NULL;
    }

    Student* head = NULL;
    int id, age;
    char name[50];
    while (fscanf(file, "%d %s %d", &id, name, &age) == 3) {
        head = addStudent(head, id, name, age);
    }
    fclose(file);
    return head;
}
```

## 代码展示