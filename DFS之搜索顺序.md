### $DFS$之搜索顺序

> $DFS$ 第一步就是考虑如何设计什么样的搜索顺序可以不重（可以不需要）不漏地搜索到所有的方案。
>
> 目前做到的搜索方法有
>
> - 依次考虑每个待选物品的使用情况
> - 

***

##### 1.[单词接龙_洛谷](https://www.luogu.com.cn/problem/P1019)

> #### 题目描述
>
> 单词接龙是一个与我们经常玩的成语接龙相类似的游戏，现在我们已知一组单词，且给定一个开头的字母，要求出以这个字母开头的最长的“龙”（每个单词都最多在“龙”中出现两次），在两个单词相连时，其重合部分合为一部分，例如 `beast` 和 `astonish`，如果接成一条龙则变为 `beastonish`，另外相邻的两部分不能存在包含关系，例如 `at` 和 `atide` 间不能相连。
>
> #### 输入格式
>
> 输入的第一行为一个单独的整数 $n$ 表示单词数，以下 $n$ 行每行有一个单词，输入的最后一行为一个单个字符，表示“龙”开头的字母。你可以假定以此字母开头的“龙”一定存在。
>
> #### 输出格式
>
> 只需输出以此字母开头的最长的“龙”的长度。
>
> #### 输入
>
> ``` c++
> 5
> at
> touch
> cheat
> choose
> tact
> a
> ```
>
> #### 输出
>
> ``` c+=
> 23
> ```

`思路`：依次考虑每一个单词是否可以接在当前龙的后面。

`难点`：如何判断当前的单词时候可以接在龙的后面？ 使用预处理的方法（$O(n^3)$）

``` c++
#include <iostream>
#include <cstring>

using namespace std;
const int N = 25;
string word[N];
int ans;
int g[N][N];
int n;
int cnt[N];

// 依次考虑每个单词
// 这里的 u 上一次使用的单词
void dfs(int u, string str)
{
    cnt[u] ++;
    ans = max((int)str.size(), ans);
    
    // 尝试接龙
    for(int i = 0; i < n; i ++)
        if(cnt[i] < 2 && g[u][i]) dfs(i, str + word[i].substr(g[u][i]));
    cnt[u]--;
}

int main()
{
    cin >> n;
    for(int i = 0; i < n; i ++) cin >> word[i];
    char ch;
    cin >> ch;
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < n; j ++)
        {
            for(int k = 1; k < min(word[i].size(), word[j].size()); k ++ )
                if(word[i].substr(word[i].size() - k, k) == word[j].substr(0,k))
                {
                    g[i][j] = k;
                    break;
                }
        }
    
    for(int i = 0; i < n; i ++ )
        if(word[i][0] == ch) dfs(i, word[i]);
    
    cout << ans << endl;
    return 0;
}
```

##### 2.[小猫爬山_AcWing](https://www.acwing.com/problem/content/167/)

> 翰翰和达达饲养了 $N$ 只小猫，这天，小猫们要去爬山。
>
> 经历了千辛万苦，小猫们终于爬上了山顶，但是疲倦的它们再也不想徒步走下山了（呜咕>_<）。
>
> 翰翰和达达只好花钱让它们坐索道下山。
>
> 索道上的缆车最大承重量为 $W$，而 $N$ 只小猫的重量分别是 $C_1、C_2……C_N$。
>
> 当然，每辆缆车上的小猫的重量之和不能超过 $W$。
>
> 每租用一辆缆车，翰翰和达达就要付 $1$ 美元，所以他们想知道，最少需要付多少美元才能把这 $N$ 只小猫都运送下山？
>
> #### 输入格式
>
> 第 $1$ 行：包含两个用空格隔开的整数，$N$ 和 $W$。
>
> 第 $2..N+1$ 行：每行一个整数，其中第 $i+1$ 行的整数表示第 $i$ 只小猫的重量 $C_i$。
>
> #### 输出格式
>
> 输出一个整数，表示最少需要多少美元，也就是最少需要多少辆缆车。
>
> #### 数据范围
>
> $1≤N≤18$,
> $1≤C_i≤W≤10^8$
>
> #### 输入样例：
>
> ```
> 5 1996
> 1
> 2
> 1994
> 12
> 29
> ```
>
> #### 输出样例：
>
> ```
> 2
> ```

`分析`：考虑枚举小猫放在哪个能放的缆车中，如果所有的都不能放，则重新开一个缆车

