### 区间$dp$

***

`区间dp是线性dp的扩展，大部分的线性dp是用一维的状态来表示状态的，而区间dp是考虑一个区间内部状态的变化，通常用状态f[i][j] 表示，划分依据大部分是区间的端点，或者考虑子区间，也就是n^3的复杂度，这个可以根据题目的数据范围来猜测，然后加以验证`

#### 1. [ 石子合并 - AcWing题库](https://www.acwing.com/problem/content/284/)

> 设有 $N$ 堆石子排成一排，其编号为 $1，2，3，…，N$。
>
> 每堆石子有一定的质量，可以用一个整数来描述，现在要将这 NN 堆石子合并成为一堆。
>
> 每次只能合并相邻的两堆，合并的代价为这两堆石子的质量之和，合并后与这两堆石子相邻的石子将和新堆相邻，合并时由于选择的顺序不同，合并的总代价也不相同。
>
> 例如有 44 堆石子分别为 `1 3 5 2`， 我们可以先合并 $1、2$ 堆，代价为 $4$，得到 `4 5 2`， 又合并 $1，2$ 堆，代价为 99，得到 `9 2` ，再合并得到 $11$，总代价为 $4+9+11=24$；
>
> 如果第二步是先合并 $2，3$ 堆，则代价为 $7$，得到 `4 7`，最后一次合并代价为 $11$，总代价为 $4+7+11=22$。
>
> 问题是：找出一种合理的方法，使总的代价最小，输出最小代价。
>
> #### 输入格式
>
> 第一行一个数 NN 表示石子的堆数 $N$。
>
> 第二行 $N$ 个数，表示每堆石子的质量(均不超过 $1000$)。
>
> #### 输出格式
>
> 输出一个整数，表示最小代价。
>
> #### 数据范围
>
> $1≤N≤300$
>
> #### 输入样例：
>
> ```
> 4
> 1 3 5 2
> ```
>
> #### 输出样例：
>
> ```
> 22
> ```

`分析` ：区间$dp$ 的第一道题，考虑状态$f[i][j]$ 表示合并区间 $[i, j]$的所有方案 

​												属性：$min$

​												状态计算：$f[i][j]$ 最后一次合并的位置，也就是枚举 最后一次的位置$k$ (这个也可以根据数据范围来推断并验证)

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 310;
int f[N][N];
int n;
int s[N];

int main()
{
    cin >> n ;
    for(int i = 1; i <= n ; i ++ )
    {
        cin >> s[i];
        s[i] += s[i - 1];
    }
    
    for(int len = 1; len <= n; len ++)
        for(int i = 1; i +len - 1 <= n; i ++)
        {
            int j= i + len - 1;
            f[i][j] = 1e9;
            if(len == 1) f[i][j] = 0;
            for(int k = i; k < j; k ++ )  // 这里k 的范围是因为  K+1 要小于等于 j  
            {
                f[i][j] = min(f[i][j], f[i][k] + f[k + 1][j] + s[j] - s[i - 1]);
            }
        }
    cout << f[1][n] ;
    return 0;
}
```

 

***

#### 2. [ 1068. 环形石子合并【区间DP+环形区间问题】(彩色铅笔) - AcWing](https://www.acwing.com/solution/content/59932/)

> 题目描述
> 给定 $n$ 个石子的 分数 $w_i$，且石子是 环形放置 的（在给定的顺序上，同时满足 $1$ 和 $n$ 也是相邻的）
>
> 规定每次只能合并 相邻 的两堆石子，形成一个新的石子堆，合并的 费用 是两个石子堆的 分数之和
>
> 求解两个方案：
>
> 方案一：把这 $n$ 堆石子合并为一堆，且满足费用最大的方案
> 方案二：把这 $n$ 堆石子合并为一堆，且满足费用最小的方案

`分析`：这道题与上一道题基本是一样的，难点就是**如何将一个环形的问题进行化简**，技巧就是**将一个环展成长度为 2 * n 的链**，这样每次枚举长度为 $n$ 的区间就可以了

``` c++
#include <iostream>
#include <cstring>
using namespace std;
const int N = 210, M = N << 1;
int f[M][M], g[M][M];
int s[M];
int n;

