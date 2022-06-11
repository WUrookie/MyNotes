### 树形$dp$

> 树形$dp$ ：顾名思义就是在树上进行的$dp$问题，一般采用$DFS$ 进行$dp$ 的过程。通过子节点更新父节点的信息，个别题目还需要通过父节点更新子节点的信息。

#### 1. [1072. 树的最长路径【树形DP】 - AcWing](https://www.acwing.com/file_system/file/content/whole/index/content/2826773/)

> 给定一棵树，树中包含 $n$ 个结点（编号$1$~$n$）和 $n−1$ 条无向边，每条边都有一个权值。
>
> 现在请你找到树中的一条最长路径。
>
> 换句话说，要找到一条路径，使得使得路径两端的点的距离最远。
>
> 注意：路径中可以只包含一个点。
>
> #### 输入格式
>
> 第一行包含整数 $n$。
>
> 接下来 $n−1$ 行，每行包含三个整数 $a_i , b_i, c_i$，表示点 $a_i$ 和 $b_i$之间存在一条权值为 $c_i$ 的边。
>
> #### 输出格式
>
> 输出一个整数，表示树的最长路径的长度。
>
> #### 数据范围
>
> $1≤n≤10000$,
> $1≤a_i,b_i≤n$,
> $−10^5≤ci≤10^5$
>
> #### 输入样例：
>
> ```
> 6
> 5 1 6
> 1 4 5
> 6 3 9
> 2 6 8
> 6 1 7
> ```
>
> #### 输出样例：
>
> ```
> 22
> ```

`分析`：这是我做的第一道树形$DP$的题目，考虑一下如何能够快速的求出树的最长路径。

首先可以考虑暴力来枚举，也就是枚举树的起点和终点，也就是$O(n^2)$的时间复杂度，通过这道题的数据范围显然是不可取的。

假设当前处在树中的某个节点，那么当前的路径由三种情况：

1. 该节点是最长路径的一个端点；
2. 该节点是最长路径的一个节点；
3. 该节点不是最长路径长的节点。

![image-20211227234355120](C:\Users\wxm\AppData\Roaming\Typora\typora-user-images\image-20211227234355120.png)

情况1：直接找理他最远的节点就可以，可以通过$dfs$得到

情况2：找理他最远的节点，然后在一条**理他第二远的节点** 这里第二远指的是路径不可以重叠。

情况3：招到理他最远的节点，那么可以证明理他最远的节点一定是最长路径的某一个端点

![image-20211227235353881](C:\Users\wxm\AppData\Roaming\Typora\typora-user-images\image-20211227235353881.png)

证明：假设所处的点为$A$ 找到离$A$最远的点为$B$, 树中的最长路径为$CD$ 

由$d_{ab} > d_ad$ 所以可以得到 $d_{be} > d_{ed}$ 那么 $CF + BE >= CF + FD$

为什么？$BE > EF + FD$ 那么$BE + FE > ED$ 所以 $B$ 到$C$的距离是最远的。

通过上面的分析：首先要找到每个节点的最远点，同时还有可能次远点也是答案

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 1e5 + 10, M = N << 1;
int h[N], ne[M], e[M], w[M], idx;
int n;
int ans;

void add(int a, int b, int c)
{
	e[idx] = b, ne[idx] = h[a], w[idx] = c, h[a] = idx ++;
}
// 无向图，所以需要建两条边，需要记录他的父节点是谁
int dfs(int u, int father)
{
    // 因为只有在 dp 的时候需要两个距离，所以不用开数组额外存储
    int d1 = 0, d2 = 0;
    for(int i = h[u]; ~i; i = ne[i])
    {
		int j = e[i];
        if(j == father) continue;
        int d = dfs(j, u) + w[i];
        if(d >= d1) d2 = d1, d1 = d;
        else if(d > d2) d2 = d;
        ans = max(ans, d1 + d2);
    }
    return d1;
}

int main()
{
    cin >> n;
    memset(h, -1, sizeof h);
    for(int i = 1; i < n; i ++ )
    {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c), add(b, a, c);
    }
    dfs(1, -1);
    
    cout << ans << endl;
    return 0;
}
```





