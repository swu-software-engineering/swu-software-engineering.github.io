---
layout:     post
title:      "【小组作业开发】"
subtitle:   " \"团队项目\""
date:       2024-11-28 00:00:00
author:     
header-img: "img/post-bg-2015.jpg"
catalog: true
---

## 要求

以三人为小组，制作一个坦克大战。

### 提交内容

提交综合项目的代码已经说明。

文件的内容三个：

1. 项目源代码；
2. 源代码的运行说明（怎么编译运行这个代码）；
3. 运行截图（放word）

## 代码示例

```cpp
#include <stdio.h>
#include <conio.h>
#include <windows.h>
// 11 周，13周（周四，10个小组，小组汇报）
/*
2 周：
坦克大战的游戏：
(1) 两种模式；人机对抗 和 人人对抗；
人机对抗，增加一个AI坦克（随机移动，随机发射子弹）；
人人对抗，p1 wsad移动 空格炮弹，p2 ikjl；
（2）选做，根据贪吃蛇的代码，实现实时对抗。
（3）自己配一个简单的音乐。

音乐键盘的游戏：
我在键盘上按123456，对应产生相应的音调。
*/

#define WIDTH 20    // 游戏地图宽度
#define HEIGHT 10   // 游戏地图高度
#define MAX_BULLETS 5 // 最大同时存在的炮弹数量
#define MAX_BRICKS 10 // 地图中的砖块数量

// 音乐模块
// 定义各个音符的频率（Hz）
// 中音区（普通音）
#define C4  262  // 简谱 1
#define D4  294  // 简谱 2
#define E4  330  // 简谱 3
#define F4  349  // 简谱 4
#define G4  392  // 简谱 5
#define A4  440  // 简谱 6
#define B4  494  // 简谱 7
// 高音区（Higher Octave）
#define C5  523  // 高音 1
#define D5  587  // 高音 2
#define E5  659  // 高音 3
#define F5  698  // 高音 4
#define G5  784  // 高音 5
#define A5  880  // 高音 6
#define B5  988  // 高音 7
// 低音区（Lower Octave）
#define C3  131  // 低音 1
#define D3  147  // 低音 2
#define E3  165  // 低音 3
#define F3  175  // 低音 4
#define G3  196  // 低音 5
#define A3  220  // 低音 6
#define B3  247  // 低音 7

// 超级玛丽
#define C6  1047
#define D6  1175
#define E6  1319
#define F6  1397
#define G6  1568
#define A6  1760
#define B6  1976
#define C7  2093
#define E7  2637
#define G5  784
#define A5  880
#define B5  988
#define F5  698
#define D5  587
#define E5  659

// 定义音符的时值
#define Q  400  // 四分音符（Quarter note）
#define H  800  // 二分音符（Half note）


typedef struct {
    int x, y;       // 坦克的坐标
    char direction; // 坦克当前方向
} Tank;

typedef struct {
    int x, y;       // 炮弹的坐标
    int active;     // 炮弹是否激活
    char direction; // 炮弹方向
} Bullet;

typedef struct {
    int x, y;       // 砖块的坐标
    int active;     // 砖块是否存在
} Brick;

// 全局变量
Tank tank;
Bullet bullets[MAX_BULLETS];
Brick bricks[MAX_BRICKS];

x =100;

// 初始化坦克位置
void initTank(Tank* tank) {
    tank->x = WIDTH / 2;
    tank->y = HEIGHT / 2;
    tank->direction = 'w'; // 初始方向向上
}

// 初始化炮弹
void initBullets(Bullet bullets[]) {
    for (int i = 0; i < MAX_BULLETS; i++) {
        bullets[i].active = 0; // 所有炮弹初始不激活
    }
}

// 初始化砖块
void initBricks(Brick bricks[]) {
    for (int i = 0; i < MAX_BRICKS; i++) {
        bricks[i].x = rand() % WIDTH;
        bricks[i].y = rand() % HEIGHT;
        bricks[i].active = 1; // 所有砖块初始存在
    }
}

// 绘制地图
void drawMap(Tank* tank, Bullet bullets[], Brick bricks[]) {
    system("cls");
    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            int printed = 0;

            // 打印坦克
            if (i == tank->y && j == tank->x) {
                printf("T");
                printed = 1;
            }

            // 打印炮弹
            for (int k = 0; k < MAX_BULLETS; k++) {
                if (bullets[k].active && i == bullets[k].y && j == bullets[k].x) {
                    printf("*");
                    printed = 1;
                    break;
                }
            }

            // 打印砖块
            for (int b = 0; b < MAX_BRICKS; b++) {
                if (bricks[b].active && i == bricks[b].y && j == bricks[b].x) {
                    printf("B");
                    printed = 1;
                    break;
                }
            }

            if (!printed) {
                printf(".");
            }
        }
        printf("\n");
    }
}

// 发射炮弹
void fireBullet(Tank* tank, Bullet bullets[]) {
    for (int i = 0; i < MAX_BULLETS; i++) {
        if (!bullets[i].active) { // 找到未激活的炮弹
            bullets[i].x = tank->x;
            bullets[i].y = tank->y;
            bullets[i].direction = tank->direction; // 炮弹方向与坦克方向一致
            bullets[i].active = 1;
            break;
        }
    }
}

// 更新炮弹位置
void updateBullets(Bullet bullets[], Brick bricks[]) {
    for (int i = 0; i < MAX_BULLETS; i++) {
        if (bullets[i].active) {
            // 根据方向更新炮弹位置
            switch (bullets[i].direction) {
                case 'w': bullets[i].y -= 1; break; // 向上
                case 's': bullets[i].y += 1; break; // 向下
                case 'a': bullets[i].x -= 1; break; // 向左
                case 'd': bullets[i].x += 1; break; // 向右
            }

            // 检测炮弹是否打到砖块
            for (int b = 0; b < MAX_BRICKS; b++) {
                if (bricks[b].active && bullets[i].x == bricks[b].x && bullets[i].y == bricks[b].y) {
                    bricks[b].active = 0; // 砖块消失
                    bullets[i].active = 0; // 炮弹消失
                    break;
                }
            }

            // 超出边界的炮弹变为不活跃
            if (bullets[i].y < 0 || bullets[i].y >= HEIGHT || bullets[i].x < 0 || bullets[i].x >= WIDTH) {
                bullets[i].active = 0;
            }
        }
    }
}

// 检查是否碰撞到砖块
int checkCollision(Tank* tank, Brick bricks[]) {
    for (int i = 0; i < MAX_BRICKS; i++) {
        if (bricks[i].active && tank->x == bricks[i].x && tank->y == bricks[i].y) {
            return 1; // 碰撞到砖块
        }
    }
    return 0; // 未碰撞到砖块
}

// 更新坦克位置
void moveTank(Tank* tank, char direction, Brick bricks[]) {
    int oldX = tank->x;
    int oldY = tank->y;

    // 更新坦克位置，添加边界检查
    switch (direction) {
        case 'w': if (tank->y > 0) tank->y--; break; // 向上
        case 's': if (tank->y < HEIGHT - 1) tank->y++; break; // 向下
        case 'a': if (tank->x > 0) tank->x--; break; // 向左
        case 'd': if (tank->x < WIDTH - 1) tank->x++; break; // 向右
    }

    // 检测是否碰到砖块，若碰到则取消移动
    if (checkCollision(tank, bricks)) {
        tank->x = oldX;
        tank->y = oldY;
    } else {
        tank->direction = direction; // 更新坦克方向
    }
}

void music1()
{
    // 第一部分：5562 11 6(低音) 2
    Beep(G4, Q); // 5
    Beep(G4, Q); // 5
    Beep(A4, Q); // 6
    Beep(D4, H); // 2
    Beep(C4, Q); // 1
    Beep(C4, Q); // 1
    Beep(A3, Q); // 6 (低音)
    Beep(D4, H); // 2

    // 第二部分：556 1(高音) 6511 6(低音)2
    Beep(G4, Q); // 5
    Beep(G4, Q); // 5
    Beep(A4, Q); // 6
    Beep(C5, H); // 1(高音)
    Beep(A4, Q); // 6
    Beep(F4, Q); // 5
    Beep(C4, Q); // 1
    Beep(C4, Q); // 1
    Beep(A3, Q); // 6 (低音)
    Beep(D4, H); // 2

    // 第三部分：521 7(低音) 6(低音)
    Beep(G4, Q); // 5
    Beep(D4, Q); // 2
    Beep(C4, Q); // 1
    Beep(B3, Q); // 7 (低音)
    Beep(A3, H); // 6 (低音)

    // 第四部分：5(低音) 52321
    Beep(G3, Q); // 5 (低音)
    Beep(G4, Q); // 5
    Beep(D4, Q); // 2
    Beep(C4, Q); // 3
    Beep(D4, Q); // 2
    Beep(C4, Q); // 1

    // 第五部分：1 6(低音) 232121 7(低音) 6(低音) 5(低音)
    Beep(C4, Q); // 1
    Beep(A3, Q); // 6 (低音)
    Beep(D4, Q); // 2
    Beep(D4, Q); // 3
    Beep(C4, Q); // 2
    Beep(C4, Q); // 1
    Beep(D4, Q); // 2
    Beep(C4, Q); // 1
    Beep(B3, Q); // 7 (低音)
    Beep(A3, Q); // 6 (低音)
    Beep(G3, H); // 5 (低音)
}

void music2()
{
    // 第一小节
        Beep(E4, Q); // 3
        Beep(G4, Q); // 5
        Beep(C5, Q); // 高音 1
        Beep(D5, Q); // 高音 2
        Beep(E5, H); // 高音 3

        // 第二小节
        Beep(D5, Q); // 高音 2
        Beep(C5, Q); // 高音 1
        Beep(B4, Q); // 7
        Beep(A4, Q); // 6
        Beep(G4, H); // 5

        // 第三小节
        Beep(G4, Q); // 5
        Beep(A4, Q); // 6
        Beep(B4, Q); // 7
        Beep(A4, Q); // 6
        Beep(G4, H); // 5

        // 第四小节
        Beep(G4, Q); // 5
        Beep(F4, Q); // 4
        Beep(E4, Q); // 3
        Beep(D4, Q); // 2
        Beep(C4, H); // 1
}

void music3()
{
  // 超级玛丽主旋律开头
    Beep(E7, Q); Beep(E7, Q); Beep(0, Q); Beep(E7, Q); // E E rest E
    Beep(0, Q); Beep(C7, Q); Beep(E7, Q); Beep(0, Q);  // rest C E rest
    Beep(G6, H); Beep(0, H);                           // G rest

    Beep(G5, H); Beep(0, H);                           // G rest
    Beep(C6, Q); Beep(0, Q); Beep(G5, Q); Beep(0, Q);  // C rest G rest
    Beep(E5, Q); Beep(0, Q); Beep(A5, Q); Beep(0, Q);  // E rest A rest
    Beep(B5, Q); Beep(0, Q); Beep(A5, Q);              // B rest A

    Beep(G5, Q); Beep(E6, Q); Beep(G6, Q); Beep(A6, Q); // G E G A
    Beep(F6, Q); Beep(G6, Q);                          // F G
    Beep(0, Q); Beep(E6, Q); Beep(0, Q); Beep(C6, Q);  // rest E rest C
    Beep(D6, Q); Beep(B5, H);                            // D B
}

int main() {
    initTank(&tank);
    initBullets(bullets);
    initBricks(bricks);

    char input;
    //music3();
    while (1) {
        drawMap(&tank, bullets, bricks); // 绘制地图
        input = _getch(); // 获取用户输入
        if (input == 'q') break; // 按 'q' 退出游戏
        if (input == ' ') {
            fireBullet(&tank, bullets); // 按空格发射炮弹
        } else {
            moveTank(&tank, input, bricks);
        }
        updateBullets(bullets, bricks); // 更新炮弹位置并检测碰撞
    }

    return 0;
}
```

## 代码展示