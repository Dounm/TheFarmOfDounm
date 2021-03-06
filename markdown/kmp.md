﻿---
title: "KMP算法详细理解和推导"
date: 2017-04-10T23:46:08+08:00
tags: [KMP]
categories: [Algorithm]
toc: true
---

## 1. 字符串匹配的朴素做法
（称被匹配串为`str`，匹配串为`pat`）
朴素的字符串匹配算法就是每次将`pat`后移一位，然后继续从`pat`的第一位开始比较。

但其实我们已经获取了信息**『不匹配位置的前面的字符都是匹配的』**.
朴素做法并没有利用到这个信息，而KMP就是要利用这个信息加快速度。

## 2. KMP如何利用已有信息

1. 首先`pat`必然是要后退，关键在于后退几位。
2. 设我们已经知道匹配的子串是『ABCADAB』，这代表了两层含义：
  1. `pat`的前7位是『ABCADAB』，`pat`是『ABCADABxxx』
  2. `str`在当前匹配位置的7位子串是『ABCADAB』，`str`是『xxxABCADABxxx..xx』
3. 若用朴素的做法将`pat`后退的过程中：
  1. 后退1步：`str`在当前匹配位置是『BCADABxxxx』，`pat`是『ABCADABxxx』，第一位就不匹配
  2. 后退2步：`str`是『CADABxxxxx』，`pat`是『ABCADABxxx』，第一位就不匹配
  3. 后退3步：`str`是『ADABxxxxxx』，`pat`是『ABCADABxxx』，第一位匹配，第二位不匹配
  4. 后退4步：`str`是『DABxxxxxxx』，`pat`是『ABCADABxxx』，第一位就不匹配
  5. 后退5步：`str`是『ABxxxxxxxx』，`pat`是『ABCADABxxx』，第一位和第二位都匹配，后面是否匹配无法根据现有信息得到，需要进一步获得`str`后续的子串是什么。
4. 我们可以发现：朴素的匹配算法中后退1-4步都是不匹配的，而这4种不匹配的情况都可以根据已得信息（即步骤2获得信息）判断出来其不匹配。当后退5步的时候，无法根据已得信息得知是否匹配，就需要进一步进行匹配。
5. 而KMP算法的目标就是**利用已得信息（即步骤2获得的信息）来消除后退1-4步这些无用匹配**。

## 3. KMP的具体实现

1. 那么KMP具体怎么实现的呢？
  1. 仔细观察上例发现，当匹配的子串是『ABCADAB』时，后退1-4步无效，可以直接跳到后退5步
  2. 根据3.e的情况，后退5步时，`str`是『ABxxxxxxxx』，开头的两个字符是『ABCADAB』的后两位后缀『AB』。而`pat`是『ABCADABxxx』，前两位前缀正好是『AB』。
  3. 再结合后退1-4步的情况，我们发现所有后退的情况，`str`的开头都是匹配的子串『ABCADAB』的后缀；`pat`都是『ABCADAB』的前缀。
  4. 因此，只要我们遍历『ABCADAB』的所有的`<前缀，后缀>`对，找到相等且最长的前后缀对即可。
  5. 对于『ABCADAB』，相等且最长的前后缀对就是`<前缀=AB，后缀=AB>`，长度为2。而『ABCADAB』长度为7，因此我们可以直接后退7-2=5步即可。
2. 因此，KMP的思想就是：**找到匹配子串相等且最长的前后缀对，然后用匹配子串的长度减去前后缀对的长度，即得到要后退的步数**。
3. 注意，匹配的子串必然是`pat`的前缀，而且`pat`的每一个前缀都有可能是匹配的子串。所以我们只需提前对`pat`子串进行一次处理即刻，记录下`pat`的每个前缀作为匹配的子串时，相等且最长的前后缀对的长度即可。（即代码里的`next`数组）

## 4. KMP的实现代码

附上我用`Python`实现的KMP算法的代码：[Dounm/Fun-Programs/string-matching/kmp.py](https://github.com/Dounm/Fun-Programs/blob/master/string-matching/kmp.py)



