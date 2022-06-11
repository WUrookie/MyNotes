#### 单调队列优化的$DP$问题

单调队列最显著的特点就是求一个滑动窗口内的最大值，设状态转移方程为$f[i] = max(f[j] + w_{ij})$ , $j$ 的范围为$i - a \le j \le i - b$ 当$i$ 发生变化的时候，$j$ 的范围也相应的增大$i$ 的距离，此时就可以使用一个单调队列来优化这个求$max, min$ 的过程。

$ps$: 如果是求一个前缀的最大值，比如状态转移为$f[i] = max(f[j] + w_{ij})$ ，$j$ 的范围为$1 \le j \le i$ ，这种情况的时候，当$i$ 只增大 1 的时候，$j$ 也只增大1 ，所以可以只是用一个变量来维护这个最值。

***

#### 题目

##### 1. [135. 最大子序和 - AcWing题库](https://www.acwing.com/problem/content/description/137/)

> 输入一个长度为 $n$ 的整数序列，从中找出一段长度不超过 $m$ 的连续子序列，使得子序列中所有数的和最大。
>
> **注意：** 子序列的长度至少是 $1$。
>
> #### 输入格式
>
> 第一行输入两个整数 $n,m$。
>
> 第二行输入 $n$ 个数，代表长度为 $n$ 的整数序列。
>
> 同一行数之间用空格隔开。
>
> #### 输出格式
>
> 输出一个整数，代表该序列的最大子序和。
>
> #### 数据范围
>
> $1≤n,m≤300000$
>
> #### 输入样例：
>
> ```
> 6 4
> 1 -3 5 1 -2 3
> ```
>
> #### 输出样例：
>
> ```
> 7
> ```

 

`分析`：设状态$f[i]$ 表示考虑前$i$ 天，并且以$a[i]$ 结尾且长度不超过$m$ 的子序列的集合

属性：$max$

状态计算：$f[i] = max(f[j] + value[j, i]) $ 这里的价值就是区间$[i, j]$ 的和，可以使用前缀和进行计算，也就是$f[i] - max(f[j] + s[i] - s[j - 1])$ 枚举的范围也就是 $[i - m + 1, i]$

暴力的做的话就是$O(nm)$ 考虑进行优化，也就是单调队列可以解决的问题，这里的窗口也就是$[i - m + 1, i]$ 区间 所以$s[j] 的范围就是 [i - m, i - 1] $(变量替换)

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 3e5 + 10;
int s[N], q[N];
int n, m;
int ans = -1e9;

