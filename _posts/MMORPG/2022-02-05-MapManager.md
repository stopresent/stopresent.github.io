---
layout: single
title: "MapManager"
categories: MMORPG
tags: MMORPG
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️MapManager




### 🪐MapManager



**맵매니저 추가**
* Managers에 추가
```cs
MapManager _map = new MapManager();
public static MapManager Map {get { return Instance._map;}}
```

* 진짜 MapManager에 추가
```cs
public Grid CurrentGrid{get; private set;}

public void LoadMap(int mapId)
{
  DestroyMap();

  string mapName = "Map_" + mapId.ToString("000");
  GameObject go = Managers.Resources.Instantiate($"Map/{mapName}");
  go.name = "Map";

  GameObject collision = Util.FindChild(go, "Tilemap_Collision", true);
  if(collision != null) 
    collision.SetActive(false);
  
  CurrentGrid = go.GetComponent<Grd>();
}

public void DestroyMap()
{
  GameObject map = GameObject.Find("Map");
  if(map != null)
  {
    GameObject.Destory(map);
    CurrentGrid = null;
  }
```


`_grid`는 사용하지 않으므로 제거, 그에 맞게 다른부분 교체(`Mamagers.Map.CurrentGrid`사용)



게임 씬 내용
게임씬 스크립트를 추가 (기존 3강에서 만들었던 내용)
`Managers.Map.LoadMap(1);`


맵정보를 타일맵베이스를 바탕으로 만들기
MapEditor에서 설정
맵을 순환하면서 체크하는 부분을 타일맵베이스로 설정


**MapManager에서 Collision 관련 파일을 LoadMap에 추가**
* 필드부분
```cs
public int MinX {get; set;}
public int MaxX {get; set;}
public int MinY {get; set;}
public int MaxY {get; set;}
bool[,] _collision;
```

* Collision 관련 파일
```
TxtAsset txt = Managers.Resource.Load<TextAsset>($"Map/{mapName}");
StringReader reader = new StringReader(txt.text);

MinX = int.Parse(reader.ReadLine());
MaxX = int.Parse(reader.ReadLine());
MinY = int.Parse(reader.ReadLine());
MaxY = int.Parse(reader.ReadLine());

int xCount = MaxX - MinX + 1;
int yCount = MaxY - MinY + 1;

_collision = new bool[yCount, xCount];

for (int y = 0; y < yCount; y++)
{ 
  string line = reader.ReadLine();
  for (int x = 0; x < xCount; x++)
  {
    _collision[y,x] = (line[x] == '1' ? true : false);
  }
}

public bool CanGo(Vector3Int cellPos)
{
  if(cellPos.x < MinX || cellPos.x > MaxX) 
    return false;
  if(cellPos.y < MinY || cellPos.y > MaxY) 
    return false;
  int x = cellPos.x - MinX;
  int y = MaxY - cellPos.y;
  return !_collision[y,x];
}
```


**PlayerController의 실제로 이동 부분을 수정**
```
void UpdateIsMoving()
{
  if(_isMoving == false && _dir != MoveDir.None)
  {
    Vector3Int destPos = _cellPos;
    switch (_dir)
    {
      case MoveDir.Up:
        destPos += Vector3Int.up;
        break;
      case MoveDir.Down:
        destPos += Vector3Int.down;
        break;
      case MoveDir.Left:
        destPos += Vector3Int.left;
        break;
      case MoveDir.Right:
        destPos += Vector3Int.right;
        break;
    }
    
    if(Managers.Map.CanGo(destPos))
    {
      _cellPos = destPos;
      _isMoving = true;
    }
  }
}
```


**카메라 이동(PlayerController)**
```
void LateUpdate()
{
  Camera.main.transform.position = new Vector3(transform.position.x, transform.position.y, -10);
}
```

