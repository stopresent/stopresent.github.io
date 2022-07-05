---
title: "C++ Rookiss Part3 자료구조와 알고리즘 : TicTaeToe"

categories:
  - Algorithm
tags:
  - Algorithm

author_profile: false

sidebar:
  nav: "docs"

date: 2022-07-05
last_modified_at: 2022-07-05
---

<br>

## 🙇‍♀️TicTaeToe

<br>


### 🪐TicTaeToe


```cpp
#include <iostream>
#include <vector>
#include <list>
#include <stack>
#include <queue>
using namespace std;
#include <thread>
#include <windows.h>

vector<vector<char>> board;
int cache[19683];

enum
{
    DEFAULT = 2,
    WIN = 1,
    DRAW = 0,
    LOSE = -1
};

bool IsFinished(vector<vector<char>>& board, char turn)
{
    for (int i = 0; i < 3; i++)
        if (board[i][0] == turn && board[i][1] == turn && board[i][2] == turn)
            return true;

    for (int i = 0; i < 3; i++)
        if (board[0][i] == turn && board[1][i] == turn && board[2][i] == turn)
            return true;

    if (board[0][0] == turn && board[1][1] == turn && board[2][2] == turn)
        return true;

    if (board[0][2] == turn && board[1][1] == turn && board[2][0] == turn)
        return true;

    return false;
}

int HashKey(const vector<vector<char>>& board)
{
    int ret = 0;

    for (int y = 0; y < 3; y++)
    {
        for (int x = 0; x < 3; x++)
        {
            ret = ret * 3;

            if (board[y][x] == 'o')
                ret += 1;
            else if (board[y][x] == 'x')
                ret += 2;
        }
    }

    return ret;
}

int CanWin(vector<vector<char>>& board, char turn)
{
    // 기저 사항
    if(IsFinished(board, 'o' + 'x' - turn))
        return LOSE;

    // 캐시 확인
    int key = HashKey(board);
    int& ret = cache[key];
    if (ret != DEFAULT)
        return ret;

    // 적용
    int minValue = DEFAULT;

    for (int y = 0; y < 3; y++)
    {
        for (int x = 0; x < 3; x++)
        {
            if (board[y][x] != '.')
                continue;

            // 착수
            board[y][x] = turn;

            // 확인
            minValue = min(minValue, CanWin(board, 'o' + 'x' - turn));

            // 취소
            board[y][x] = '.';
        }
    }

    if (minValue == DRAW || minValue == DEFAULT)
        return ret = DRAW;

    return ret = -minValue;

}

int main()
{
    board = vector<vector<char>>
    {
        {'.', '.', '.'},
        {'.', '.', '.'},
        {'.', '.', '.'},
    };

    for (int i = 0; i < 19683; i++)
        cache[i] = DEFAULT;

    int win = CanWin(board, 'o');

    switch (win)
    {
    case WIN:
        cout << "Win" << endl;
        break;
    case DRAW:
        cout << "Draw" << endl;
        break;
    case LOSE:
        cout << "Lose" << endl;
        break;
    }
}
```