int main()
{
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++ ) scanf("%d", &s[i]);
    for(int i = 1; i <= n; i ++ ) s[i] += s[i - 1];
    
    int hh = 0, tt = 0;
    for(int i = 1; i <= n; i ++ )
    {
        if(hh <= tt && q[hh] < i - m) hh ++; 
        // 这里的队列是不会空的，所以不用判断
        ans = max(ans, s[i] - s[q[hh]]);
        while(hh <= tt && s[q[tt]] >= s[i]) tt --;
        q[++ tt] = i;
    }
    cout << ans;
    return 0;
}
```

##### 2.[AcWing 1088. 旅行问题（彩色铅笔）](https://www.acwing.com/solution/content/67696/)

> John 打算驾驶一辆汽车周游一个环形公路。
>
> 公路上总共有 $n$ 个车站，每站都有若干升汽油（有的站可能油量为零），每升油可以让汽车行驶一千米。
>
> John 必须从某个车站出发，一直按顺时针（或逆时针）方向走遍所有的车站，并回到起点。
>
> 在一开始的时候，汽车内油量为零，John 每到一个车站就把该站所有的油都带上（起点站亦是如此），行驶过程中不能出现没有油的情况。
>
> 任务：判断以每个车站为起点能否按条件成功周游一周。
>
> #### 输入格式
>
> 第一行是一个整数 $n$，表示环形公路上的车站数；
>
> 接下来 $n$ 行，每行两个整数 $p_i,d_i$，分别表示表示第 $i$ 号车站的存油量和第 $i$ 号车站到 **顺时针方向** 下一站的距离。
>
> #### 输出格式
>
> 输出共 $n$ 行，如果从第 $i$ 号车站出发，一直按顺时针（或逆时针）方向行驶，能够成功周游一圈，则在第 ii 行输出 $TAK$，否则输出$NIE$。
>
> #### 数据范围
>
> $3≤n≤10^6$,
> $0≤p_i≤2×10^9$,
> $0≤d_i≤2×10^9$
>
> #### 输入样例：
>
> ```
> 5
> 3 1
> 1 2
> 5 2
> 0 1
> 5 4
> ```
>
> #### 输出样例：
>
> ```
> TAK
> NIE
> TAK
> NIE
> TAK
> ```

`分析`：通过观察数据范围知道暴力不可取（一重循环遍历起点，另一重循环遍历终点），这样一定会超时。有一个**关键的问题**，如何判断从当前点能不能到达终点。这里利用**前缀和（合法括号序列）** 的思想。

*合法括号序列*：在任意前缀中，左括号的数量大于等于右括号的数量。如果将左括号当作$1$,右括号当作$-1$，那么对于每个前缀和，等价于前缀和大于等于$0$。

这里也是用这个思想：

**首先需要破换成连**

`考虑顺时针的情况`:

1. 将从**当前点的油量减去下一点的距离看作当前点的价值**，也就是和*合法括号序列*一样，等价于每一点的前缀和大于等于0
2. 考虑需要枚举的区间，设起点为$i$, 那么从当前点可以回到$i$ 需要满足的区间为$[i,i+n-1]$，为什么不是$[i,i + n]$ ？ 看第一条的条件，将``当前点的油量减去下一点的距离看作当前点的价值``，也就说明，只要第$i + n - 1$ 点满足条件了，也就能到到达$i$点就，
3. 根据上面的分析，区间中所有的点都需要满足$\ge0$ 的条件，也就等价于最小值$\ge0$ ，此时问题就转化为单调队列可以优化的问题了
4. 枚举顺序？顺时针固定了$i$ ，求的是后面区间中的最小值，所以倒着枚举比较好(这也是一般考虑固定区间右端点的原因，如果固定的是左端点，寻找某种性质的时候就需要从后往前枚举)

`考虑逆时针的情况`：

1. 和上面顺时针分析方式一样，此时固定是区间的**右端点** ， 枚举的区间为$[i - n + 1,i]$

不过多赘述



``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 2e6 + 10;
typedef long long LL;
LL s[N];
int o[N], d[N];
int q[N];
int n;
bool st[N];


int main()
{
    cin >> n;
    for(int i = 1; i <= n; i ++) cin >> o[i] >> d[i];
    for(int i = 1; i <= n; i ++) s[i] = s[i + n] = o[i] - d[i];
    for(int i = 1; i <= 2 * n; i ++ ) s[i] += s[i - 1];
    
    int hh = 0, tt = -1;
    for(int i = n * 2; i; i --)
    {
        if(hh <= tt && q[hh] > i + n - 1) hh ++;
        while(hh <= tt && s[q[tt]] >= s[i]) tt--;
        q[++ tt] = i;
        if(i <= n && s[q[hh]] >= s[i - 1]) st[i] = true;
    }
    
    hh = 0, tt = -1;
    d[0] = d[n];
    for(int i = 1; i <= n; i ++) s[i] = s[i + n] = o[i] - d[i - 1];
    for(int i = 2 * n; i; i --) s[i] += s[i + 1];
    
    for(int i = 1; i <= 2 * n; i ++)
    {
        if(hh <= tt && q[hh] < i - n + 1) hh ++;
        while(hh <= tt && s[q[tt]] >= s[i]) tt --;
        q[++ tt] = i;
        if(i > n && s[q[hh]] >= s[i + 1]) st[i - n] = true;
    }
    
    for(int i = 1; i <= n; i ++)
        if(st[i]) cout <<"TAK"<< endl;
        else cout <<"NIE" << endl;
    return 0;
}
```