int main()
{
	cin >> n ;
    for(int i = 1; i <= n; i ++ )
    {
        cin >> s[i];
        s[i + n]  = s[i];
    }
    for(int i = 1; i <= 2 * n; i ++ ) s[i] += s[i - 1];
    memset(f, 0x3f, sizeof f);
    memset(g, 0xcf, sizeof g);
    
    for(int len = 1; len <= n; len ++ )
        for(int i = 0; i + len - 1 <= 2 * n; i ++ )
        {
            int j = i + len - 1;
            if(len == 1) f[i][j] = g[i][j] = 0;
            else
            {
                for(int k = i; k < j; k ++)
                {
                    f[i][j] = min(f[i][j], f[i][k] + f[k + 1][j] + s[j] - s[i - 1]);
                    g[i][j] = max(g[i][j], g[i][k] + g[k + 1][j] + s[j] - s[i - 1]);
                }
            }
        }
    
    int maxv = -1e9, minv = 1e9;
    for(int i = 1; i <= n; i ++) maxv = max(maxv, g[i][i + n - 1]), minv = min(minv,f[i][i + n - 1]);
    
    cout << minv << endl << maxv << endl;
    return 0;
}
```

#### 3.[320. 能量项链 - AcWing题库](https://www.acwing.com/problem/content/description/322/)

> 在 $Mars$ 星球上，每个 $Mars$ 人都随身佩带着一串能量项链，在项链上有 $N$ 颗能量珠。
>
> 能量珠是一颗有头标记与尾标记的珠子，这些标记对应着某个正整数。
>
> 并且，对于相邻的两颗珠子，前一颗珠子的尾标记一定等于后一颗珠子的头标记。
>
> 因为只有这样，通过吸盘（吸盘是 $Mars$ 人吸收能量的一种器官）的作用，这两颗珠子才能聚合成一颗珠子，同时释放出可以被吸盘吸收的能量。
>
> 如果前一颗能量珠的头标记为 $m$，尾标记为 $r$，后一颗能量珠的头标记为 $r$，尾标记为 $n$，则聚合后释放的能量为 $m×r×n$（$Mars$ 单位），新产生的珠子的头标记为 $m$，尾标记为 $n$。
>
> 需要时，Mars 人就用吸盘夹住相邻的两颗珠子，通过聚合得到能量，直到项链上只剩下一颗珠子为止。
>
> 显然，不同的聚合顺序得到的总能量是不同的，请你设计一个聚合顺序，使一串项链释放出的总能量最大。
>
> 例如：设 N=4N=4，44 颗珠子的头标记与尾标记依次为 (2，3)(3，5)(5，10)(10，2)(2，3)(3，5)(5，10)(10，2)。
>
> 我们用记号 ⊕⊕ 表示两颗珠子的聚合操作，$(j⊕k)(j⊕k)$ 表示第 $j$、$k$ 两颗珠子聚合后所释放的能量。则
>
> 第 $4、1$ 两颗珠子聚合后释放的能量为：$(4⊕1)=10×2×3=60$。
>
> 这一串项链可以得到最优值的一个聚合顺序所释放的总能量为 $((4⊕1)⊕2)⊕3)=10×2×3+10×3×5+10×5×10=710$4。
>
> #### 输入格式
>
> 输入的第一行是一个正整数 $N$，表示项链上珠子的个数。
>
> 第二行是 $N$ 个用空格隔开的正整数，所有的数均不超过 $1000$，第 $i$ 个数为第 $i$ 颗珠子的头标记，当 $i<N$ 时，第 $i$ 颗珠子的尾标记应该等于第 $i+1$ 颗珠子的头标记，第 $N$ 颗珠子的尾标记应该等于第 $1$ 颗珠子的头标记。
>
> 至于珠子的顺序，你可以这样确定：将项链放到桌面上，不要出现交叉，随意指定第一颗珠子，然后按顺时针方向确定其他珠子的顺序。
>
> #### 输出格式
>
> 输出只有一行，是一个正整数 $E$，为一个最优聚合顺序所释放的总能量。
>
> #### 数据范围
>
> $4≤N≤100$,
> $1≤E≤2.1×10^9$
>
> #### 输入样例：
>
> ```
> 4
> 2 3 5 10
> ```
>
> #### 输出样例：
>
> ```
> 710
> ```

`分析` ： 这道题和上道题基本一样，就是有一点弯弯得绕，就是如何表示价值，然后以及区间的循环范围，如何确定呢？ 可以考虑当区间长度为$2$ 的情况，长度从 2 开始，因为从 1 开始没有意义， 到多少结束呢？还是区间长度为 2 的情况，最起码要做到 3 才可以结束，因为 区间中两个数代表有两个矩阵

​			划分方式，最后一次合并的位置，中间的数已经消没了，所以价值就是 $w[i] * w[k] * w[j]$

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 210;
int f[N][N];
int n;
int w[N];
int main()
{
    cin >> n;
    for(int i = 1; i <= n; i ++ )
    {
        cin >> w[i];
        w[i + n] = w[i];
    }
    
    for(int len = 2; len <= n + 1; len ++ )
        for(int i = 1; i + len - 1<= 2 * n; i ++ )
        {
            int j = i + len - 1;
            for(int k = i + 1; k < j ; k ++ )
            {
                f[i][j] = max(f[i][j], f[i][k] + f[k][j] + w[i] * w[k] * w[j]);
            }
            
        }
    
    int ans = 0;
    for(int i = 1; i <= n; i ++ ) ans = max(ans, f[i][i + n]);
    
    cout << ans;
    return 0;
}
```

