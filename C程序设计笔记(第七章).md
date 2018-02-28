# 函数
直接分析书上的实例
```c
#include <stdio.h>

int main(){
    float add(float x, float y);        //函数声明
    float a,b,c;
    printf("Please enter a and b: ");
    scanf("%f,%f",&a,&b);
    c=add(a, b);
    printf("sum is %f\n",c);
    return 0;
}
float add(float x,float y){            //函数原型prototype
    float z;
    z=x+y;
    return z;
}
```
##  函数递归调用
递归调用类似于数学归纳法
满足年龄公式:  `age(n)=age(n-1)+2  (n>1)`,`age(n)=10  (n=1)`
```c
int main(){
    int age(int n);
    printf("NO.5 age: %d\n",age(5));
    return 0;
}

int age(int n){
    int c;
    if (n==1) {
        c=10;           //基线条件
    }else{
        c=age(n-1)+2;   //递归条件
    }
    return c;
}
```
实际过程是一次次得进行递归调用，到`age(1)`有确定值，再逐个递推出`age(2)`...

用递归实现阶乘
```c
#include <stdio.h>

int main(){
    int n,m;
    int fact(int n);
    printf("input an integer number: ");
    scanf("%d",&n);
    m=fact(n);
    printf("factorial is: %d\n",m);
    return 0;
}

int fact(int n){
    int f;
    if (n<0)
        printf("n<0, data errorI");
    else if(n==0)
        f=1;
    else
        f=n*fact(n-1);
    return f;
}
```
[汉诺塔问题Hanoi](https://baike.baidu.com/item/汉诺塔/3468295?fr=aladdin)
```c
#include <stdio.h>

int main(){
    void hanoi(int n,char one,char two,char three);
    int m;
    printf("input the number of diskes: ");
    scanf("%d",&m);
    printf("The step to move %d diskes:\n",m);
    hanoi(m,'A', 'B', 'C');
}

void hanoi(int n,char one,char two,char three)     //定义hanoi
//将n个盘从one座，借助two座，移到three座
{
    void movepl(char x,char y);                    //声明movepl
    if (n==1)
        movepl(one, three);
    else{
        hanoi(n-1, one, three, two);
        movepl(one, three);
        hanoi(n-1, two, one, three);
    }
    
}

void movepl(char x,char y){
    printf("%c->%c\n",x,y);
}
```
总共移动次数：`2^n-1`
## 数组作为函数参数
###数组元素作函数实参
形参在函数被调用时临时分配储存单元。实参给形参传数据用的是“值传递”方式。

求十个数中最大数
```c
#include <stdio.h>

int main(){
    int max(int x,int y);
    int a[10],m,n,i;
    printf("enter 10 integer numbers: ");
    for (i=0; i<10; i++)
        scanf("%d",&a[i]);
    printf("\n");
    for (i=0,m=a[0],n=0;i<10 ; i++) {
        if (max(m, a[i])>m) {
            m=max(m, a[i]);
            n=i;
        }
    }
    printf("largest number is %d.\nIt is the %dth number\n",m,n+1);
}

int max(int x,int y){
    return (x>y?x:y);
}
```
### 数组名作也函数参数
求平均分数
```c
#include <stdio.h>

int main(){
    float average(float array[10]);
    float score[10],aver;
    int i;
    printf("input 10 scores: \n");
    for (i=0; i<10; i++) {
        scanf("%f",&score[i]);
    }
    printf("\n");
    aver=average(score);                     //调用average()
    printf("average score is %5.2f\n",aver);
    return 0;
}

float average(float array[10]){
    //可以不定义数组大小，因为是形参
    int i;
    float aver,sum=array[0];
    for (i=1; i<10; i++)
        sum=sum+array[i];
    aver=sum/10;
    return aver;
}
```
`array[0]`,`score[0]`共用同一地址，向形参传递的是首元素地址

选择排序
```c
#include <stdio.h>

int main(){
    void sort(int array[],int n);
    int a[10],i;
    printf("enter array: \n");
    for (i=0; i<10; i++) {
        scanf("%d",&a[i]);
    }
    sort(a, 10);
    printf("The sorted array: \n");
    for (i=0; i<10; i++) {
        printf("%d",a[i]);
    }
    printf("\n");
    return 0;
}

void sort(int array[],int n){   //以一个形参代表数组元素个数
    int i,j,k,t;
    for (i=0; 1<n-1; i++) {
        k=i;
        for (j=i+1; j<n; j++)
            if (array[j]<array[k]) {
                k=j;                   //找出最小的数
            }
        t=array[k];array[k]=array[i];array[i]=t;    //与首个数交换
    }
}
```
## 变量的作用域和生命周期
局部变量：在函数内，形参，符合语句
```c
int main(){
    int a=3;
    {
        int b=6;
    }
    printf("%d\n",b);  //error
}
```
静态储存方式：程序运行期间由系统分配固定的存储空间方式
动态存储方式：程序运行期间根据需要进行动态分配存储空间额方式

内存供用户使用的存储空间有三部分：程序区、静态存储区、动态存储区

全局变量存放在静态存储区中，函数执行完毕释放。

动态存储区存放：1.函数形参，调用时分配存储空间 2.函数中定义的没用关键字`static`声明的变量，即自动变量 3.函数调用时的现场保护和返回地址等
函数开始时分配动态存储空间，结束时释放。
###  局部变量的存储类别
每个变量和函数都有两个属性：1.数据类型 2.数据的存储类别

1. 自动变量`auto`：
函数中的形参和在函数中定义的局部变量
调用函数时分配存储空间，执行完调用函数自动释放存储空间。

2. 静态局部变量`static`：
函数调用结束，继续保留原值，占有存储空间不释放。但其他函数不能引用它，只能本函数引用。
⚠️是保留原值，不重新赋值。
```c
#include <stdio.h>

int main(){
    int a=3,i;
    for (i=1; i<5; i++) {
        printf("%d\n",f(a));    //5,6,7,8
    }
}

int f(int x){
    static int c=1;
    ++c;
    return (x+c);
}
```
3.  寄存器变量`register`
由于使用频繁，存储在寄存器中提高效率。
> 实际中一般是寄存器中保存地址，CPU通过寄存器找到挂在数据总线上的内存，来读写数据。

###  全局变量的存储类别
全局变量都是存放在静态存储区中

在文件内扩展外部变量的作用域：
```c
#include <stdio.h>

int main(){
    int max();
    extern int A,B,C;   //把外部变量A，B，C的作用域拓展到从此处开始
    printf("Please enter three interger number: ");
    scanf("%d %d %d",&A,&B,&C);
    printf("max is %d\n",max());
    return 0;
}

int A,B,C;  //定义外部变量

int max(){
    int m;
    m=A>B?A:B;
    if (C>m) m=C;
    return m;
}
```

将外部变量的作用域拓展到其他文件：
```c
#  file1.c
int A;
#  file2.c
extern A;  //将A扩展到本文件中
```
将外部变量的作用域限制在本文件中：
```c
#  file1.c
static int A;  //静态外部变量，只能用于本文件
```
>  ⚠️对局部变量用static声明，把它分配在静态存储区；对全局变量用static声明，该变量作用域只限于本文件模块
###  变量的定义和声明
```c
int main(){
  int  A;  //定义：建立存储空间的声明
  extern  A;//声明：不需要建立存储空间的声明
}
```
###  内部函数和外部函数
内部函数又称静态函数，不能被其他文件调用
```c
static int func(int a, int b){}  //内部函数
```
外部函数：
```c
#  file1.c
int func(int a, int b){}  //内部函数
#  file2.c
extern nt func(int a, int b);
```