##### 3. [AcWing 1089. 烽火传递（彩色铅笔）](https://www.acwing.com/solution/content/67778/)

> 烽火台是重要的军事防御设施，一般建在交通要道或险要处。
>
> 一旦有军情发生，则白天用浓烟，晚上有火光传递军情。
>
> 在某两个城市之间有 $n$ 座烽火台，每个烽火台发出信号都有一定的代价。
>
> 为了使情报准确传递，在连续 $m$ 个烽火台中至少要有一个发出信号。
>
> 现在输入 $n,m$ 和每个烽火台的代价，请计算在两城市之间准确传递情报所需花费的总代价最少为多少。
>
> #### 输入格式
>
> 第一行是两个整数 $n,m$，具体含义见题目描述；
>
> 第二行 $n$ 个整数表示每个烽火台的代价 $a_i$。
>
> #### 输出格式
>
> 输出仅一个整数，表示最小代价。
>
> #### 数据范围
>
> $1≤n,m≤2×105$,
> $0≤a_i≤1000$
>
> #### 输入样例：
>
> ```
> 5 3
> 1 2 5 6 2
> ```
>
> #### 输出样例：
>
> ```
> 4
> ```

`分析`：经典的线性$dp$ 看这道题的数据范围可以猜出只能使用一维状态表示。

状态定义：$f[i]$ 表示考虑前 $i$ 天，且点燃第$i$个烽火台的所有方案

属性：价值$min$

状态计算：考虑上一次点燃是哪个位置，合法的位置有$[i - n,i - 1]$， 所以状态转移也就出来了$f[i] = min(f[j]) + w[i]$ ，根据这个状态转移方程的区间可以看出也可以使用单调队列优化。



``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 2e5 + 10;
int a[N];
int f[N], q[N];
int n, m;


int main()
{
    cin >> n >> m;
    for(int i = 1; i <= n; i ++ )cin >> a[i];
    
    int hh = 0, tt = 0;
    // 队列中存储了一个初始状态f[0],此时下标为0
    // 所以 tt 初始化为 0
    // q[0] = 0; 
    for(int i = 1; i <= n; i ++ )
    {
        if(hh <= tt && q[hh] <i - m) hh ++;
        f[i] = f[q[hh]] + a[i];
        while(hh <= tt && f[q[tt]] >= f[i]) tt --;
        q[++ tt] = i;
    }
    int ans = 1e9;
    for(int i = n - m + 1; i <= n; i ++ ) ans = min(ans, f[i]);
    cout << ans;
    return 0;
}
```

##### 4.[AcWing 1090. 绿色通道(彩色铅笔)](https://www.acwing.com/solution/content/67926/)

> 高二数学《绿色通道》总共有 $n$ 道题目要抄，编号 $1,2,…,n$，抄第 $i$ 题要花 $a_i$ 分钟。
>
> 小 Y 决定只用不超过 $t$ 分钟抄这个，因此必然有空着的题。
>
> 每道题要么不写，要么抄完，不能写一半。
>
> 下标连续的一些空题称为一个空题段，它的长度就是所包含的题目数。
>
> 这样应付自然会引起马老师的愤怒，最长的空题段越长，马老师越生气。
>
> 现在，小 Y 想知道他在这 $t$ 分钟内写哪些题，才能够尽量减轻马老师的怒火。
>
> 由于小 Y 很聪明，你只要告诉他最长的空题段至少有多长就可以了，不需输出方案。
>
> #### 输入格式
>
> 第一行为两个整数 $n,t$。
>
> 第二行为 $n$ 个整数，依次为 $a_1,a_2,…,a_n$。
>
> #### 输出格式
>
> 输出一个整数，表示最长的空题段至少有多长。
>
> #### 数据范围
>
> $0<n≤5×10^4$,
> $0<a_i≤3000$,
> $0<t≤10^8$
>
> #### 输入样例：
>
> ```
> 17 11
> 6 4 5 2 5 3 4 5 2 3 4 5 2 3 6 3 5
> ```
>
> #### 输出样例：
>
> ```
> 3
> ```

`分析`：正面考虑基本可以说是无从做起，从**最长的间距最少** 这种描述来看可以考虑使用二分求解。

二分可以求某种性质的分界点：首先需要定义一下这道题的性质。答案满足的条件？

​	假设答案为ans， 也就是空题段为ans的时候，能不能在 t 时间完成多到题目，满足条件，如果空题段越大，那么显然是可以完成的，少于ans空题段，是不能完成的

状态表示：$f[i]$考虑前$i$ 道题，且写第$i$ 道题所有的方案

属性:$min$

状态转移：考虑上一道题的是哪个，合法区间为$[i - limit - 1,i - 1]$



``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N =2e5 + 10;

int a[N];
int q[N];
int f[N];
int n, m;

bool check(int mid)
{
    memset(f, 0, sizeof f);
    int hh = 0, tt = 0;
    
    for(int i = 1; i <= n; i ++)
    {
        if(hh <= tt && q[hh] < i - mid - 1) hh ++;
        f[i] = f[q[hh]] + a[i];
        while(hh <= tt && f[q[tt]] >= f[i]) tt --;
        q[++ tt] = i;
    }
    int ans = 1e9;
    for(int i = n - mid; i <= n; i++) ans = min(ans,f[i]);
    
    return ans <= m;
}

int main()
{
    cin >> n >> m;
    for(int i = 1; i <= n; i ++ ) cin >> a[i];
    int l = 0, r = n;
    while(l < r)
    {
        int mid = l + r >> 1;
        if(check(mid)) r = mid;
        else l = mid + 1;
    }
    cout << l << endl;
    return 0;
}
```

