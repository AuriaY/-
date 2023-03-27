## 一. 基础算法

> 包括排序、二分、高精度、前缀和与差分、双指针算法、位运算、离散化、区间合并等内容。

### 快速排序

> 找出一个参考值，将数组分为两个，再排序两个数组

```C++
#include<iostream>

using namespace std;

const int N = 1e6 + 10;
int n, q[N];

void quick_sort(int q[], int l, int r)
{
	if(l >= r) return; 
	//判断边界，如果只有一个数或者没有数在里面就不用排序了
	int mid = (l + r)/ 2;
	int x = q[mid], i = l - 1, j = r + 1;
	//建立两个指针，判断指针所指的值与所选参考值的关系
	while(i < j)
	{
		do i ++; while(q[i] < x);
		do j --; while(q[j] > x);
		if(i < j) swap(q[i], q[j]);
	}
	//运用递归处理分好的两个部分
	quick_sort(q, l, j);
	quick_sort(q, j + 1, r);
}	

int main(void)
{
	scanf("%d", &n);
	for(int i = 0; i < n; i++) scanf("%d", &q[i]);
	
	quick_sort(q, 0, n - 1);
	
	for(int i = 0; i < n; i ++) printf("%d ", q[i]);
	return 0;
	
}
```

### 归并排序

> 将数组从中间分为两个，将两个数组排序再从头对比，取数放到结果数组里面，当一个指针指到结尾时，另一个数组内剩余的部分直接全部加到结果数组里

```c++
#include<iostream>

using namespace std;

const int N = 1e6 + 10;
int n, q[N], tmp[N];//需要一个额外的数组来存结果

void merge_sort(int q[], int l, int r)
{
	if(l >= r) return;
	int mid = l + r >> 1;
	merge_sort(q, l, mid);
	merge_sort(q, mid + 1, r);
	int k = 0;//用来记录tmp中输入了多少个数字
	int i = l, j = mid + 1;
	while(i <= mid && j <= r)//两个指针移动的边界
	{
		if(q[i] < q[j]) tmp[k++] = q[i++]; //是先将k, i赋值然后再自增，所以没问题
		else tmp[k ++] = q[j ++];
	}
	while(i <= mid) tmp[k ++] = q[i ++];//判断哪个数组没取完
	while(j <= r) tmp[k ++] = q[j ++];
	
	for(int i = l, j = 0; i <= r; i++, j++) q[i] = tmp[j];
	//将tmp里的答案复制到q里
}

int main(void)
{
	scanf("%d", &n);
	for(int i = 0; i < n; i++) scanf("%d", &q[i]);
	
	merge_sort(q, 0, n - 1);
	
	for(int i = 0; i < n; i++) printf("%d ", q[i]); 
	
	return 0;
}
```

### 整数二分

> 本质是边界，左边满足某性质，右边满足某性质，二分寻求性质的边界，保证答案在边界内

```c++
#include<iostream>

using namespace std;

const int N = 1e6 + 10;
int n, m, q[N];

int main(void)
{
	scanf("%d%d", &n, &m);
	for(int i = 0; i < n; i ++) scanf("%d", &q[i]);
	
	while(m --)//查询边界
	{
		int x;
		scanf("%d", &x);//所查答案（满足的条件）
		int l = 0, r = n - 1;//边界
		while(l < r)
		{
			int mid = l + r >> 1;
			if(x <= q[mid])r = mid;
			else l = mid + 1;//缩小范围
		}
		if(q[l] != x)printf("-1 -1\n");
		else
		{
			printf("%d ", l);
			while(l < r)
			{
				int mid = l + r + 1>> 1;//求右边时+1（r...出现-1，则+1）
				if(x >= q[mid])l = mid;
				else r = mid - 1;
			}
			printf("%d\n", l);
		}
		
	}
	return 0;
}
```

### 高精度

#### 高精度加法

