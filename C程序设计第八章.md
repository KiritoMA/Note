#  指针
程序经过编译后已经将变量名转换为变量的地址
对变量的存取都是通过地址进行的
```c
//输入值送到的是地址为2000开始的整型存储单元
scanf("%d",&i);
k=I+j;
//从2000-2003字节取出i的值
//从2004-2007取出字节j的值
//相加后送到k所占用的2008-2011字节中
```
变量名与变量地址有一一对应关系

直接访问：直接按变量名进行访问
间接访问：将变量i的地址存放在另一变量中，然后通过该变量来找到i的地址，从而访问变量i

```c
i_pointer=&i;  //  将I的地址存放到i_pointer中
```
地址即“指针”，地址指向变量单元。例如，地址2000是变量i的指针。

指针变量：一个变量专门用来存放另一变量的地址
地址指向该变量单元，就是通过地址能找到所需的变量单元
```c
#include <stdio.h>

int main(){
    int a=100,b=10;
    int *pointer_1, *pointer_2; //定义指针变量，*表示该变量是指针变量
    pointer_1=&a;               //pointer_1指向a
    pointer_2=&b;
    printf("*pointer_1=%d, *pointer_2=%d\n",*pointer_1,*pointer_2);
    //* 表示指向，代表pointer_1所指向的变量
    return 0;
}
```
##  指向函数的指针
```c
#include <stdio.h>

int main(){
    int max(int,int);
    int a,b,c;
    printf("please enter a and b: ");
    scanf("%d,%d",&a,&b);
    c=max(a, b);
    printf("a=%d\nb=%d\nmax=%d\n",a,b,c);
    return 0;
}

int max(int x,int y){
    int z;
    if (x>y)    z=x;
    else    z=y;
    return z;
}
```
```c
#include <stdio.h>

int main(){
    int max(int,int);
    int (*p)(int,int);  //  定义指向函数的指针变量p
    int a,b,c;
    p=max;
    printf("please enter a and b: ");
    scanf("%d,%d",&a,&b);
    c=(*p)(a,b);        //通过指针变量，调用max
    printf("a=%d\nb=%d\nmax=%d\n",a,b,c);
    return 0;
}

int max(int x,int y){
    int z;
    if (x>y)    z=x;
    else    z=y;
    return z;
}
```
