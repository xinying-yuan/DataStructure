#### 1. 先来记忆一套二分模板

```c++
// 在闭区间[L,R]中的二分写法
int find(int L, int R)
{
  int ans, mid;
  while(L <= R) {
    int mid = L + R >> 1; // 如果怕溢出也可以使用 L + (R -L) / 2;
    if (P(mid)) // 如果条件成立
      ans = mid, R = mid - 1;
    	// 这里只要记录满足条件的mid，最后的循环一定会结束，也一定会在ans中保留正确的答案
    else
      L = mid + 1; // L 和 R不用仔细考虑加1、减1全部都写上去
  }
   return ans;
}
```

### 278. [First bad version](https://leetcode.cn/problems/first-bad-version/description/)

```c++
class Solution {
public:
    int firstBadVersion(int n) {
        int ans, l = 0, r = n;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if(isBadVersion(mid)) {
                ans = mid, r = mid - 1;
            } else 
                l = mid + 1;
        }
        return ans;
    }
};
```

### 2812. [找出最安全的路径 bfs + 二分](https://leetcode.cn/problems/find-the-safest-path-in-a-grid/)

```c++
class Solution {
private:
    const int walk[4][2] = {
        {1, 0},
        {0, 1},
        {-1,0},
        {0, -1}
    };
    typedef pair<int, int> pii;
    typedef vector<vector<int>> vii;
    void bfsUpdate(vii& d, queue<pii> q, int& dmax) {
        int n = d.size();
        vii v(n, vector<int>(n, 0));
        while(!q.empty()) {
            auto it = q.front();
            q.pop();
            // cout << it.first << " " << it.second << endl;
            int val = d[it.first][it.second];
            // int x = it.first, y = it.second;
            // update distance form down right up left, any graceful writing method?
            for (int i = 0; i < 4; i++) {
                int x = it.first + walk[i][0], y = it.second + walk[i][1];
                if (x < n && x >= 0 && y < n && y >= 0 && !v[x][y]) {
                    q.push(pii(x, y));
                    v[x][y] = 1;
                    d[x][y] = min(d[x][y], val + 1);
                    dmax = max(d[x][y] , dmax);
                }
            }
        }
    }
    // 941 / 1035用例通过，快速优化checkconnected的方法
    // 开始减枝处理 980 / 1035个优化
    bool checkConnect(vii d, int target) {
        int n = d.size();
        if(d[0][0] < target || d[n - 1][n - 1] < target) return false;
        vii v(n, vector<int>(n, 0));
        queue<pair<int,int>>q;
        q.push(pii(0,0));
        while(!q.empty()) {
            auto it = q.front();
            q.pop();
            // if (it.first == n - 1 && it.second == n - 1) return true;
            for(int i = 0; i < 4; i++) {
                int x = it.first + walk[i][0], y = it.second + walk[i][1];
                if (x < 0 || y < 0 || x >= n || y >= n || v[x][y] || d[x][y] < target) continue;
                q.push(pii(x, y));
                v[x][y]  = 1;
            }
        }
        return v[n - 1][n - 1];
    }
public:

    int maximumSafenessFactor(vii& grid) {
        // 二分算法
        int n = grid.size();
        if (grid[0][0] == 1 || grid[n - 1][n - 1] == 1) return 0;
        // calclate each cells' distance with the thief
        vii d(n, vector<int>(n, INT_MAX));
        queue<pii> q;
        int dmax = INT_MIN;
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < n; j++) {
                if(grid[i][j] == 1) {
                    d[i][j] = 0;
                    q.push(pii(i,j));
                };
            }
        }
        bfsUpdate(d, q, dmax);
        // binary search
        int l = 0, r = dmax;
        int ans = 0;
        while(l <= r) {
            //cout << "l = " << l << "r = " << r << endl;
            int mid = (l + r) >> 1;
            if (checkConnect(d,mid)) {
                ans = mid; // 路径中的所有的节点的安全系数都大于等于该点
                l = mid + 1;
            } else {
                r = mid - 1;
                //cout << mid << " not satisfy" << endl;
            }
        }
        return ans;
    }
};
```

### 410. [分割数组的最大值](https://leetcode.cn/problems/split-array-largest-sum/)

```c++
class Solution {
public:
    int check(int mid, vector<int>& nums, int k) {
        int cnt = 1, sum = 0; // check 是否可以分割成为m个子数组
        for(auto it : nums) {
            sum += it;
            if (sum > mid) {
                sum = it;
                cnt++;
                if(cnt > k) return false;
            }
        }
        return true;
    }
    int splitArray(vector<int>& nums, int k) {
        int l = 0, r = 0, ans = 0;
        for(auto it: nums) r += it, l = max(l, it);
        while(l <= r) {
            int mid = l + (r - l) / 2;
            if (check(mid, nums, k)) { // 1. 能分割成为m个子数组 2. 每个子数组的和都小于等于mid
                ans = mid, r = mid - 1;
            } else l = mid + 1;
        }
        return ans;
    }
};
```

### 如何判断一个场景适合使用二分算法

```c++
// 如何判断一个场景适合使用二分算法:
// 1. 命题可以被归纳为找到使命题P(x)成立(或者不成立)的最大(或者最小的)的x
// 2. 把P(x)看做一个值为真或假的函数，那么它一定在某个分界线的一侧全部为真，另一侧全部为假
// 3. 可以找到一个复杂度优秀的算法来检验P(x)的真假
// 通俗来讲，二分答案可以用来处理"最大的最小"或者"最小的最大"的问题
```










