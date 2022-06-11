### 状态压缩$DP$

***

> 状态压缩$dp$是是通过一个二进制（b进制）数来表示有个集合，然后对这个集合进行暴力枚举，最后得到结果的方法。
>
> 典型特征就是数据范围比较小，通常是$1 \le n \le 30$
>
> 像这种范围就可以考虑状态压缩$dp$

#### 题目

##### 1.[最短哈密顿路径.AcWing](https://www.acwing.com/problem/content/93/)

> 给定一张 $n$ 个点的带权无向图，点从 $0∼n−1$ 标号，求起点 $0$ 到终点 $n−1$ 的最短 $Hamilton$ 路径。
>
> $Hamilton$ 路径的定义是从 $0$ 到 $n−1$不重不漏地经过每个点恰好一次。
>
> #### 输入格式
>
> 第一行输入整数 $n$。
>
> 接下来 $n$ 行每行 $n$ 个整数，其中第 $i$ 行第 $j $个整数表示点 $i $到 $j$ 的距离（记为 a[i,j]）。
>
> 对于任意的 $x,y,z$，数据保证 $a[x,x]=0$，$a[x,y]=a[y,x]$并且 $a[x,y]+a[y,z]≥a[x,z]$。
>
> #### 输出格式
>
> 输出一个整数，表示最短 $Hamilton$ 路径的长度。
>
> #### 数据范围
>
> $1≤n≤20$
> $0≤a[i,j]≤10^7$
>
> #### 输入样例：
>
> ```
> 5
> 0 2 4 5 1
> 2 0 6 5 3
> 4 6 0 8 3
> 5 5 8 0 5
> 1 3 3 5 0
> ```
>
> #### 输出样例：
>
> ```
> 18
> ```

`分析`：如果暴力做的话，也就是枚举每一条路径，有 $n!$种方法，显然会超时。考虑一下这样一条路径：

考虑 1 到  4 之间的节点，有6种方法：

$1->2->3->4$

$1->3->2->4$

$1->2->4->3$

$1->3->4->2$

$1->4->2->3$

$1->4->3->2$

假设都是到$4$ 的路径，如果第一条路径优于第二条路径，其实是不关系他们之间顺序的，只关注他们经过哪些点和最终停在几号点。

考虑状态：$f[i][j]$ 表示当前经过点的集合为$i$, 最终停在$j$点的所有方案。

属性：权值$min$

状态计算：考虑上一个点在哪就就可以了



``` c++
#include <iostream>
#include <cstring>
// 集合类型的状压dp
using namespace std;
const int N = 20, M = 1 << N;
int f[M][N];
// 要先枚举集合
int n;
int g[N][N];

int main()
{
    cin >> n;
    for(int i = 0; i < n; i ++ )
        for(int j = 0; j < n; j ++ ) cin >> g[i][j];
    memset(f, 0x3f,sizeof f);
    f[1][0] = 0;
    for(int i = 0; i < 1 << n; i ++ )
        for(int j = 0; j < n; j ++ )
        {
            if(i >> j & 1)
                for(int k = 0; k < n; k ++ )
                    if(i >> k & 1)
                    f[i][j] = min(f[i][j], f[i - (1 << j)][k] + g[k][j]);

        }
    
    cout << f[(1 << n) - 1][n - 1];
    return 0;
    
}
```

##### 2.[小国王.AcWing（彩色铅笔）](https://www.acwing.com/solution/content/56348/)

> 在 $n×n$ 的棋盘上放 $k$ 个国王，国王可攻击相邻的 $8$ 个格子，求使它们无法互相攻击的方案总数。
>
> #### 输入格式
>
> 共一行，包含两个整数 $n$ 和 $k$。
>
> #### 输出格式
>
> 共一行，表示方案总数，若不能够放置则输出$0$。
>
> #### 数据范围
>
> $1≤n≤10$,
> $0≤k≤n^2$
>
> #### 输入样例：
>
> ```
> 3 2
> ```
>
> #### 输出样例：
>
> ```
> 16
> ```

`分析`：看数据范围显然是不可以爆搜的，时间复杂度为$O(2^{n^2})$，考虑状态压缩$dp$，假设第$i$ 层的状态是$i$， 如果这一层有连续的两个$1$，这个状态是不合法的。