#### 4.[312. 戳气球 (LeetCode)](https://leetcode-cn.com/problems/burst-balloons/)

> 有 $n$ 个气球，编号为$0$ 到 $n - 1$，每个气球上都标有一个数字，这些数字存在数组 $nums$ 中。
>
> 现在要求你戳破所有的气球。戳破第 $i$ 个气球，你可以获得 $nums[i - 1] * nums[i] * nums[i + 1]$ 枚硬币。 这里的 $i - 1$ 和 $i + 1$ 代表和 $i$ 相邻的两个气球的序号。如果 $i - 1$或 $i + 1$ 超出了数组的边界，那么就当它是一个数字为 $1$ 的气球。
>
> 求所能获得硬币的最大数量。
>
> 示例 1：
>
> ``` c++
> 输入：nums = [3,1,5,8]
> 输出：167
> 解释：
> nums = [3,1,5,8] --> [3,5,8] --> [3,8] --> [8] --> []
> coins =  3*1*5    +   3*5*8   +  1*3*8  + 1*8*1 = 167
>   
> ```
>
> 示例 2：
>
> ``` c++
> 输入：nums = [1,5]
> 输出：10
> ```
>
> 
>
>
> 提示：
>
> $n == nums.length$
> $1 <= n <= 500$
> $0 <= nums[i] <= 100$
>
> 

`分析`： 这道题看似和上面一样，但是还需要修改才能得到正确的答案。

考虑状态 $f[i][j] 表示戳[i, j] 区间内气球的所有方案$ 

属性：$max$

状态转移：分析一下最后一个不同点，就是**戳的气球的位置**， 最后一个戳气球的位置可以是端点吗？，可以的，可以是最后一个位置吗？，也是可以的。

那么获得的分数是多少呢？ 因为区间中除了 最后一个气球，其他气球已经被戳完了，所以获得的分数应该是 $w[i - 1] *w[k] *w[j + 1]$

``` c++
const int N =510;
class Solution {
public:
    int maxCoins(vector<int>& nums) {
        int f[N][N];
        memset(f,0, sizeof f);
        int n  = nums.size();
        nums.push_back(1);
        nums.insert(nums.begin(), 1);
        for(int len = 1; len <= n; len ++)
            for(int i = 1; i + len - 1 <= n; i ++ )
            {
                int j = i + len - 1;
                
                for(int k = i; k <= j; k ++ )
                {
                    f[i][j] = max(f[i][j], f[i][k - 1] + f[k + 1][j] + nums[i - 1] * nums[k] * nums[j + 1]);
                }
            }

        return f[1][n];
    }
};
```

#### 5.[1070. 括号配对 - AcWing题库](https://www.acwing.com/problem/content/description/1072/)

> $Hecy$ 又接了个新任务：BE 处理。
>
> BE 中有一类被称为 GBE。
>
> 以下是 GBE 的定义：
>
> - 空表达式是 GBE
> - 如果表达式 A 是 GBE，则 [A] 与 (A) 都是 GBE
> - 如果 A 与 B 都是 GBE，那么 AB 是 GBE
>
> 下面给出一个 BE，求至少添加多少字符能使这个 BE 成为 GBE。
>
> 注意：BE 是一个仅由`(`、`)`、`[`、`]`四种字符中的若干种构成的字符串。
>
> #### 输入格式
>
> 输入仅一行，为字符串 BE。
>
> #### 输出格式
>
> 输出仅一个整数，表示增加的最少字符数。
>
> #### 数据范围
>
> 对于所有输入字符串，其长度小于100。
>
> #### 输入样例：
>
> ```
> [])
> ```
>
> #### 输出样例：
>
> ```
> 1
> ```

`分析` ：看这个数据范围，是可以忍受 $ n^3$ 的时间复杂度的，所以考虑一下区间$dp$ 。

考虑状态 $f[i][j]$ 表示$[i,j]$ 区间内$GBE$ 的集合

属性：$max长度的最大值$

状态转移：长度最起码从$2$开始，因为只有一个字符是不行的， 现在已经有了子区间的中所有$GBE$的最大长度，那么如何获得 $[i,j]$区间的最大长度呢？

考虑两个区间的端点，如果匹配，那么就是 $f[i + 1][j - 1] + 2$ 即可

如果不匹配呢？ 那就考虑题目中的第三种情况，也就是**如果 A 与 B 都是 GBE，那么 AB 是 GBE**

也就是从 $[i,j]$ 中的$GBE$ 的数量

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 110;
int f[N][N];
char str[N];
int n;


bool match(int i, int j)
{
    if(str[i] == '(' && str[j] == ')' || str[i] == '[' && str[j] == ']') return true;
    return false;
}

