---
title: 挑战五：How to Get Highest Score in Garupa (I)
author: Ball-lightnings
date: 2020-01-28
permalink: /challenge-5-I
key: challenge-5-I
sharing: true
license: true
aside:
  toc: true
show_edit_on_github: true
show_subscribe: true
pageview: true
---

### 说在前面

~~在开始之前，先推销一波邦邦。~~

[BanG Dream](https://zh.moegirl.org/BanG_Dream!)，是Bushiroad推出的~~可以推很多团又不用担心自己成为DD的~~企划。

[邦邦手游](https://game.bilibili.com/bangdream/)的国服也即将迎来一周年。希望喜欢音游的都可以去尝试一下。邦邦总体来说难度适中，容易上手，玩几天就能轻松fc六兆年（大嘘）。

但是不论如何：

“bangdream猛男乐团派对是我玩过最好玩的手游，游戏没有任何缺点，维护时间长是为了更好的游戏体验，下午维护也很正常只是我没见过世面，游戏卡顿是我手机问题，游戏服务器如丝般顺滑，游戏体力不多为了保护视力减少盯屏幕时间，一首歌3体刷本高效又快捷，不仅如此官方还大量更新可爱角色美丽皮肤，活动奖励丰富又不肝，每个池都有保底非酋体验极好，是手游之鉴，游戏界面非常整洁，角色立绘十分可爱，live2d做得也很精致，歌曲难度适中，游戏体验非常好。打不了的歌都是我手和脑子不配，出不了up四星都是风水不好，联机掉线是我家路由器没买对。”

### 言归正传

众所周知，音游的得分是由你打的好坏决定的（废话）。

包括邦邦在内的大多数音游的判定机制都是按照击打note的误差时间来评价，分为以下五个等级：perfect、great、good、bad、miss。

打出连续的perfect和great称为Combo，而往往单个note得分也有Combo数的加成，还与主乐队综合能力与曲目难度有关，具体值如下：

<div align="center"><img src="https://s1.ax1x.com/2020/03/13/8mLMwQ.jpg"><br>引自b站专栏，原作者@BuG_Dream</div>

可以看出，基础分数是与曲目有关的常数，而总得分是所有note数之和，即有

$$S_k=\sum_{i=1}^kM_i$$
 
其中$k$为总note数，$S_k$为总得分，$M_i$（$i\in[1,k], i\in\mathbb N$）为第$i$个单个note得分。

知道这些知识后，我们就可以开始出题了。

### バンドリ~~力儿八~~对邦挑战

户山香澄是Poppin' Party(下称popipa~~破琵琶~~）的主唱和吉他手，有一天，她打算搞点kirakira的事情，于是决定在Live House CiRCLING举办一场dokidoki的对邦，并向Roselia和Afterglow发出挑战，~~要把她们做成团子，~~还请来自己的~~后宫粉丝~~好友，Galaxy的朝日六花来当评委。

然而对邦前一天，香澄突然怂了，担心比不过R组~~她们会说相声~~和~~夕阳红~~AG。于是她恳求市谷有咲帮她计算一下最高得分。

假设popipa基础得分为$a$，准度为$p$，一首曲子note总数为$k$，miss数为$x$，其中$p=\dfrac{N(perfect)}k$，$N(perfect)$为perfect数。请你帮有咲计算一下，popipa的极限高分和翻车低分分别是多少。

以~~黑叫~~Black Shout为例：

**样例输入**

    2000 0.93 506 3

**样例输出**

    1142976.000
    gr33-p470-m3
    1104444.000
    p144-m-p108-m-p112-gr4-m-p106-gr29

~~为什么你们破琵琶要唱R组的歌啊kora！~~