考虑这个状态可以从哪些状态转移过来呢？假设上一层的状态是$j$ 首先$i\&j$ 必须为 0，否则说明他们摆在同一列了，其次就是$i|j$这个状态不能出现连续的1

至此：状态表示已经出来了$f[i][j][k]$ 表示 前$i$ 行已经摆好，且第$i$层的状态是$j$，一共放置了$k$个国王的所有状态的集合

``` c++
#include <iostream>
#include <cstring>
#include <vector>
using namespace std;
// 连通性的状态压缩dp
const int N = 1 << 11, M = 15;
long long f[M][N][M * M];
int n, m;
vector<int> alls;
bool check(int s)
{
    for(int i = 0; i < n; i ++ )
        if((s >> i & 1) && (s >> (i + 1) & 1)) return false;
    return true;
}
int cnt[N];
int get(int s)
{
    int res = 0;
    while(s)
    {
        if(s & 1) res ++;
        s >>= 1;
    }
    return res;
}
int main()
{
    cin >> n >> m;
    for(int i = 0; i < 1 << n; i ++ )
        if(check(i)) 
        {
            alls.push_back(i);
            cnt[i] = get(i);
        }
    f[0][0][0] = 1;
    // f[i][j][k] 表示前 i 行已经摆好，且当前行的状态是 j，放了 k 个国王的所有方案
    for(int i = 1; i <= n + 1; i ++ )
        for(auto j : alls)
            for(int k = 0; k <= m; k ++ )
                for(auto p : alls)
                {
                    if(check(p | j) && !(p & j))
                        if(k >= cnt[p])
                        f[i][j][k] += f[i - 1][p][k - cnt[p]];
                }
    cout << f[n + 1][0][m] << endl;
    
    
    return 0;
    
}
```

##### 3. [玉米田.AcWing](https://www.acwing.com/problem/content/description/329/)

> 农夫约翰的土地由 $M×N$ 个小方格组成，现在他要在土地里种植玉米。
>
> 非常遗憾，部分土地是不育的，无法种植。
>
> 而且，相邻的土地不能同时种植玉米，也就是说种植玉米的所有方格之间都不会有公共边缘。
>
> 现在给定土地的大小，请你求出共有多少种种植方法。
>
> 土地上什么都不种也算一种方法。
>
> #### 输入格式
>
> 第 11 行包含两个整数 $M$ 和 $N$。
>
> 第 $2..M+1$ 行：每行包含 $N$ 个整数 $0$ 或 $1$，用来描述整个土地的状况，$1$ 表示该块土地肥沃，$0$ 表示该块土地不育。
>
> #### 输出格式
>
> 输出总种植方法对 $10^8$ 取模后的值。
>
> #### 数据范围
>
> $1≤M,N≤12$
>
> #### 输入样例：
>
> ```
> 2 3
> 1 1 1
> 0 1 0
> ```
>
> #### 输出样例：
>
> ```
> 9
> ```

`分析`：和上一道题基本一样的，就是要注意当前行只有能够放置的时候才能放(土地的限制)

状态表示：$f[i][j]$ 表示前 $i$行已经摆好，且当前行的状态是$j$ 的方案的集合

状态转移：根据上一行的状态划分

``` c++
#include <iostream>
#include <cstring>
#include <vector>

using namespace std;
const int N = 15, M = 1 << N, mod = 1e8;
int f[N][M];
int n, m;
int g[N];
vector<int> alls;
vector<int> state[M];
bool check(int s)
{
    for(int i = 0; i < m; i ++)
        if((s >> i & 1) && (s >> (i + 1) & 1)) return false;
    return true;
}
int main()
{
    cin >> n >> m;
    for(int i = 1; i <= n; i ++ )
        for(int j = 0; j < m; j ++ )
        {
            int x;
            cin >> x;
            if(x == 0) g[i] += 1 << j;
        }
    for(int i = 0; i < 1 << m; i ++ )
    {
        if(check(i)) alls.push_back(i);
    }
    for(auto i : alls)
        for(int j : alls)
        {
            if(!(i & j)) state[i].push_back(j);
        }
    
    f[0][0] = 1;
    for(int i = 1; i <= n + 1; i ++ )
        for(int j = 0; j < 1 << m; j ++ )
            for(auto k : state[j])
                if(!(j & g[i]))
                    f[i][j] =(f[i][j] +  f[i - 1][k]) % mod;
    
    cout << f[n + 1][0] << endl;
    return 0;
}
```