```c++
#include<iostream>
#include<vector>
#include<string.h>

using namespace std;

vector<int> add(vector<int> &A, vector<int> &B) //要加 “ & ”
{
	vector<int> C;
	int t = 0;
	for(int i = 0; i < A.size() || i < B.size(); i++)
	{
		if(i < A.size()) t += A[i];
		if(i < B.size()) t += B[i];
		C.push_back(t % 10);
		t /= 10;
	}
	if(t) C.push_back(1); //如果t到最后存在，就再进一位
	return C;
}

int main(void)
{
	string a, b;
	vector<int> A, B;
	
	cin >> a >> b;// a = "123456"
	// 倒序读入
	for(int i = a.size() - 1; i >= 0; i--) A.push_back(a[i] - '0'); //A = {6,5,4,3,2,1};
	for(int i = b.size() - 1; i >= 0; i--) B.push_back(b[i] - '0');
	
	auto C = add(A, B);
	
	for(int i = C.size() - 1; i >= 0; i--) printf("%d", C[i]);
}
```

#### 高精度减法

```c++
#include<iostream>
#include<vector>
#include<string.h>

using namespace std;

bool cmp(vector<int> A, vector<int> B)
{
	if(A.size() != B.size()) return A.size() > B.size();
	for(int i = A.size() - 1; i >= 0; i--) //倒着判断，从最高位开始
	{
		if(A[i] != B[i]) return A[i] > B[i];
	}
	return true;
}

vector<int> sub(vector<int> &A, vector<int> &B) //要加 “ & ”
{
	vector<int> C;
	int t = 0;
	for(int i = 0; i < A.size(); i++)
	{
		t = A[i] - t;
		if(i < B.size()) t -= B[i];//计算
		C.push_back((t+10) % 10);//结果
		if(t < 0) t = 1;//t的状态，这里是借位，所以t=1,下一次循环减一
		else t = 0;
	}
	while(C.size() > 1 && C.back() == 0) C.pop_back(); //删前导0
	return C;
}

int main(void)
{
	string a, b;
	vector<int> A, B;
	
	cin >> a >> b;// a = "123456"
	// 倒序读入
	for(int i = a.size() - 1; i >= 0; i--) A.push_back(a[i] - '0'); //A = {6,5,4,3,2,1};
	for(int i = b.size() - 1; i >= 0; i--) B.push_back(b[i] - '0');
	
	//和加法不同，判断一下a,b的大小，正负情况
	if(cmp(A, B))
	{
		auto C = sub(A, B);
		for(int i = C.size() - 1; i >= 0; i--) printf("%d", C[i]);
	}
	else
	{
		auto C = sub(B, A);
		
		printf("-"); //输出负号
		
		for(int i = C.size() - 1; i >= 0; i--) printf("%d", C[i]);
	}
	return 0;
}
```

#### 高精度乘法

```c++
#include<iostream>
#include<vector>
#include<string.h>

using namespace std;

vector<int> mul(vector<int> &A, int b) //要加 “ & ”
{
	vector<int> C;
	int t = 0;
	for(int i = 0; i < A.size() || t; i++)
	{
		if(i < A.size()) t += b * A[i];//计算
		C.push_back(t % 10);//结果
		t /= 10;//t的状态
	}
	while(C.size() > 1 && C.back() == 0) C.pop_back(); //循环删前导0，while
	return C;
}

int main(void)
{
	string a;
	int b;//小的数乘以高精度
	vector<int> A;
	
	cin >> a >> b;// a = "123456"
	// 倒序读入
	for(int i = a.size() - 1; i >= 0; i--) A.push_back(a[i] - '0'); //A = {6,5,4,3,2,1};
	
	auto C = mul(A, b);
	for(int i = C.size() - 1; i >= 0; i--) printf("%d", C[i]);
	return 0;
}
```





### 前缀和

> 求区间范围的和

```c++
#include<iostream>

using namespace std;

const int N = 1e5 + 10;
int a[N], s[N];

int main(void)
{
    int n, m;
    cin >> n >> m;
    for(int i = 1; i <= n; i ++) //下标从1开始，避免越界
    {
        scanf("%d", &a[i]);
        s[i] = s[i - 1] + a[i];//初始化前缀和
    }
    while(m --)
    {
        int l ,r;
        cin >> l >> r;
        printf("%d\n", s[r] - s[l -1]);//所以这里要-1
    }
}
```