##### 5.[AcWing 1087. 修剪草坪(彩色铅笔)](https://www.acwing.com/solution/content/67531/)

> 在一年前赢得了小镇的最佳草坪比赛后，$FJ$ 变得很懒，再也没有修剪过草坪。
>
> 现在，新一轮的最佳草坪比赛又开始了，$FJ$ 希望能够再次夺冠。
>
> 然而，$FJ$ 的草坪非常脏乱，因此，$FJ$ 只能够让他的奶牛来完成这项工作。
>
> $FJ$ 有 $N$ 只排成一排的奶牛，编号为 $1$ 到 $N$。
>
> 每只奶牛的效率是不同的，奶牛 $i$ 的效率为 $E_i$。
>
> 编号相邻的奶牛们很熟悉，如果 $FJ $安排超过 $K$ 只编号连续的奶牛，那么这些奶牛就会罢工去开派对。
>
> 因此，现在 $FJ$ 需要你的帮助，找到最合理的安排方案并计算 $FJ$ 可以得到的最大效率。
>
> 注意，方案需满足不能包含超过 $K$ 只编号连续的奶牛。
>
> #### 输入格式
>
> 第一行：空格隔开的两个整数 $N$ 和 $K$
>
> 第二到 $N+1$ 行：第 $i+1$ 行有一个整数 $E_i$。
>
> #### 输出格式
>
> 共一行，包含一个数值，表示$ FJ$ 可以得到的最大的效率值。
>
> #### 数据范围
>
> $1≤N≤10^5$,
> $0≤E_i≤10^9$
>
> #### 输入样例：
>
> ```
> 5 2
> 1
> 2
> 3
> 4
> 5
> ```
>
> #### 输出样例：
>
> ```
> 12
> ```
>
> #### 样例解释
>
> $FJ$ 有 5 只奶牛，效率分别为 1、2、3、4、5。

`分析`：看数据范围只可以有一维状态表示。

状态定义：$f[i]$ 表示考虑前 $i$ 头牛的合法方案的集合。

属性：$max$

状态计算：首先考虑第$i$ 头牛选不选。