int main()
{
    cin >> str + 1;
    n = strlen(str + 1);
    
    for(int len = 2; len <= n; len ++ ) 
        for(int i = 1; i + len - 1 <= n; i ++ )
        {
            int j = i +  len - 1;
            if(match(i,j)) f[i][j] =max(f[i + 1][j - 1] + 2,f[i][j]);
            for(int k = i; k <= j; k ++ )
                f[i][j] = max(f[i][j],f[i][k] + f[k + 1][j]);
            
        }
    
    cout <<n -  f[1][n] ;
    return 0;
}
```

#### 6.[1222. 密码脱落 - AcWing题库](https://www.acwing.com/problem/content/1224/)

> X星球的考古学家发现了一批古代留下来的密码。
>
> 这些密码是由$A、B、C、D $ 四种植物的种子串成的序列。
>
> 仔细分析发现，这些密码串当初应该是前后对称的（也就是我们说的镜像串）。
>
> 由于年代久远，其中许多种子脱落了，因而可能会失去镜像的特征。
>
> 你的任务是：
>
> 给定一个现在看到的密码串，计算一下从当初的状态，它要至少脱落多少个种子，才可能会变成现在的样子。
>
> #### 输入格式
>
> 共一行，包含一个由大写字母$ABCD$构成的字符串，表示现在看到的密码串。
>
> #### 输出格式
>
> 输出一个整数，表示至少脱落了多少个种子。
>
> #### 数据范围
>
> 输入字符串长度不超过$1000$
>
> #### 输入样例1：
>
> ```
> ABCBA
> ```
>
> #### 输出样例1：
>
> ```
> 0
> ```
>
> #### 输入样例2：
>
> ```
> ABDCDCBABC
> ```
>
> #### 输出样例2：
>
> ```
> 3
> ```

`分析`： 求回文串，看数据范围可以考虑$n^2$ 的做法，考虑区间$dp$

状态定义：$f[i][j]$ 表示区间$[i,j]$ 的回文串的数量

属性：$max$

状态转移：和上一道题考虑一样，也就是两个区间的端点，如果相等，那么直接$f[i + 1][j - 1] + 2$

如果不相等,如果$str[i]$ 有用，$str[j]$没用，那么也就是$f[i][j - 1]$ 反之，则是$f[i + 1][j]$

还有另一种情况，$str[i],str[j]$ 都没用，但是已经被上述两种情况包含了

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 1010;
char str[N];
int n;
int f[N][N];

int main()
{
    cin >> str + 1;
    n = strlen(str + 1);
    
    for(int len = 1; len <= n; len ++)
        for(int i = 1; i + len - 1 <= n; i ++ )
        {
            int j = i + len - 1;
            if(len == 1) f[i][j]  = 1;
            else{
                f[i][j] = max(f[i + 1][j], f[i][j - 1]);
                if(str[i] == str[j]) f[i][j] = max(f[i][j], f[i + 1][j- 1] + 2);
            }
            
        }
    
    cout <<n - f[1][n] <<endl;
    return 0;
}
```

#### 7.[479. 加分二叉树 - AcWing题库](https://www.acwing.com/problem/content/description/481/)

> 设一个 $n$ 个节点的二叉树 tree 的中序遍历为$(1,2,3,4...n)$，其中数字 $1,2,3,…,n$ 为节点编号。
>
> 每个节点都有一个分数（均为正整数），记第 $i$ 个节点的分数为 $d_i$，$tree$ 及它的每个子树都有一个加分，任一棵子树 $subtree$（也包含 $tree$ 本身）的加分计算方法如下：     
>
> $subtree的左子树的加分 × subtree的右子树的加分 ＋ subtree的根的分数 $
>
> 若某个子树为空，规定其加分为 $1$。
>
> 叶子的加分就是叶节点本身的分数，不考虑它的空子树。
>
> 试求一棵符合中序遍历为$(1,2,3,…,n)$且加分最高的二叉树 $tree$。
>
> 要求输出： 
>
> （1）tree的最高加分 
>
> （2）tree的前序遍历
>
> #### 输入格式
>
> 第 11 行：一个整数 $n$，为节点个数。 
>
> 第 22 行：$n$ 个用空格隔开的整数，为每个节点的分数（$0<分数<100$）。
>
> #### 输出格式
>
> 第 $1$ 行：一个整数，为最高加分（结果不会超过`int`范围）。     
>
> 第 $2$ 行：$n$ 个用空格隔开的整数，为该树的前序遍历。如果存在多种方案，则输出字典序最小的方案。
>
> #### 数据范围
>
> $n<30$
>
> #### 输入样例：
>
> ```
> 5
> 5 7 1 2 10
> ```
>
> #### 输出样例：
>
> ```
> 145
> 3 1 2 4 5
> ```