### 差分

> 在一段数据上，都加上x

```c++
#include<iostream>

using namespace std;

const int N = 1e5 + 10;
int a[N], b[N];

int insert(int l ,int r, int c)
{
    b[l] += c;
    b[r + 1] -= c;
}

int main(void)
{
    int n, m;
    cin >> n >> m;
    for(int i = 1; i <= n; i++) //从1开始，i <= n
    {
        scanf("%d", &a[i]);
        insert(i, i, a[i]);
    }
    
    while(m --)
    {
        int l, r, c;
        cin >> l >> r >> c;
        insert(l, r, c);
    }
    //b[i]是a[i]的差分，b[i] = a[i] + a[i -1]
    for(int i = 1; i <= n; i ++) b[i] += b[i - 1];//b[N]里面存了a[i],其实是求a[i]
    for(int i = 1; i <= n; i ++) printf("%d ", b[i]);
}
```

### 离散化

```c++
#include<iostream>
#include<vector>
#include<algorithm>

using namespace std;

typedef pair<int, int> PII;
const int N = 300010;
int n, m;
int a[N], s[N];
vector<int> alls;
vector<PII> add, query;

int find(int x)
{
	int l = 0, r = alls.size() - 1;
	while(l < r)
	{
		int mid = l + r >> 1; //二分，mid写在里面
		if(alls[mid] >= x) r = mid; //保证答案在区间内
		else l = mid + 1;
	}
	return r + 1;
}

int main(void)
{
	cin >> n >> m;
	for(int i = 0; i < n; i++)
	{
		int x, c;
		cin >> x >> c;
		add.push_back({x, c});
		alls.push_back(x);
	}
	for(int i = 0; i < m; i++)
	{
		int l, r;
		cin >> l >> r;
		query.push_back({l ,r});
		alls.push_back(l);
		alls.push_back(r);
	}
	//去重
	sort(alls.begin(), alls.end());
	alls.erase(unique(alls.begin(), alls.end()), alls.end());
	
	//处理插入
	for(auto item : add)
	{
		int x = find(item.first);
		a[x] += item.second;
	}
	//预处理前缀和
	for(int i = 1; i <= alls.size(); i++) s[i] = s[i - 1] + a[i]; // 前缀和从1开始
	
	//处理询问
	for(auto item : query)
	{
		int l = find(item.first), r = find(item.second);
		cout << s[r] - s[l - 1] << endl;
	}
	return 0;
}
```

## 二. 数据结构

### 单链表（邻接表）

> 用数组模拟比用动态数值vector快

```c++
#include<iostream>

using namespace std;

const int N = 1e6 + 10;
int m;
int e[N], ne[N], idx, head;

int main(void)
{
	scanf("%d", &m);
	
	head = -1;//初始化头节点
	
	for(int i = 0; i < m; i++)
	{
		char op;
		cin >> op;
		if(op == 'H')
		{
			int x;
			cin >> x;
			e[idx] = x, ne[idx] = head, head = idx ++;
			//把头节点更新一下
		}
		else if(op == 'D')
		{
			int k; cin >> k;
			if(k == 0)
			{
				head = ne[head];
			}
			else
			{
				ne[k - 1] = ne[ne[k - 1]];
			}
			
		}
		else if(op == 'I')//建议不要省略最后的判断，不知道为什么会错
		{
			int op1, op2;
			cin >> op1 >> op2;
			e[idx] = op2;
			ne[idx] = ne[op1 - 1];
			ne[op1 - 1] = idx ++;
		}
	}
	//循环输出一遍链表
	for(int i = head; i != -1; i = ne[i])
	{
		printf("%d ", e[i]);
	}
}
```

### 双链表



## 三. 搜索与图论

### DFS深度优先搜索

