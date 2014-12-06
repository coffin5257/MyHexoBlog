title: 数据结构课程设计
tags:
  - 水题
id: 42
categories:
  - 数据结构与算法
date: 2014-06-24 14:00:15
---

上学期的课这学期做课设- - 运气好抽到简单题哈哈~~
不定时(看心情)更新其它题目，当练手 :-)
p.s 推酷等转载的站点基本不会更新作者的后续更改，所以，请看到的朋友还是来这里（http://www.coffin5257.com）吧~

<!--more-->

## 猴子吃桃子问题

有一群猴子摘了一堆桃子，他们每天都吃当前桃子的一半且再多吃一个，到了第10天就只余下一个桃子。用多种方法实现求出原来这群猴子共摘了多少个桃子。
要求：

1.  采用数组数据结构实现上述求解
2.  采用链数据结构实现上述求解
3.  采用递归实现上述求解
4.  如果采用4种方法者,适当加分

```C
// Array

#include<stdio.h>
int main()
{
    int a[11],i;
    a[9]=1;
    for(i=9;i&gt;=0;i--){
        a[i-1]=(a[i]+1)*2;
        printf("第%d天的桃子为%dn",i+1,a[i]);
    }
    return 0;
}
```

```C
// Linked List

#include<stdio.h>
#include

typedef struct LNode
{
    int data;
    struct LNode *next;
} LNode,*LinkList;

int main()
{
    int i;
    LinkList L,p;
    L=(LinkList)malloc(sizeof(LNode));
    L-&gt;next=NULL;
    p=(LinkList)malloc(sizeof(LNode));
    p-&gt;data=1;
    p-&gt;next=L-&gt;next;
    L-&gt;next=p;
    for(i=1;i&lt;10;i++) 
    { 
        p=(LinkList)malloc(sizeof(LNode));
        p-&gt;data=2*(L-&gt;next-&gt;data+1);
        p-&gt;next=L-&gt;next;
        L-&gt;next=p;
        printf("第%d天的桃子为%dn",10-i,L-&gt;next-&gt;data);
    }
    return 0;
}
```

```C
//Recursion

#include<stdio.h>

int monkey_recursion(int n);

int main()
{
    int result=monkey_recursion(1);
    printf("%d",result);
    return 0;
}

int monkey_recursion(int n)
{
    //int result;
    if(n==10)
       return 1;
    return (monkey_recursion(n+1)+1)*2;
}
```

```C
// Queue

#include<stdio.h>

typedef struct queue
{
    int data[11];
    int front,rear;
} queue;

int main()
{
    int i;
    queue monkey;
    monkey.front=monkey.rear=0;
    int tmp=1;
    for(i=10;i&gt;0;i--)
    {
        monkey.data[monkey.rear++]=tmp;
        tmp=2*(tmp+1);
    }
    while(monkey.front!=monkey.rear)
        printf("第%d天的桃子为%dn",11-monkey.front,monkey.data[monkey.front++]);
    return 0;
}
```

**哟～听说课设答辩已经完了呢......那就让它变成一个坑好了- -**