`分析`：先置知识，只知道一棵树的遍历是无法确定该树的形状的，如果抛开这道题的背景，也就是求一个区间的最大值，考虑区间$dp$

状态定义：$f[i][j]$ 表示二叉树的中序遍历为该序列$[i,j]$区间内的数字所有二叉树

属性：$分数max$

状态计算：考虑区间中哪一个树是根节点，范围$l \le k \le r$



``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 50;

int f[N][N],g[N][N];
int w[N];
int n;

void dfs(int l, int r)
{
    if (l > r) return ;
    int k = g[l][r];
    cout << k << ' ';
    dfs(l,k - 1);
    dfs(k + 1, r);
}

int main()
{
    cin >> n;
    for(int i = 1; i <= n; i ++ ) cin >> w[i];
    
    for(int len = 1; len <= n; len ++ )
        for(int i = 1; i + len - 1 <= n; i ++ )
        {
            int j = i + len - 1;
            if(len == 1) f[i][j] = w[i], g[i][j] = i;
            else
            {
                for(int k = i; k <= j; k ++ )
                {
                    int left =  k == i ? 1:f[i][k - 1];
                    int right = k == j? 1 :f[k + 1][j];
                    if(left * right + w[k] > f[i][j])
                    {
                        f[i][j] = left * right + w[k];
                        g[i][j] = k;
                    }
                }
            }
        }
    
    cout << f[1][n] <<endl;
    dfs(1, n);
    return 0;
}
```



#### 8.[1069. 凸多边形的划分【彩色铅笔】 - AcWing](https://www.acwing.com/solution/content/62040/)

> 题目描述
> 给定 $n$ 个点的 凸多边形 中每个 顶点 的 权值 $wi$
> 我们可以把该 凸多边形 划分成 $n - 2$ 个 三角形（三角形不能相交，这样就能保证是 $n−2$ 了）
>
> 每次划分三角形的 费用 为 三个顶点权值 的 乘积
>
> 求一个划分 方案，使得 方案的 费用总和 最小
>
> #### 数据范围
>
> $N≤50$,
> 数据保证所有顶点的权值都小于$10^9$
>
> #### 输入样例：
>
> ``` c++
> 5
> 121 122 123 245 231
> ```
>
> 
>
> #### 输出样例：
>
> ``` c++
> 12214884
> ```

`分析` ：这道题的数据范围很小，所以考虑时间复杂度比较高的做法，考虑区间$dp$

状态定义：$f[i][j]$ 表示合并区间为$[i,j]$ 的所有合并方式（意思就是只考虑区间$[i,j]$ 的合并方式）

属性：$权值min$

状态计算：考虑固定两个顶点，就固定$i,j$  这两个顶点，那么枚举另外一个端点$k$ 就可以得到答案

此外：此题每个点的权值很大， 所以要使用高精度

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 55, M = 35;
int f[N][N][M]; // M 表示对应的数字
int n;
int w[N];

void add(int a[], int b[])
{
    static int c[M];
    int t = 0;
    memset(c, 0, sizeof c);
    for(int i = 0; i < M; i ++)
    {
        t += a[i] + b[i];
        c[i] = t % 10;
        t /= 10;
    }
    memcpy(a, c, sizeof c);
}
void mul(int a[], int b)
{
	static int c[M];
    int t = 0;
    memset(c,0 ,sizeof c);
    for(int i = 0; i < M; i ++ )
    {
        t += a[i] * b;
        c[i] = t % 10;
        t /= 10;
	}
    memcpy(a, c, sizeof c);
}
void print(int a[])
{
    int k = M - 1;
    while(k && a[k] == 0) k--;
    while(k >= 0) cout <<a[k--];
    cout <<endl;
    return ;
}
int cmp(int a[], int b[])
{
    for(int i = M - 1; i>= 0; i -- )
    {
        if(a[i] > b[i]) return 1;
        else if(a[i] < b[i]) return -1;
    }
    
    return 0;
}

int main()
{
    cin >> n;
    for( int i = 1; i <= n; i ++ ) cin >>w[i];
    
    int tmp[M];
    
    for(int len = 3; len <= n; len ++)
        for(int i = 1; i + len - 1 <= n; i ++)
        {
            int j = i + len - 1;
            f[i][j][M - 1] = 1;
            for(int k = i + 1; k < j; k ++ )
            {
                memset(tmp, 0,sizeof tmp);
                tmp[0] = w[k];
                mul(tmp, w[i]);
                mul(tmp, w[j]);
                add(tmp, f[i][k]);
                add(tmp, f[k][j]);
                if(cmp(tmp, f[i][j]) <=0)
                {
                    memcpy(f[i][j], tmp, sizeof tmp);
                }
            }
        }
    
    print(f[1][n]);
    return 0;
}
```