```c++
#include<iostream>

using namespace std;

const int N = 10;
int n;
int path[N];//第几层
bool st[N];//状态

void dfs(int u)
{
	if(u == n)
	{
		for(int i = 0; i < n; i++) printf("%d ", path[i]);
		puts("");
		return;
	}
	
	for(int i = 1; i <= n; i++)//从1开始(因为题目从1开始数字排序)
	{
		if(!st[i])
		{
			path[u] = i;//将i放在这个空
			st[i] = true;
			dfs(u + 1);
			st[i] = false;//还原现场
		}
	}
}

int main(void)
{
	cin >> n;
	dfs(0);
	return 0;
}
```

### BFS宽度优先搜索

```c++

```



### 树的深度优先遍历

```c++
#include<iostream>
#include<algorithm>
#include<cstring>

using namespace std;

const int N = 100010, M = N * 2;
//点的数量N, 有M条边（无向图，上下都能走，所以建两条边）
int n;
int h[N], e[M], ne[M], idx;
//h[]存每个点的单链表，用e[], ne[], idx实现单链表操作
int ans = N;
bool st[N];

void add(int a, int b)
{
	e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
	//插入头节点，然后更新头指针（改成b)
}

int dfs(int u)
{
	st[u] = true;
	
	int size = 0, sum = 0;
	for(int i = h[u]; i != -1; i = ne[i])
	{
		int j = e[i];
		if(st[j])continue;
		
		int s = dfs(j);//求它的子节点的大小
		size = max(size, s);
        //子节点对比
        //对比哪部分的数更大（有很多个子节点）
		sum += s;//子节点一共的数
	}
	
	size = max(size, n - sum - 1);
    //节点上下对比
    //上面的部分和下面的部分，取最大值
	ans = min(ans, size);
    //全局对比，是否需要更新树的重心（是新的最小吗？
	
	return sum + 1;//自己和下面子节点的点数（递归求size）
}

int main(void)
{
	scanf("%d", &n);
	
	memset(h, -1, sizeof h);
	//每个点都要存一个单链表，表示这个点可以走的路有哪些
	//把这些单链表都初始化，设置头节点 = -1
	
	for(int i = 0; i < n - 1; i ++)
	{
		int a, b;
		scanf("%d%d", &a, &b);
		add(a, b), add(b, a);
	}
	dfs(1);
	
	printf("%d\n", ans);	
	
	return 0;
}
```

## 四. 数学知识

### 质数（素数）

> 从2开始的数（大于1的整数），约数只有1和它本身
>
> 素数为n，因为约数成对（n/d 和 d），只要满足小的那个约数就可以，所以 d <= n/d, d^2 <= n, d <= 根号n;

#### 试除法（判断素数）

```c++
#include<iostream>

using namespace std;

bool is_prime(int x) // 用布尔类型来判断
{
	if(x < 2) return false;
	
	for(int i = 2; i <= x/i; i ++)
	{
		if(x % i == 0) return false;//可以整除说明有除了1和它本身之外别的约数
		
	}
	return true;
}

int main(void)
{
	int n; cin >> n;
	for(int i = 0; i < n; i++)
	{
		int x; cin >> x;
		if(is_prime(x)) puts("Yes");
		else puts("No");
	}
}
```

#### 分解质因数

> 每个合数都可以写成几个质数（素数）相乘的形式，因数一定是素数

```c++
#include<iostream>

using namespace std;

void divide(int x) 
{
	for(int i = 2; i <= x/i; i++)//一个数最小的因数就是质因数（素数）
	{
		if(x % i == 0)//出现最小的因数
		{
			int s = 0;
			while(x % i == 0)//将这个最小质因数除完之后，
			{
				x /= i;     //得到一个新的数，所以x不停地变化
				s ++;
			}
			cout << i << " " << s << endl;
		}
	}//这个新的数的最小质因数一定比之前的大，因为小的之前都除完了，然后继续循环求质因数
	
	//如果最后剩1的话，就除干净了，没有质因数了，因为没有因数可以分解了
	//如果最后不剩1的话，它（改变的x）自己就是最后一个
	if(x > 1) cout << x << " " << 1 << endl;
	cout << endl;
}

int main(void)
{
	int n; cin >> n;
	for(int i = 0; i < n; i++)
	{
		int x; cin >> x;
		divide(x);
	}
	return 0;
	
}
```