1. 不选，等价于$f[i - 1]$
2. 选，这里的划分依据为**这一段的长度**(我觉得挺难想的) ，可以将划分为长度为$[1,m]$ 的区间， 等价于 $f[i - j - 1] + s[i] - [i - j]$  为什么？ 因为当枚举的长度为$j$ 的时候，那么最开始的牛下标为$i - j + 1$ 那么此时考虑第$i - j$ 头牛，这头牛是一定不可以选的，那么$f[i -j - 1]$ 就表示这个集合，然后对方程进行小小的变形，将$i - j$ 变成$j$ ，那么相应$j$的范围变为$[i -m,i - 1]$  ，此时状态转移方程变为

​				$f[i] = max(f[j - 1] - s[j]) + s[i]$        $i-m \le j \le i - 1$

3. 此时还有一个细节，$j - 1$ 是可能越界的，考虑一下实际含义，也就是选满的情况，表示长度为 $m$ 的情况，也就是最后一段全选了，那么此时$f[-1]$ 就表示长度小于等于$k$ 且全选了，所以$f[-1]$ 表示$0$





``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 1e5 + 10;
typedef long long LL;
LL s[N];
int q[N];
int n, m;
LL f[N];

LL g(int x)
{
    return  f[max(x - 1,0)] - s[x];
}

int main()
{
    cin >> n >> m;
    for(int i = 1; i <= n; i ++ )
    {
        cin >> s[i];
        s[i] += s[i - 1];
    }
    
    int hh = 0, tt = 0;
    for(int i = 1; i <= n; i ++ )
    {
        if(hh <= tt && q[hh] < i - m) hh ++;
        f[i] = max(f[i - 1], g(q[hh]) + s[i]);
        while(hh <= tt && g(q[tt]) <= g(i)) tt--;
        q[++ tt] = i;
    }
    cout << f[n] << endl;
    return 0;
}
```



##### 6.[AcWing 1091. 理想的正方形(彩色铅笔)](https://www.acwing.com/solution/content/68010/)

> 有一个 $a×b$ 的整数组成的矩阵，现请你从中找出一个 $n×n$ 的正方形区域，使得该区域所有数中的最大值和最小值的差最小。
>
> #### 输入格式
>
> 第一行为三个整数，分别表示 $a,b,n$ 的值；
>
> 第二行至第 $a+1$ 行每行为 $b$ 个非负整数，表示矩阵中相应位置上的数。
>
> #### 输出格式
>
> 输出仅一个整数，为 $a×b$矩阵中所有 $n×n$ 正方形区域中的最大整数和最小整数的差值的最小值。
>
> #### 数据范围
>
> $2≤a,b≤1000$,
> $n≤a,n≤b,n≤100$,
> 矩阵中的所有数都不超过 $10^9$。
>
> #### 输入样例：
>
> ```
> 5 4 2
> 1 2 5 6
> 0 17 16 0
> 16 17 2 1
> 2 10 2 1
> 1 2 2 2
> ```
>
> #### 输出样例：
>
> ```
> 1
> ```

`分析`: 我也不知道怎么想到的哈哈，将每一个小窗口内的最大值最小值填到最后一列，然后再在最后一列中求出最大值和最小值

直接上代码：

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 1010;
int w[N][N], row_min[N][N], row_max[N][N];
int n, m, k;
int q[N];


void get_max(int a[], int b[], int tot)
{
    int hh = 0, tt = -1;
    for(int i = 1; i <= tot; i ++ )
    {
        if(hh <= tt && q[hh] < i - k + 1) hh ++;
        while(hh <= tt && b[q[tt]] <= b[i]) tt--;
        q[++ tt] = i;
        a[i] = b[q[hh]];
    }
}
void get_min(int a[], int b[], int tot)
{
    int hh = 0, tt = -1;
    for(int i = 1; i <= tot; i ++ )
    {
        if(hh <= tt && q[hh] < i - k + 1) hh ++;
        while(hh <= tt && b[q[tt]] >= b[i]) tt--;
        q[++ tt] = i;
        a[i] = b[q[hh]];
    }
}

int main()
{
    cin >> n >> m >> k;
    for(int i = 1; i <= n; i ++ )
        for(int j = 1; j <= m; j ++ )
            cin >> w[i][j];
    
    for(int i = 1; i <= n; i ++ )
    {
        get_max(row_max[i],w[i], m);
        get_min(row_min[i],w[i], m);
    }

    int res = 1e9;
    int a[N], b[N], c[N];
    for(int i = k; i <= m; i ++ )
    {
        for(int j = 1; j <= n; j ++ ) a[j] = row_min[j][i];

        get_min(b, a, n);
        for(int j = 1; j <= n; j ++ ) a[j] = row_max[j][i];
        get_max(c, a, n);
        
        for(int j = k; j <= n; j ++ ) res = min(res, c[j] - b[j]);
    }

    cout << res << endl;
    return 0;
}

```

