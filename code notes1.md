> 好久没有用markdown了，浅浅复健一下，然后写写最近学习的笔记w  

### 题目中的情景

1. 如果没有说输入多少次，用

   ```c++
   while(cin >> n >> m){}
   //如果对输入有限制，比如int >0
   while(cin >> n >> m, n > 0 && m > 0){}
   ```


2. 行末换行

   ```c++
   // puts("");
   for(int i = 1; i <= n; i++)
       {
           for(int j = 1; j <= m; j ++) printf("%d ", b[i][j]);
           puts("");
       }
   ```

3. 四舍五入

   ```c++
   //因为不在Math.h里，所以自己写来实现
   double Round(double x)
   {
       return (x>0.0) ? (x+0.5) : (x-0.5);
   }
   
   int main(void)
   {
       Round(n/2.0)//调用函数
   }
   ```

4. 运算
  ```c++
  #include<math.h>
  pow(x,n); //x的n次方
  pow(x,1/n);//x开n次方
  sqrt(x);//x的平方根
  abs();//整数绝对值
  fabs();//浮点绝对值
  ```

5. printf() 和 scanf()
  ```c++
  //用 c_str();
  #include<string.h>
  //输入字符串
  scanf("%s",a[i].c_str());
  printf("%s", a[i].c_str());
  //输入数组
  scanf("%d",&a[i]);
  printf("%d",&a[i]);
  ```

6. 排序
```c++
	#include<algorithm>
	sort(begin, end);//默认升序
	//加上判断，可以改成降序
	int/bool compare(int a, int b)
	{
		return a < b;//升序，降序是 a > b; 
	}
	sort(begin, end, compare);
```
6. 退出循环
    break 是退出循环
    continue 是开始循环的新一次迭代，不是退出循环
7. 矩阵
```c++
for(int j = 0; j < row; j++) //row行，col列
    {
        int i = col;
        while(i--) cin >> a[j][i];
    }
```
8. 字符串插入（两个字符串，某个安到另一个的里面）
```c++
#include <iostream>
using namespace std;

int main()
{
    string s,sub;
    while(cin >> s>>sub)
    {
        int idx=0;
        for(int i=0;i<s.size();i++)
            if(s[i] > s[idx])
            {
                idx= i;////字符串中的最大值
            }
        s.insert(idx+1,sub);
        cout<<s<<endl;
    }
}
```
9. 阶乘
```c++
int fact(int n)
{
    if(n == 1) return 1;
    return n * fact(n-1);
}
```
10. 斐波那契数列(注意序号是从0/1开始)
```c++
 int Fibonacci(int n) {
        if(n <= 1) return n;
        return Fibonacci(n-1) + Fibonacci(n-2);
    }
```
11. 判断输出
```c++
cout << (x % 2 ? x : x+=1);
printf("X[%d] = %d\n", i, a <= 0 ? 1 : a);
//应该就是一个判断的简写
// 条件 ? 结果1 ：结果2
```
12. 字符串，字符数组
```c++
#include<string.h>

//输入包含空格
char str[100];
	fgets(str, 100, stdin); //注意fgets不会删除行末的回车字符

string s;
    getline(cin, s);

//求长度
strlen(str)；

//比较字符串大小(数组，string可以直接> < >= <= ...)
strcmp(a, b)；
//a < b返回-1，a == b返回0，a > b返回1
//这里的比较方式是字典序！
```

13. 约数，质数（需要整除之类的）优化

```c++
//把"让所有数都循环一遍"，优化
//判断数字x 的约数/质数
//原本是
for(int i = 1; i <= x; i ++)
//可以改成(这样不包含1 和 本身)
for(int i = 2; i <= x/i; i++)
// x/i 的意思，当约数是i时的另一个约数，只求比较小的那个约数（i）,大的那个就可以通过（x/i）知道，不需要循环到大的那个，所以判断 i <= x/i 就ok,取等号是因为存在两个约数相同的情况
```

14. 求排列

    ```c++
    #include<algorithm>
    next_permutation(a.begin(), a.end)//a是字符串或者动态数组
    next_permutation(a+1, a + n + 1)//a是数组，n是数组大小（存成从1开始，所以a + 1)
        //返回下一个字典序排列，而且是改变原数组的
    ```

### 一些小模块的细节

+ 位运算（比较快）

  ```
  >>1  等价于 /2
  <<1  等价于 *2
  ```

+ 科学记数

  ```
  1e6 等价于 1*10^6
  ```

+ 遍历（还不太懂）for(auto a:b)

+ 输出特殊字符

  ```
  %% \\ \' \" (都是输出右边的符号)
  ```

+ 输出左右对齐

  默认左对齐，右对齐在%和d之间加上数字，表述字符长度

  还可以加上补齐的内容，比如0

  %010d(用0 补齐，字符长度为10，右对齐)
  
+ 数学公式
```
  等差数列：an = a1 + (n-1)d;

  Sn = n*a1 + n(n-1)d/2 或 n(a1 + an)/2

  等比数列：an = a1*q^(n-1)

  Sn = a1(1-q^n)/1-q
```
+ int， long long int
  占位符是```c++ "%lld" ```
  
+ 判断奇数
  if（i % 2)  // i % 2 = 1
  
  或者if(n & 1)//位运算，二进制最后一位和1相同，所以是奇数
  
+ 大写与小写的转换