#### 筛质数（区间范围内，素数的个数）

##### 朴素筛法

```c++
#include<iostream>

using namespace std;

const int N = 1000010;

int primes[N], cnt;
bool st[N];

void get_primes(int n)
{
	for(int i = 2; i <= n; i++)
	{
		if(st[i]) continue;//如果是true（被标记过）就跳过
		primes[cnt ++] = i;//没被标记就是素数
		for(int j = i + i; j <= n; j += i)//将素数的倍数都标记，倍数都不可能是素数
		{
			st[j] = true;
		}
	}
}

int main(void)
{
	int n; cin >> n;
	get_primes(n);
	
	cout << cnt << endl;
	
	return 0;
}
```

##### 线性筛法

> 注意<=这些地方

```c++
#include<iostream>

using namespace std;

const int N = 1000010;

int primes[N], cnt;
bool st[N];

void get_primes(int n)
{
	for(int i = 2; i <= n; i++)
	{
		if(!st[i]) primes[cnt ++] = i;//没被标记（false）是质数
		for(int j = 0; primes[j] <= n/i; j++)
		{
			st[primes[j] * i] = true;//将i与所有最小质因数（质数）的倍数筛去
			if(i % primes[j] == 0) break;//保证只用最小质因数筛，不将数重复筛
		}
	}
}

int main(void)
{
	int n; cin >> n;
	
	get_primes(n);
	
	cout << cnt << endl;
	
	return 0;
}
```

### 约数

#### 试除法求约数

```c++
#include<iostream>
#include<algorithm>
#include<vector>

using namespace std;

vector<int> get_divisors(int x)
{
	vector<int> res;//不确定约数的多少
	for(int i = 1; i <= x / i; i++)//从1开始（因为是约数，不是质数）记得优化
	{
		if(x % i == 0)//如果可以整除就是约数
		{
			res.push_back(i);
			if(i != x / i)res.push_back(x / i);
		}
	}
	sort(res.begin(), res.end());
	return res;
}

int main(void)
{
	int n; cin >> n;
	
	while(n--)
	{
		int x;
		cin >> x;
		auto res = get_divisors(x);//用auto不用打那么长
		
		for(auto x : res) cout << x << " ";//遍历输出结果
		cout << endl;
	}
	return 0;
}

```

#### 约数个数

> n可以分解质因数 p1^a1次方（乘a个p)......pk^ak
>
> p1^a1的约数有p1^0, p1^1, p1^2，......，p1^a1，共(a1+1)个
>
> n的约数个数就是（a1+a)(a2+1)(a3+1)...(ak+1)
>
> 例题：正整数378000共有多少个正约数？
>
> 解：将378000分解质因数
>
> 378000=2^4×3^3×5^3×7^1
>
> 由约数个数定理可知378000共有正约数(4+1)×(3+1)×(3+1)×(1+1)=160个。
>
> 其实就是质因数的排列组合，就是约数

```c++
#include<iostream>
#include<algorithm>
#include<unordered_map>
#include<vector>

using namespace std;

typedef long long LL;

const int N = 110, mod = 1e9 + 7;

int main(void)
{
    int n; cin >> n;
    
    unordered_map<int, int>primes;
    
    while(n --)
    {
        int x; cin >> x;
        for(int i = 2; i <= x / i; i ++)//分解质因数，所以从2开始，别忘了<=
        {
            while(x % i == 0)//不停循环求次数，所以是while（别写成if了）
            {
                x /= i;
                primes[i] ++;
            }
        }
        if(x > 1) primes[x] ++;
    }
    LL res = 1;//开一个longlong存答案，1不影响乘法
    for(auto p : primes) res = res * (p.second + 1) % mod;
    
    cout << res;
}
```

