---
title: 再次学习C++
key: 20181021
date: 2018-10-21 16：17：30 +0800
tags: c++ 算法
---

~~很久很久很久前就有想学习编程的想法，然而太懒了，日子也还能凑合过......~~

于是书买了几本，没看完的，更也没认真写过什么东西。后来开始慢慢捡起来学，毕竟现在的工作没太多盼头，学习点东西打发时间或者作为预备技能也是挺好的。

目前主要是在[学堂在线](http://www.xuetangx.com/) 看看视频以及试着做做习题，等有一定基础再看看做什么东西吧。

然后作为一个初学者，我选择的语言大概不是很合适，或者说C/C++是一门对初学者不太友好的语言，尤其是C++。我个人觉得吧，如果C++学不进去，我就可以放弃学习编程了。
<!--more-->
之前断断续续看过郑莉老师 [C++语言程序设计基础](http://www.xuetangx.com/courses/course-v1:TsinghuaX+00740043X_tv+2015_T1/info) 以及[C++语言程序设计进阶](http://www.xuetangx.com/courses/course-v1:TsinghuaX+00740043_2X_tv+2015_T1/courseware) ，大概是时间断断续续拖太久了，感觉也没学到什么东西。

目前主要是在看[基于Linux的C++](http://www.xuetangx.com/courses/course-v1:TsinghuaX+20740084X+sp/courseware) ~~虽然我用的是windows~~ ，然后尽量将习题做出来之后再看后续视频，感觉上稍微舒服一点了。虽然算法、程序思维什么的基本没有，旁边一本<<C++ Primer 5th>>中文版看着就头大...不过总算是在继续了。

然后贴一下习题好了
>5.1编写函数，返回1~52之间的随机数。

这个题比较简单，就不单独说明了,具体代码也可以参考5.2的代码。

>5.2可以将上题生成的随机数模拟为不含大小王牌的扑克牌。编写函数，重复生成52个随机数，并映射为每张扑克牌。说明：重复生成的典型原则是按花色（梅花、方块、红桃、黑桃）和大小（2~10、J、Q、K、A）顺序进行映射，例如梅花2小于梅花3，......梅花A小于方块2，......黑桃K小于黑桃A。需要注意的是，一旦生成某张牌后，即不允许再次生成它，如何解决此问题？

最开始脑子没转过弯，把花色和大小拆开来想了，于是一直没想出来怎么解决这个问题......于是网上找了个答案

```c++
  /*模拟发不含大小王牌的扑克牌，
 将生成的随机数映射为每张扑克牌。
 按照花色(梅花、方块、红桃、黑桃)和大小(2~10、J、Q、K、A)顺序进行映射。
 映射规则如下:
 梅花:0-12
 方块:13-25
 红桃:26-38
 黑桃:39-51
 */
#include <cstdlib>
#include <ctime>
#include <iostream>
using namespace std;
int main()
{
    int i, sj;
    int shu[52] = {0};
    srand((int)time(NULL));
    for (i = 1; i <= 52; i++)
    {
        sj = rand() % 52;
        while (shu[sj] == 1)
        {
            sj = rand() % 52;
        }
        shu[sj] = 1;
        cout << "the " << i << " card" << endl;
        switch (sj % 4)
        {
        case 0:
            cout << "梅花";
            break;
        case 1:
            cout << "方块";
            break;
        case 2:
            cout << "红心";
            break;
        case 3:
            cout << "黑桃";
            break;
        default:
            break;
        }
        switch (sj % 13 + 1)
        {
        case 2:
        case 3:
        case 4:
        case 5:
        case 6:
        case 7:
        case 8:
        case 9:
        case 10:
            cout << sj % 13 + 1 << endl;
            break;
        case 1:
            cout << "A" << endl;
            break;
        case 11:
            cout << "J" << endl;
            break;
        case 12:
            cout << "Q" << endl;
            break;
        case 13:
            cout << "K" << endl;
            break;
        default:
            break;
        }
    }
    getchar();
    return 0;
}
```
然后自己写不出来就想办法把别人的看懂，然后看看有没有什么能改进的地方吧。我没绕过去的主要地方就是思路局限了，没有考虑把花色和序号组合成52个数，所以就一直没迈过这个坎。

上面的代码是通过设置一个52个int值为0数组，然后通过rand()%52取余的方式来获得0~51的随机数（共52个数），然后刚好对应上52张牌，每生成一张对应的牌（和数组的序号对应）之后，就将该位赋值为1，通过检查对应的值判断该牌是否已经生成来避免重复，然后就是比较简单的通过对4和13的取余判断对应花色和数字了，整体思路大概就这个样子。

通过分析对方的代码发现

```c++
 while (shu[sj] == 1)
        {
            sj = rand() % 52;
        }
```
这一段代码会反复判断，例如已经发了51张牌的情况下，rand()%52大概有51/52的几率都不成功，应该是一定程度上会影响执行效率，当然这个52数值太小，实际使用的时候没啥感觉，当量级扩大的时候可能会比较明显 ~~我都是随便猜的，没测试过~~

于是改进代码如下
```c++
/*模拟发不含大小王牌的扑克牌，
 将生成的随机数映射为每张扑克牌。
 按照花色(梅花、方块、红桃、黑桃)和大小(2~10、J、Q、K、A)顺序进行映射。
 映射规则如下:
 梅花:0-12
 方块:13-25
 红桃:26-38
 黑桃:39-51
 */
#include <cstdlib>
#include <ctime>
#include <iostream>
using namespace std;
void switchc(int &a, int &b)
{
    int c;
    c = a;
    b = a;
    a = c;
    return;
}
int main()
{
    int shu[52];
    //生成0~51的序列
    for (int i = 0; i <= 51; i++)
    {
        shu[i] = i;
    }
    srand((int)time(NULL));
    //随机化对数列进行switch（）
    for (int i = 0; i <= 51; i++)
    {
        int a = rand() % 52;
        if (a != i)
        {
            switchc(shu[i], shu[a]);
        }
    }
    for (int i = 0; i <= 51; i++)
    {

        switch (shu[i] % 4)
        {
        case 0:
            cout << "梅花";
            break;
        case 1:
            cout << "方块";
            break;
        case 2:
            cout << "红心";
            break;
        case 3:
            cout << "红桃";
            break;
        default:
            break;
        }
        switch (shu[i] % 13 + 1)
        {
        case 2:
        case 3:
        case 4:
        case 5:
        case 6:
        case 7:
        case 8:
        case 9:
        case 10:
            cout << shu[i] % 13 + 1 << endl;
            break;
        case 1:
            cout << "A" << endl;
            break;
        case 11:
            cout << "J" << endl;
            break;
        case 12:
            cout << "Q" << endl;
            break;
        case 13:
            cout << "K" << endl;
            break;
        default:
            break;
        }
    }
    getchar();
    return 0;
}
```

思路如下：
- 生成一个0~51按序排列的int数组，代表52张牌
- 通过for函数依次对int数组进行switchc()操作
- switchc()是对[i , rand()%52]的数组对应序号进行对换，也就是说每一次rand()操作都是有效的，除非rand()%52 == i
- 后续就是对乱序的数组进行判断，输出牌组

这种方式通过对原算法的调整，模拟了洗牌的过程，减少了算法中的无效操作，~~理论上~~感觉上应该是提升了程序效率。

**求有没有大佬教教我，我的思路到底对不对。**