#### 9. [321. 棋盘分割 - AcWing题库](https://www.acwing.com/problem/content/description/323/)

> 将一个 8×88×8 的棋盘进行如下分割：将原棋盘割下一块矩形棋盘并使剩下部分也是矩形，再将剩下的部分继续如此分割，这样割了 $(n−1)$ 次后，连同最后剩下的矩形棋盘共有 $n$ 块矩形棋盘。(每次切割都只能沿着棋盘格子的边进行)
>
> ![1191_1.jpg](https://www.acwing.com/media/article/image/2019/02/05/19_32dad08629-1191_1.jpg)
>
> 原棋盘上每一格有一个分值，一块矩形棋盘的总分为其所含各格分值之和。
>
> 现在需要把棋盘按上述规则分割成 nn 块矩形棋盘，并使各矩形棋盘总分的均方差最小。
>
> 均方差![formula.png](https://www.acwing.com/media/article/image/2019/02/05/19_566d096029-formula.png) ，其中平均值![lala.png](https://www.acwing.com/media/article/image/2019/02/05/19_047fe57229-lala.png) ，$x_i$ 为第 $i$ 块矩形棋盘的总分。
>
> 请编程对给出的棋盘及 $n$，求出均方差的最小值。
>
> #### 输入格式
>
> 第 11 行为一个整数 $n$。
>
> 第 $2$ 行至第 $9$ 行每行为 $8$ 个小于 $100$ 的非负整数，表示棋盘上相应格子的分值。每行相邻两数之间用一个空格分隔。
>
> #### 输出格式
>
> 输出最小均方差值（四舍五入精确到小数点后三位）。
>
> #### 数据范围
>
> $1<n<15$
>
> #### 输入样例：
>
> ```
> 3
> 1 1 1 1 1 1 1 3
> 1 1 1 1 1 1 1 1
> 1 1 1 1 1 1 1 1
> 1 1 1 1 1 1 1 1
> 1 1 1 1 1 1 1 1
> 1 1 1 1 1 1 1 1
> 1 1 1 1 1 1 1 0
> 1 1 1 1 1 1 0 3
> ```
>
> #### 输出样例：
>
> ```
> 1.633
> ```

`分析`：看做二维的区间$dp$ , 每次选择保留一部分

状态定义 :$f[x_1][y_1][x_2][y_2]$  表示划分区域 $[x1, x2] * [y1,y2]$ 的最小方差

属性：$均方差min$

状态计算：考虑每一次切的位置，横切，纵切，上半部分和下半部分

``` c++ 
#include <iostream>
#include <cstring>
#include <cmath>
using namespace std;
const int N = 15, M = 9;
const double INF = 0x3f3f3f3f;
int s[M][M];
double f[N][N][N][N][N];
int n;
double X;
double get(int x1, int y1, int x2, int y2)
{
    double sum = s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1];
    sum = sum - X;
    return sum * sum;
}

double dfs(int x1, int y1, int x2, int y2,int k)
{
    double &v = f[x1][y1][x2][y2][k];
    if(v >= 0) return v;
    if(k == 1) return v = get(x1, y1, x2, y2);
    
    v = INF;
    for(int i = x1; i + 1 <= x2; i ++ )
    {
        v = min(v,dfs(x1, y1, i, y2, k - 1) + get(i + 1, y1, x2, y2));
        v = min(v, dfs(i + 1, y1, x2, y2, k - 1) + get(x1, y1, i, y2));
    }
    for(int i = y1; i + 1 <= y2; i ++ )
    {
        v = min(v, dfs(x1, y1, x2, i,k - 1) + get(x1, i + 1, x2, y2));
        v = min(v, dfs(x1, i + 1, x2, y2,k - 1) + get(x1, y1, x2, i));
    }
    return v;
}


void print(double a[][9])
{
    for(int i = 1; i <= 9; i ++)
    {
        for(int j = 1; j<= 9; j ++)
        {
            cout << a[i][j] <<' ';
        }
        cout << endl;
    }
    cout << endl;
}
int main()
{
    cin >> n;
    for(int i = 1; i <= 8; i ++ )
        for(int j = 1; j <= 8; j ++ ) cin >> s[i][j];
    
    for(int i = 1; i <= 8; i ++ )
        for(int j = 1;j <= 8; j ++ )
            s[i][j] += s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1];
            
    memset(f, -1, sizeof f);
    X = double(s[8][8]) / n;
    double ans = dfs(1,1,8,8, n);
    
    printf("%.3f\n", sqrt(ans / n));
    return 0;
}
```



#### 10.[1388. 游戏 - AcWing题库](https://www.acwing.com/problem/content/description/1390/)

> 玩家一和玩家二共同玩一个小游戏。
>
> 给定一个包含 $N$ 个正整数的序列。
>
> 由玩家一开始，双方交替行动。
>
> 每次行动可以在数列的两端之中任选一个数字将其取走，并给自己增加相应数字的分数。（双方的初始分都是 $0$ 分）
>
> 当所有数字都被取完时，游戏结束。
>
> 分更高的一方获胜。
>
> 请计算，如果双方都采取最优策略进行游戏，则游戏结束时，双方的得分各是多少。
>
> #### 输入格式
>
> 第一行包含整数 $N$。
>
> 后面若干行包含 $N$ 个整数，表示这个序列。
>
> #### 输出格式
>
> 共一行，两个整数，分别表示玩家一和玩家二的最终得分。
>
> #### 数据范围
>
> $2≤N≤100$,
> 数列中的数字的取值范围为 $[1,200]$。
>
> #### 输入样例：
>
> ```
> 6
> 4 7 2 9
> 5 2
> ```
>
> #### 输出样例：
>
> ```
> 18 11
> ```

`分析`：考虑区间$dp$

状态表示：$f[i][j]$ 表示考虑 $[i,j]$ 区间内，先手可以获得的最大分数

状态计算：考虑最后选择的位置，这道题很显然只有区间的端点可以考虑，难点就是如何计算得分。

假设选择的是区间的左端点，那么最起码可以获得$w_i$的分数，那么剩余的得分呢？也就是剩下的得分出去后一个人的先手能过获得的分数，也就是$s[i +1,j] - f[i + 1][j]$

同理右端点一样

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 110;

int f[N][N];
int n;
int w[N];
int s[N];
// f[i][j] 表示只考虑 [i, j] 区间，第一个玩家可以获得的最大价值
//4 7 2 9 5 2

int main ()
{
    cin >> n ;
    for(int i = 1; i <= n ; i ++ ) cin >> w[i];
    for(int i = 1; i <= n ; i ++ ) s[i] = s[i - 1] + w[i];  
    
    for(int len = 1; len <= n; len ++ )
        for(int l = 1; l + len - 1 <= n; l ++)
        {
            int r = l + len - 1;
            f[l][r] = max(f[l][r], w[l] + s[r] - s[l] - f[l + 1][r]);
            f[l][r] = max(f[l][r], w[r] + s[r - 1] - s[l - 1] - f[l][r - 1]);
        }
    
    cout << f[1][n] <<' ' << s[n] - f[1][n];
    return 0;
}

```

#### 11. [414. 数字游戏 - AcWing题库](https://www.acwing.com/problem/content/416/)

> 丁丁最近沉迷于一个数字游戏之中。
>
> 这个游戏看似简单，但丁丁在研究了许多天之后却发觉原来在简单的规则下想要赢得这个游戏并不那么容易。
>
> 游戏是这样的，在你面前有一圈整数（一共 $n$ 个），你要按顺序将其分为 $m$ 个部分，各部分内的数字相加，相加所得的 $m$ 个结果对 $10$ 取模后再相乘，最终得到一个数 $k$。
>
> 游戏的要求是使你所得的 $k$ 最大或者最小。 
>
> 例如，对于下面这圈数字（$n=4，m=2$）：
>
> ![QQ截图20190308022939.png](https://www.acwing.com/media/article/image/2019/03/08/19_fee91a1641-QQ%E6%88%AA%E5%9B%BE20190308022939.png)
>
> 当要求最小值时，$((2−1)mod10)×((4+3)mod10)=1×7=7$，要求最大值时，为 $((2+4+3)mod10)×(−1mod10)=9×9=81$。
>
> 特别值得注意的是，无论是负数还是正数，对 $10$ 取模的结果均为非负值。
>
> 丁丁请你编写程序帮他赢得这个游戏。
>
> #### 输入格式
>
> 输入文件第一行有两个整数，$n$ 和 $m$。
>
> 以下 $n$ 行每行一个整数，其绝对值不大于 $10000$，按顺序给出圈中的数字，首尾相接。
>
> #### 输出格式
>
> 输出文件有两行，各包含一个非负整数。
>
> 第一行是你程序得到的最小值，第二行是最大值。
>
> #### 数据范围
>
> $1≤n≤50$,
> $1≤m≤9$
>
> #### 输入样例：
>
> ```
> 4 2
> 4
> 3
> -1
> 2
> ```
>
> #### 输出样例：
>
> ```
> 7
> 81
> ```

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 110, M = 10;
int w[N];
int f[N][N][M];
int g[N][N][M];
int n, m;
int s[N];

int get_mod(int x)
{
    return (x % 10 + 10) % 10;
}

int main()
{
    cin >> n >> m;
    for(int i = 1; i <= n; i ++ )
    {
        cin >> w[i];
        w[i + n] = w[i];
    }
    for(int i = 1; i <= 2 * n; i ++ ) s[i] = s[i - 1] + w[i];
    // f[i][j][k] 表示只考虑区间[i, j] 所合并成 k 份所能获得的最大值
    // 显然是枚举最后一段的位置，l_____k - 1个部分____|___________r  一共 k 个部分
    // 由于前面最起码有k - 1 个数，所以起点最少为 l + k - 1

    memset(f, 0xcf, sizeof f);
    memset(g, 0x3f, sizeof f);
    for(int len = 1; len <= n; len ++ )
        for(int i = 1; i + len - 1 <= 2 * n; i ++ )
        {
            int j = i + len - 1;
            f[i][j][1] = g[i][j][1] =  get_mod(s[j] - s[i - 1]);
            for(int k = 2; k <= m; k ++ )
            {
                // 这是枚举最后一段的位置
                for(int x = i + k - 1; x <= j; x ++ )
                {
                    f[i][j][k] = max(f[i][j][k], f[i][x - 1][k - 1] * get_mod(s[j] - s[x - 1]));
                    g[i][j][k] = min(g[i][j][k], g[i][x - 1][k - 1] * get_mod(s[j] - s[x - 1]));
                }
            }
        }
        
    
     int maxv=-1e9,minv=1e9;
        for (int i = 1; i <= n; i ++ )
        {
            maxv = max(maxv, f[i][i + n - 1][m]);
            minv = min(minv, g[i][i + n - 1][m]);
        }
        cout<<minv<<endl<<maxv<<endl;
        
    return 0;
}

```





#### 12.[284. 金字塔 - AcWing题库](https://www.acwing.com/problem/content/description/286/)

> 虽然探索金字塔是极其老套的剧情，但是有一队探险家还是到了某金字塔脚下。
>
> 经过多年的研究，科学家对这座金字塔的内部结构已经有所了解。
>
> 首先，金字塔由若干房间组成，房间之间连有通道。
>
> 如果把房间看作节点，通道看作边的话，整个金字塔呈现一个有根树结构，节点的子树之间有序，金字塔有唯一的一个入口通向树根。
>
> 并且，每个房间的墙壁都涂有若干种颜色的一种。
>
> 探险队员打算进一步了解金字塔的结构，为此，他们使用了一种特殊设计的机器人。
>
> 这种机器人会从入口进入金字塔，之后对金字塔进行深度优先遍历。
>
> 机器人每进入一个房间（无论是第一次进入还是返回），都会记录这个房间的颜色。
>
> 最后，机器人会从入口退出金字塔。
>
> 显然，机器人会访问每个房间至少一次，并且穿越每条通道恰好两次（两个方向各一次）， 然后，机器人会得到一个颜色序列。
>
> 但是，探险队员发现这个颜色序列并不能唯一确定金字塔的结构。
>
> 现在他们想请你帮助他们计算，对于一个给定的颜色序列，有多少种可能的结构会得到这个序列。
>
> 因为结果可能会非常大，你只需要输出答案对$10^9$ 取模之后的值。
>
> #### 输入格式
>
> 输入仅一行，包含一个字符串 $S$，长度不超过 $300$，表示机器人得到的颜色序列。
>
> #### 输出格式
>
> 输出一个整数表示答案。
>
> #### 输入样例：
>
> ```
> ABABABA
> ```
>
> #### 输出样例：
>
> ```
> 5
> ```

`分析`：首先需要搞清楚$DFS$序列的性质。`合法的DFS序列一定是奇数个`，然后考虑区间$dp$

状态定义：$f[i][j]$ 表示字符串区间$[i,j]$ 所构成的$tree$

属性：$count$

状态计算：考虑最后一个子树的的$dfs$ 序列的起点，也就是枚举$k$，必须要满足$str[k] == str[l] == str[r]$ ，不然就不是一颗子树的根节点

```  c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 310, mod = 1e9;
char str[N];
int f[N][N];
int n;
// 
int main()
{
    cin >> str + 1;
    n = strlen(str + 1);
    if(n % 2 == 0) cout << 0;
    else
    {
    for(int len = 1; len <= n; len ++ )
        for(int l = 1; l + len - 1 <= n; l ++ )
        {
            int r = l + len - 1;
            if(len == 1) f[l][r] = 1;
            // 表示叶节点
            else if(str[l] == str[r])
            {
                for(int k = l; k <= r - 2; k += 2 )  // k ++ 也可以，因为为偶数的时候答案会是0不影响
                {
                    if(str[k] == str[r])
                      //  r - 1 - k - 1 >= 0 k <= r - 2 ,如果等于 0 的话，也就说明区间中只有一个点，说明是叶节点，也是合法的
                    f[l][r] =(f[l][r] + (long long )f[l][k]*f[k + 1][r - 1]) % mod;
                } 
            }
        }
        cout << f[1][n];
    }
    return 0;
}
```

