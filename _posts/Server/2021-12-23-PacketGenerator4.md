---
layout: single
title: "C# Rookiss Part4 ๊ฒ์์๋ฒ : PacketGenerator4"
categories: Server
tags: Server
author_profile: false
sidebar:
  nav: "docs"
---


## ๐โโ๏ธPacketGenerator4

PacketFormat์ ์๋์ผ๋ก DummyClient.GenPackets, Server.GenPackets์ ๋ฃ์ด์ฃผ๊ธฐ

PDL์ ์์ ํ๊ณ  PacketGenerator๋ฅผ ์คํํ๋ฉด GenPackets๋ค์ด ์๋์ผ๋ก ๋ณํ๋๋๋ฒ!

### ๐ช์ฐจ๊ทผ์ฐจ๊ทผ


PacketGenerator์ `string pdlPath = "PDL.xml";` ์ถ๊ฐ

ํ๋ก๊ทธ๋จ์ด ์คํํ ๋ ์ธ์๋ก ๋ญ๊ฐ๋ฅผ ๋๊ฒจ์คฌ๋ค๋ฉด ํ์ฑํด์ pdlPath์ ๋ฃ๊ธฐ
- `if (arg.Length >= 1) pdlPath = args[0];`


#### PDL์ ์์ ํ๊ณ  PacketGenerator๋ฅผ ์คํํ๋ฉด GenPackets๋ค์ด ์๋์ผ๋ก ๋ณํ๋๋๋ฒ!

* GenPackets.bat๋ผ๋ ๋ฐฐ์นํ์ผ์ ์์ฑ
  * ๋ฐฐ์น ํ์ผ์ ์๋์ฐ์์ ์ ๊ณตํ๋ ๋ช๋ น์ด๋ค์ ์์ฑํด์ ํ๋ฒ์ ์คํ๋๊ฒ ํ๋ ๊ฒ
  * PacketGenerator.exeํ์ผ์ ํด๋ฆญํ๋ ์์์ ๋์ ํด์ฃผ๊ณ  ์ถ์
* `START ../../PacketGenerator/bin/PacketGenerator.exe ../../PacketGenerator/PDL.xml` ์๋ ฅ
  * ๋ฐฐ์นํ์ผ์ ์์น์์ PacketGenerator.exeํ์ผ์ ์์น๋ก ์ค์ ํ๊ณ  ์ฝ์ PDL.xml์์น๋ฅผ ์ค์ 
  * ์ฒซ์ธ์๋ก ๋ฃ์ด์ค `../../PacketGenerator/PDL.xml`์ด ์ค์ ๋ก ํ๋ก๊ทธ๋จ์ด ์คํ๋  ๋ ๋ฉ์ธ์ args์ ๋ค์ด๊ฐ๊ฒ ๋จ!
  * ๊ทธ๋์ args[0]์ด `../../PacketGenerator/PDL.xml`๊ฐ ๋๋ฉฐ pdlPath์ ๋ค์ด๊ฐ
* DummyCient์ Packet, Server์ Packetํด๋์์ GenPackets.cs ํ์ผ๋ก ๋ฃ๊ธฐ
  * `XCOPY /Y GenPackets.cs "../../DummyClient/Packet"` ์๋ ฅ
  * `XCOPY /Y GenPackets.cs "../../Server/Packet"` ์๋ ฅ
    * XCOPY๋ ๋ณต์ฌํ๋ค๋ ๋ป
    * /Y๋ ๊ฐ์ ์ด๋ฆ์ ํ์ผ์ด ์์ผ๋ฉด ๋ฎ์ด์ด๋ค
    * ๋ง๋ค ํ์ผ์ ์์น๋ฅผ "" ๋ก ๊ฐ์ธ์ค์ผ ํจ

๋ฐฐ์น ํ์ผ์ ์ด์ฉํด exeํ์ผ์ ํด๋ฆญํด์ฃผ์ด์ GenPackets.csํ์ผ์ ์์ฑ
์์ฑ๋ ํ์ผ์ ๋ณต์ฌํด ์ํ๋ ์์น์ ๊ฐ๊ฐ ๋ฃ์ด์ค



### ๐ช์ถ๋ ฅ ๊ฒฝ๋ก ๋ฐ๊พธ๊ธฐ

์์ฑ -> ๋น๋ -> ๊ตฌ์ฑ : ๋ชจ๋  ๊ตฌ์ฑ

์ถ๋ ฅ ๊ฒฝ๋ก์ ํด๋์ค์ 

=> netcoreapp3.1์ด๋ ํด๋๋ก ์์ฑ๋จ (ํด๋์ด๋ฆ์ ๋ฐ๋ ์ ์์)


* ํด๋ ์ ๊ฑฐ ๋ฐฉ๋ฒ

ํ์ผํ์๊ธฐ์์ ํด๋ ์ด๊ธฐ -> PacketGenerator.csprojํ์ผ ์ด๊ธฐ(๋ฉ๋ชจ์ฅ)
`<PropertyGroup>  </PropertyGroup>` ์ฌ์ด์ `<AppendTargetFrameworkToOutputPath>false</AppendTargetFrameworkToOutputPath>` ์ถ๊ฐ
  
