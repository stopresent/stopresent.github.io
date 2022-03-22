---
layout: single
title: "PacketGenerator4"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## 🙇‍♀️PacketGenerator4

PacketFormat을 자동으로 DummyClient.GenPackets, Server.GenPackets에 넣어주기

PDL을 수정하고 PacketGenerator를 실행하면 GenPackets들이 자동으로 변화되는법!

### 🪐차근차근


PacketGenerator에 `string pdlPath = "PDL.xml";` 추가

프로그램이 실행할땐 인자로 뭔가를 넘겨줬다면 파싱해서 pdlPath에 넣기
- `if (arg.Length >= 1) pdlPath = args[0];`


#### PDL을 수정하고 PacketGenerator를 실행하면 GenPackets들이 자동으로 변화되는법!

* GenPackets.bat라는 배치파일을 생성
  * 배치 파일은 윈도우에서 제공하는 명령어들을 작성해서 한번에 실행되게 하는 것
  * PacketGenerator.exe파일을 클릭하는 작업을 대신해주고 싶음
* `START ../../PacketGenerator/bin/PacketGenerator.exe ../../PacketGenerator/PDL.xml` 입력
  * 배치파일의 위치에서 PacketGenerator.exe파일의 위치로 설정하고 읽을 PDL.xml위치를 설정
  * 첫인자로 넣어준 `../../PacketGenerator/PDL.xml`이 실제로 프로그램이 실행될 때 메인에 args에 들어가게 됨!
  * 그래서 args[0]이 `../../PacketGenerator/PDL.xml`가 되며 pdlPath에 들어감
* DummyCient의 Packet, Server의 Packet폴더안에 GenPackets.cs 파일로 넣기
  * `XCOPY /Y GenPackets.cs "../../DummyClient/Packet"` 입력
  * `XCOPY /Y GenPackets.cs "../../Server/Packet"` 입력
    * XCOPY는 복사한다는 뜻
    * /Y는 같은 이름의 파일이 있으면 덮어쓴다
    * 만들 파일의 위치를 "" 로 감싸줘야 함

배치 파일을 이용해 exe파일을 클릭해주어서 GenPackets.cs파일을 생성
생성된 파일을 복사해 원하는 위치에 각각 넣어줌



### 🪐출력 경로 바꾸기

속성 -> 빌드 -> 구성 : 모든 구성

출력 경로에 폴더설정

=> netcoreapp3.1이란 폴더로 생성됨 (폴더이름은 바뀔 수 있음)


* 폴더 제거 방법

파일탐색기에서 폴더 열기 -> PacketGenerator.csproj파일 열기(메모장)
`<PropertyGroup>  </PropertyGroup>` 사이에 `<AppendTargetFrameworkToOutputPath>false</AppendTargetFrameworkToOutputPath>` 추가
  