##### 7.[多重背包之单调队列优化](https://www.acwing.com/problem/content/6/)

> 有 $N$ 种物品和一个容量是 $V$ 的背包。
>
> 第 ii 种物品最多有 $s_i$ 件，每件体积是 $v_i$，价值是 $w_i$。
>
> 求解将哪些物品装入背包，可使物品体积总和不超过背包容量，且价值总和最大。
> 输出最大价值。
>
> #### 输入格式
>
> 第一行两个整数，$N，V$，用空格隔开，分别表示物品种数和背包容积。
>
> 接下来有 $N$ 行，每行三个整数 $v_i,w_i,s_i$，用空格隔开，分别表示第 $i$ 种物品的体积、价值和数量。
>
> #### 输出格式
>
> 输出一个整数，表示最大价值。
>
> #### 数据范围
>
> $0<N≤1000$
> $0<V≤20000$
> $0<v_i,w_i,s_i≤20000$
>
> ##### 提示
>
> 本题考查多重背包的单调队列优化方法。
>
> #### 输入样例
>
> ```
> 4 5
> 1 2 3
> 2 4 1
> 3 4 3
> 4 5 2
> ```
>
> #### 输出样例：
>
> ```
> 10
> ```

`分析`：先附上朴素版本的代码，且没用进行空间的优化

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 1010;
int f[N][N];
int n, m;

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= n; i ++ )
    {
        int v, w, s;
        cin >> v >> w >> s;
        for(int j = m; j >= v; j -- )
        {
            for(int k = 0; k <= s && j - k * v >= 0; k ++ )
                f[i][j] = max(f[i][j], f[i - 1][j - k * v] + k * w); 
        }
    }
    cout << f[n][m];
    return 0;
}
```

观察上面的状态转移方程，可以发现状态转移是在前缀的区间内进行的。

也重新写出来一下

$f[i][j] = max(f[i][j], f[i - 1][j - k * v] + k * w)$

展开获得，并且优化掉第一维状态

$f[j] = max(f[j - v] + w, f[j - 2v] + 2w, f[j - 3v] + 3w...,f[mod] + div*w)$

将$j$ 用 $div*v + mod$代替

$f[div*v + mod] =max( f[mod] + div*w, f[mod + k*v] + (div - k)*w)$

其中$k$ 的范围是$[0,div]$  $div = min(s, j/v)$

这刚好符合求$f[i] = max(f[j] + w_{ij})$ 且 $j$属于一个窗口内的最大值这类模型，所以使用单调队列优化

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 20010;
int f[N], g[N];
int n, m;
int q[N];
int main()
{
    cin >> n >> m;
    while(n -- )
    {
        int v, w, s;
        cin >> v >> w >> s;
        memcpy(g, f, sizeof g);
        for(int j = 0; j < v; j ++ ) // 枚举体积
        {
            int hh = 0, tt = -1;
            for(int k = j; k <= m; k += v)
            {
                // 区间长度为 s + 1
                if(hh <= tt && k - s * v > q[hh] ) hh ++;
                if(hh <= tt) f[k] = max(f[k], g[q[hh]] + (k - q[hh]) / v * w);
                while(hh <= tt && g[q[tt]] + (k - q[tt]) / v * w <= g[k]) tt--;
                q[++ tt] = k;
            }
        }
    }
    cout << f[m];
    return 0;
}
```