``` c++
#include <iostream>
#include <algorithm>
#include <cstring>
/*
剪枝：优化搜索顺序
最优性剪枝
可行性剪枝
*/

using namespace std;
const int N = 20;
int w[N], n, m;
int ans = N;
int sum[N];


void dfs(int u, int t)
{
    // 最优性剪枝
    if(u >= ans) return;
    if(t == n)
    {
        ans = u;
        return ;
    }
    for(int i = 1; i <= u; i ++ )
    {
        // 可行性剪枝
        if(w[t] + sum[i] <= m)
        {
            sum[i] += w[t];
            dfs(u, t + 1);
            sum[i] -= w[t];
        }
    }
    sum[u + 1] = w[t];
    dfs(u + 1, t + 1);
    sum[u + 1] = 0;
}

int main()
{
    cin >> n >> m;
    for(int i = 0; i < n; i ++) cin >> w[i];
    // 优化搜索顺序
    sort(w, w + n);
    reverse(w, w + n);
    dfs(0, 0);
    
    cout << ans;
    return 0;
}
```



##### 3.[截断数列_AcWing](https://www.acwing.com/problem/content/4304/)

> 给定一个由 $n$ 位数字组成的序列 $a_1a_2…a_n$。
>
> 其中，每个数字都是 $0∼9$ 之一。
>
> 请你判断，能否将数列从中间截断为两个或更多个非空部分，要求每一部分的各位数字之和都相等。
>
> 例如，$350178$ 可以截断为 $3$ 个部分 $350、17、8$，并且满足 $3+5+0=1+7=8$。
>
> #### 输入格式
>
> 第一行包含一个整数 $n$。
>
> 第二行包含 $n$ 个数字 $a_1,a_2,…,a_n$，数字之间不含空格。
>
> #### 输出格式
>
> 如果可以按要求截断数列，则输出 `YES`，否则输出 `NO`。
>
> #### 数据范围
>
> 前 66 个测试点满足 $2≤n≤10$。
> 所有测试点满足 $2≤n≤100$。
>
> #### 输入样例1：
>
> ```
> 5
> 73452
> ```
>
> #### 输出样例1：
>
> ```
> YES
> ```
>
> #### 输入样例2：
>
> ```
> 4
> 1248
> ```
>
> #### 输出样例2：
>
> ```
> NO
> ```

`分析`：题目说最少分成2段，可以枚举这个段数，然后通过$DFS$判断这个方案是否合法

搜索顺序：考虑下一段开始的位置

``` c++
#include <iostream>

using namespace std;
const int N = 110;
int a[N], n;
int s[N];
int len;

// u 表示下一次截断的开始位置
bool dfs(int u)
{
    if(u == n + 1) return true;
    for(int i = u; i <= n; i ++ )
        if(s[i] - s[u - 1] == len && dfs(i + 1)) return true;

    return false;
}

int main()
{
    cin >> n;
    for(int i = 1; i <= n; i ++)
    {
        char ch;
        cin >> ch;
        a[i] = ch - '0';
        s[i] = s[i - 1] + a[i];
    }
    // 枚举分成 k 段
    for(int i = 2; i <= n; i++)
    {
        len = s[n] / i;
        if(s[n] % i == 0 && dfs(1)) 
        {
            puts("YES");
            return 0;
        }
    }
    puts("NO");
    return 0;
}

```

##### 4.[分割回文串_LeetCode](https://leetcode-cn.com/problems/palindrome-partitioning/)

> 给你一个字符串 `s`，请你将 `s` 分割成一些子串，使每个子串都是 **回文串** 。返回 `s` 所有可能的分割方案。
>
> **回文串** 是正着读和反着读都一样的字符串。
>
> #### 输入：
>
> ```
> aab
> ```
>
> #### 输出：
>
> ``` 
> a a b
> 
> aa b
> ```

`分析`：搜索顺序和上道题一样都是枚举下一段的开始位置

``` c++
class Solution {
public:
    vector<vector<string>> ans;
    vector<string> path;

    bool check(int i, int j, string s)
    {
        while(i <= j)
        {
            if(s[i] == s[j]) i ++, j --;
            else return false;
        }
        return true;
    }

    vector<vector<string>> partition(string s) {
        // 枚举下一次切割的位置
        dfs(s, 0);
        return ans;
    }
    void dfs(string& s, int idx)
    {
        if(idx >= s.size())
        {
            ans.push_back(path);
            return ;
        }
        for(int i = idx; i < s.size(); i ++)
        {
            if(check(idx, i, s))
            {
                path.push_back(s.substr(idx, i - idx + 1));
                dfs(s, i + 1);
                path.pop_back();
            }
        }

    }
};
```

