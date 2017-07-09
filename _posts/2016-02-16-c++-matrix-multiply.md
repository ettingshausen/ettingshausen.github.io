---
layout: post
title:  "C++两个矩阵相乘"
date:   2016-02-16 09:09:06 +0800
categories: matrix
---

```c++
/*
编程求两个矩阵相乘的结果。输入第一行是整数m,n,表示第一个矩阵式m行n列的；然后是一个m * n的矩阵。
再下一行的输入时整数p,q，表示下一个矩阵p行,q列的（n=p）;然后就是一个p行q列的矩阵。
要求输出两个矩阵相乘的结果矩阵（1<m、n、p、q<=8）.
P82页
2014年10月3日21:32:23
*/
#include <iostream>
using namespace std;
const int size = 10;
void init(int *, int *, int a[][size]);//初始化数组
void multi(int, int, int, int, int a[][size], int b[][size], int result[][size]);
void print(int, int, int result[][size]);
int main()
{
    int m, n, p, q;
    int a[size][size];
    int b[size][size];
    int result[size][size] = { 0 }; //将保存结果的数组初始化为0；
    /*
    int a[size][size] = { { 2, 4, 5 }, { 2, 1, 3 } };  //初始化数组
    int b[size][size] = { { 1, 1, 1 }, { 2, 3, 2 }, { 0, 1, 4 } };  //初始化数组
    m = 2;
    n = 3;
    p = q = 3;
    */
    init(&m, &n, a);
    init(&p, &q, b);
    multi(m, n, p, q, a, b, result);
    //print(m, n, a);   检查输入矩阵
    //print(p, q, b);
    print(m, q, result);
    system("pause");
    return 0;
}
void multi(int m, int n, int p, int q, int a[][size], int b[][size], int result[][size])
{
    if (n == p) //第一个矩阵的列数与第二个矩阵的行数相等时，两个矩阵才能相乘；
        for (int i = 0; i < m; i++)  //a的行数
        {
            for (int j = 0; j < q; j++)      //b的列数
            {
                for (int k = 0; k < n; k++)  //a的列数
                {
                    result[i][j] += a[i][k] * b[k][j];
                }
            }
        }
    else
        printf("行数不匹配\n");
}
void print(int m, int q, int result[][size])
{
    for (int i = 0; i < m; i++)
    {
        for (int j = 0; j < q; j++)
        {
            printf("%d\t", result[i][j]);
        }
        printf("\n");
    }
}
void init(int *pm, int *pn, int a[][size])
{
    cin >> *pm >> *pn;
    for (int i = 0; i < *pm; i++)
    {
        for (int j = 0; j < *pn; j++)
        {
            cin >> a[i][j];
        }
    }
}
```