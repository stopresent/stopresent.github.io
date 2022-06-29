---
title: "C++ Rookiss Part3 자료구조와 알고리즘 : DisjointSet"

categories:
  - Algorithm
tags:
  - Algorithm

author_profile: false

sidebar:
  nav: "docs"

date: 2022-06-29
last_modified_at: 2022-06-29
---

<br>

## 🙇‍♀️DisjointSet


<br>


### 🪐DisjointSet

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <stack>
#include <queue>
using namespace std;
#include <thread>

// 최소 스패닝 트리 (Minimum Spanning Tree)

// 상호 배타적 집합 (Disjoint Set)
// -> 유니온-파인드 Union-Find (합치기-찾기)

// 트리 구조를 이용한 상호 배타적 집합의 표현

void LineageBattleGround()
{
    struct User
    {
        int teamId;
        // TODO
    };

    // TODO : UserManager
    vector<User> users;
    for (int i = 0; i < 1000; i++)
    {
        users.push_back(User{ i });
    }

    for (User& user : users)
    {
        if (user.teamId == 1)
            user.teamId = 2;
    }
}

class NaiveDisjoinSet
{
public:
    NaiveDisjoinSet(int n) : _parent(n)
    {
        for (int i = 0; i < n; i++)
            _parent[i] = i;
    }

    int Find(int u)
    {
        if (u == _parent[u])
            return u;

        return Find(_parent[u]);
    }

    void Merge(int u, int v)
    {
        u = Find(u);
        v = Find(v);

        if (u == v)
            return;

        _parent[u] = v;
    }

private:
    vector<int> _parent;
};

// 트리가 한쪽으로 기우는 문제를 해결?
// 트리를 합칠 때, 항상 [높이가 낮은 트리를] [높이가 높은 트리] 밑으로
// (Union-By-Rank) 랭크에 의한 합치기 최적화

class DisjoinSet
{
public:
    DisjoinSet(int n) : _parent(n), _rank(n, 1)
    {
        for (int i = 0; i < n; i++)
            _parent[i] = i;
    }

    int Find(int u)
    {
        if (u == _parent[u])
            return u;

        return _parent[u] = Find(_parent[u]);
    }

    void Merge(int u, int v)
    {
        u = Find(u);
        v = Find(v);

        if (u == v)
            return;

        if (_rank[u] > _rank[v])
            swap(u, v);

        _parent[u] = v;

        if (_rank[u] == _rank[v])
            _rank[v]++;
    }

private:
    vector<int> _parent;
    vector<int> _rank;
};

int main()
{
    DisjoinSet teams(1000);

    teams.Merge(10, 1);
    int teamId1 = teams.Find(1);
    int teamId2 = teams.Find(10);

    teams.Merge(3, 2);
    int teamId3 = teams.Find(3);
    int teamId4 = teams.Find(2);

    teams.Merge(1, 3);
    int teamId5 = teams.Find(1);
    int teamId6 = teams.Find(3);
    return 0;
}
```