##### 4. [炮兵阵地.AcWing](https://www.acwing.com/problem/content/description/294/)

> 司令部的将军们打算在 $N×M$ 的网格地图上部署他们的炮兵部队。
>
> 一个 $N×M$ 的地图由 $N$ 行 $M$ 列组成，地图的每一格可能是山地（用 `H` 表示），也可能是平原（用 `P` 表示），如下图。
>
> 在每一格平原地形上最多可以布置一支炮兵部队（山地上不能够部署炮兵部队）；一支炮兵部队在地图上的攻击范围如图中黑色区域所示：
>
> ![1185_1.jpg](https://www.acwing.com/media/article/image/2019/02/16/19_d512cdba31-1185_1.jpg)
>
> 如果在地图中的灰色所标识的平原上部署一支炮兵部队，则图中的黑色的网格表示它能够攻击到的区域：沿横向左右各两格，沿纵向上下各两格。
>
> 图上其它白色网格均攻击不到。
>
> 从图上可见炮兵的攻击范围不受地形的影响。
>
> 现在，将军们规划如何部署炮兵部队，在防止误伤的前提下（保证任何两支炮兵部队之间不能互相攻击，即任何一支炮兵部队都不在其他支炮兵部队的攻击范围内），在整个地图区域内最多能够摆放多少我军的炮兵部队。
>
> #### 输入格式
>
> 第一行包含两个由空格分割开的正整数，分别表示 $N$ 和 $M$；
>
> 接下来的 $N$ 行，每一行含有连续的 $M$ 个字符(`P` 或者 `H`)，中间没有空格。按顺序表示地图中每一行的数据。
>
> #### 输出格式
>
> 仅一行，包含一个整数 $K$，表示最多能摆放的炮兵部队的数量。
>
> #### 数据范围
>
> $N≤100,M≤10$
>
> #### 输入样例：
>
> ```
> 5 4
> PHPP
> PPHH
> PPPP
> PHPP
> PHHP
> ```
>
> #### 输出样例：
>
> ```
> 6
> ```

`分析`：这道题如果只分析上一行的状态显然是不可以的，因为上上行的状态也会影响到这一行的状态，所以状态表示要多加一维来表示上上行的状态

状态表示:$f[i][j][k]$ 表示前$i$ 行已经摆好，且当前行的状态是$j$,上一行的状态是$j$的所有方案

属性：1的个数最多

状态计算：第$i - 2$ 行的状态进行划分



``` c++
#include <iostream>
#include <cstring>
#include <vector>
using namespace std;
const int N = 11, M = 1 << N, R = 105;
int f[2][M][M];
int n, m;
vector<int> alls;
vector<int> state[M];
int g[R];
int cnt[M];
bool check(int s)
{
    for(int i = 0; i < m; i ++ )
    if((s >> i & 1) && ((s >> i + 1 & 1) || (s >> i + 2 & 1))) return false;
    return true;
}
int get(int s)
{
    int res = 0;
    while(s)  res += s & 1 , s >>=1;
    return res;
}
int main()
{
    cin >> n >> m;
    for(int i = 1; i <= n; i ++ )
        for(int j = 0; j < m; j ++ )
        {
            char ch;
            cin >> ch;
            if(ch == 'H') g[i] += 1 << j;
        }

    for(int i = 0; i < 1 << m; i ++ )
        if(check(i)) 
        {
            alls.push_back(i);
            cnt[i] = get(i);
        }
    for(auto i : alls)
        for(auto j : alls)
            if(!(i & j))  state[i].push_back(j);
    
    for(int i = 1; i <= n + 2; i ++ )
        for(int j : alls)
        {
            if(j & g[i]) continue;
            for(int k : state[j])
                for(int pre : state[k])
                {
                    if(pre & j) continue;
                    f[i & 1][j][k] = max(f[i & 1][j][k], f[i - 1 & 1][k][pre] + cnt[j]);
                }
        }
    cout << f[n + 2 & 1][0][0] << endl;
    
    return 0;
}
```

