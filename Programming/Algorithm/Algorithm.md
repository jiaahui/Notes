# Algorithm

## Sort

### Quick Sort

```c++
/* 快速排序 */
void quick_sort(int arr[], int left, int right) {
    // 递归结束条件
    if (left >= right) return;
    // 核心
    int pivot = arr[left + right >> 1];  // 枢轴
    int i = left, j = right;
    while (left <= right) {
        while (arr[i] < pivot) i++;
        while (arr[j] > pivot) j--;
        if (i <= j) {
            swap(arr[i], arr[j]);
            i++, j--;
        }
    }
    // 分治
    quick_sort(arr, left, j);
    quick_sort(arr, i, right);
}
```

#### Standard Library

Ref: https://hackingcpp.com/cpp/std/algorithms.html#sort

![image.png](https://raw.githubusercontent.com/jiaahui/Pictures/main/sort.webp)

### Merge Sort

```c++
int tmp[N];
/* 归并排序 */
void merge_sort(int arr[], int left, int right) {
    // 递归结束条件
    if (left >= right) return;
    // 分治
    int mid = left + right >> 1;
    merge_sort(arr, left, mid);
    merge_sort(arr, mid + 1, right);
    // 核心
    int k = 0, i = left, j = mid + 1;
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) tmp[k++] = arr[i++];
        else tmp[k++] = arr[j++];
    }
    while (i <= mid) tmp[k++] = arr[i++];
    while (j <= right) tmp[k++] = arr[j++];
    // tmp -> arr
    for (int p = left; p <= right; p++) arr[p] = tmp[p - left];
}
```

#### 逆序对

```c++
/* 计算逆序对的数量 */
long long merge_sort(int arr[], int left, int right) {
    // 递归结束条件
    if (left >= right) return;
    // 分治
    int mid = left + right >> 1;
    long long cnt = merge_sort(arr, left, mid) + merge_sort(arr, mid + 1, right);  // 递归
    // 核心
    int k = 0, i = left, j = mid + 1;
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) tmp[k++] = arr[i++];
        else {
            tmp[k++] = arr[j++];
            cnt += mid - i + 1;  // <核心>
        }
    }
    while (i <= mid) tmp[k++] = arr[i++];
    while (j <= right) tmp[k++] = arr[j++];
    // tmp -> arr
    for (int p = left; p <= right; p++) arr[p] = tmp[p - left];
    return cnt;  // 逆序对
}
```



## Search

### Binary Search

#### 整数

```c++
/* 在数组 arr 的[0, n-1]区间二分 */
int left = -1, right = n;  // 左边界的前一个元素 右边界的后一个元素
while (left + 1 != right) {
    int mid = left + right >> 1;
    if (arr[mid] >= e) right = mid;  // 右侧 >= e
    else left = mid;  // 左侧 < e
}
/**
 * 循环结束的时候 left + 1 == right
 * 左侧符合某个条件 右侧属于另一个条件
 *
 * 例如: 查找递增数组中第一个大于等于 target 的元素
 * 左侧: 小于 target  右侧: 大于等于 target
 * if (arr[mid] >= target) mid 位置的元素属于右侧 更新 right = mid
 * else 属于左侧 更新 left = mid
 * 循环结束后 right 指向第一个大于等于 target 的元素
 *
 * 例如: 查找递增数组中最后一个小于等于 target 的元素
 * 左侧: 小于等于 target  右侧: 大于 target
 * if (arr[mid] <= target) mid 位置的元素属于左侧 更新 left = mid
 * else 属于右侧 更新 right = mid
 * 循环结束后 left 指向最后一个小于等于 target 的元素
 */
```

#### 浮点数

```c++
/* 二分法计算三次方根 对应库函数 cbrt() */
const double eps = 1e-8;  // 一般设置为精度要求的百分之一
double left = -10000, right = 10000;
while (right - left >= eps) {
    double mid = (left + right) / 2;
    if (mid * mid * mid >= n) right = mid;
    else left = mid;
}
cout << fixed << setprecision(6) << left << endl;  // #include <iomanip>
```

#### Standard Library

![image.png](https://raw.githubusercontent.com/jiaahui/Pictures/main/binary_search.webp)

## 高精度算术计算

### 加法

```c++
/* 高精度加法 */
string add(string num1, string num2) {
    reverse(num1.begin(), num1.end());
    reverse(num2.begin(), num2.end());
    string result;
    int carry = 0;  // 进位
    int i = 0;
    while (i < num1.size() || i < num2.size()) {
        if (i < num1.size()) carry += num1[i] - '0';
        if (i < num2.size()) carry += num2[i] - '0';
        result += carry % 10 + '0';
        carry /= 10;
        i++;
    }
    // 剩余进位处理
    if (carry > 0) result += carry + '0';
    reverse(result.begin(), result.end());
    return result;
}
```

### 减法

```c++
/* 高精度减法 较大的正数 - 较小的正数 */
string sub(string num1, string num2) {
    string result;
    reverse(num1.begin(), num1.end());
    reverse(num2.begin(), num2.end());
    int borrow = 0;  // 借位
    for (int i = 0; i < num1.size(); i++) {
        borrow = num1[i] - '0' - borrow;
        if (i < num2.size()) borrow -= num2[i] - '0';
        result += (borrow + 10) % 10 + '0';  // 模运算避免负值
        borrow = borrow < 0 ? 1 : 0;  // 更新借位标记
    }
    // 先导零处理
    while (result.size() > 1 && result.back() == '0') result.pop_back();
    reverse(result.begin(), result.end());
    return result;
}
```

### 乘法

```c++
/* 高精度乘法 较大的正数 * 普通正数 */
string mul(string num1, int num2) {
    string result;
    reverse(num1.begin(), num1.end());
    int n = num1.size();
    int tmp = 0;
    // 乘法逻辑 注意循环条件
    for (int i = 0; i < n || tmp; i++) {
        if (i < n) tmp += (num1[i] - '0') * num2;
        result += tmp % 10 + '0';
        tmp /= 10;
    }
    // 先导零处理
    while (result.size() > 1 && result.back() == '0') result.pop_back();
    reverse(result.begin(), result.end());
    return result;
}
```

### 除法

```c++
/* 高精度除法 较大的正数 / 普通正数 */
pair<string, int> div(string num1, int num2) {
    string result;
    int rem = 0;
    for (int i = 0; i < num1.size(); i++) {
        rem = rem * 10 + num1[i] - '0';
        result += rem / num2 + '0';
        rem %= num2;
    }
    while (result.size() > 1 && result.front() == '0') result.erase(0, 1);  // 删除从 0 开始的 1 个字符
    return {result, rem};
}
```



## 前缀和差分

前缀和是一种数据预处理的方法，适用于快速查询区间的值的情况

差分是前缀的逆运算，适用于操作区间的情况

### 一维前缀

```c++
for (int i = 1; i <= n; i++) prefix[i] = prefix[i-1] + arr[i];  // 索引 0 留空可以简化编程
// 区间 left - right 的和
int result = prefix[right] - prefix[left - 1];
```

### 二维前缀

```c++
for (int i = 1; i <= n; i++) 
    for (int j = 1; j <= m; j++)
    	prefix[i][j] = prefix[i-1][j] + prefix[i][j-1] - prefix[i-1][j-1] + arr[i][j];
// 区域 x1,y1 - x2,y2 的和
int result = prefix[x2][y2] - prefix[x2][y1 - 1] - prefix[x1 - 1][y2] + prefix[x1 - 1][y1 - 1];
```

### 一维差分

```c++
/* 差分定义构造 */
for (int i = 1; i <= n; i++) difference[i] = arr[i] - arr[i - 1];
/* 通过插入构造  [left, right] 区间每个数 + value */
void insert(int left, int right, int value) {
    difference[left] += value;
    difference[right + 1] -= value;
}

for (int i = 1; i <= n; i++) insert(i, i, arr[i]);
```

### 二维差分

```c++
void insert(int x1, int y1, int x2, int y2, int val) {
    diff[x1][y1] += val;
    diff[x1][y2+1] -= val;
    diff[x2+1][y1] -= val;
    diff[x2+1][y2+1] += val;
}
/* 差分 -> 原二维数组 */
for (int i = 1; i <= n; i++) 
    for (int j = 1; j <= m; j++) 
        arr[i][j] = arr[i - 1][j] + arr[i][j - 1] - arr[i-1][j-1] + diff[i][j];
```



## 双指针

双指针是一种解决问题的思想，通常可以将 $O(n^2)$ 时间复杂度降低到 $O(n)$ 时间复杂度

常见的双指针有：

- 快慢指针
- 相向指针

## 位运算

```c++
/* 判断第 i 位是 1 或 0 */
bool is_one = x >> i & 1;
/* 截取二进制表示中的最后一个 1 和后面的全部 0 */
// e.g.  10100010011000100010  -> 10
int lowbit(int x) {
    return x & (-x);
}
```



## 离散化

```c++
vector<int> nodes;  // 存储使用到的数轴的点
sort(nodes.begin(), nodes.end());  // 排序
nodes.erase(unique(nodes.begin(), nodes.end()), nodes.end());  // 去重操作

int find(int x) {
    int left = 0, right = alls.size() - 1;
    while (left < right) {
        int mid = (left + right) >> 1;
        if (nodes[mid] >= x) right = mid;
        else left = mid + 1;
    }
    return right + 1;
}
```



## 单链表

```c++
// head: 头节点索引
// n[i]: 数据
// ne[i]: 指针
// idx: 下一个位置的指针
int head, n[N], ne[N], idx;

void init() {
    head = -1;
    idx = 0;
}
// 头节点后插入节点
void insert_to_head(int x) {
    n[idx] = x;
    ne[idx] = head;
    head = idx;
    idx++;
}
// e[k] 后插入新节点
void insert(int k, int x) {
    n[idx] = x;
    ne[idx] = ne[k];
    ne[k] = idx;
    idx++;
}
// 删除 e[k]
void remove(int k) {
    ne[k] = ne[ne[k]];
}
```



## 双链表

```c++
int e[N], left[N], right[N], idx;

void init() {
    right[0] = 1;  // e[0]: 空左端点
    left[1] = 0;   // e[1]: 空右端点
    idx = 2;
}
// 在 e[k] 右侧插入新节点
void insert(int k, int x) {
    e[idx] = x;
    // 先左后右调整指针
    left[idx] = k;
    right[idx] = right[k];
    left[right[k]] = idx;
    right[k] = idx;
    idx++;
}
// 删除 e[k] 节点
void remove(int k) {
    left[right[k]] = left[k];
    rigth[left[k]] = right[k];
}
```



## 模拟栈

```c++
int e[N], idx;

void init() {
    idx = 0;
}

void push(int x) {
    e[idx++] = x;
}

void pop() {
    idx--;
}

bool empty() {
    return idx == 0;
}

int top() {
    if (!empty()) return e[idx - 1];
    else return 0x3f3f3f3f;
}
```



## 表达式求值

```c++
#include <iostream>
#include <stack>
#include <string>
#include <map>

using namespace std;

stack<int> nums;  // 数字栈
stack<char> ops;  // 符号栈
map<int, int> rk{ {'+', 1}, {'-', 1}, {'*', 2}, {'/', 2} };
// 计算表达式
void eval() {
    int num2 = nums.top();
    nums.pop();
    int num1 = nums.top();
    nums.pop();
    char op = ops.top();
    ops.pop();
    int result;
    if (op == '+') result = num1 + num2;
    else if (op == '-') result = num1 - num2;
    else if (op == '*') result = num1 * num2;
    else result = num1 / num2;
    
    nums.push(result);
}

int main() {
    string s;
    cin >> s;
    for (int i = 0; i < s.size(); i++) {
        if (isdigit(s[i])) {
            // 连续数字
            int num = 0, j = i;
            while (j < s.size() && isdigit(s[j])) {
                num = num * 10 + s[j] - '0';
                j++;
            }
            nums.push(num);
            i = j - 1;
        }
        else if (s[i] == '(') ops.push(s[i]);
        else if (s[i] == ')') {
            while (ops.top() != '(') eval();
            ops.pop();
        }
        else {
            // 关键: 操作符优先级
            while (!ops.empty() && rk[ops.top()] >= rk[s[i]]) eval();
            ops.push(s[i]);
        }
    }
    
    while (!ops.empty()) eval();
    cout << nums.top() << endl;
    return 0;
}
```

## 单调栈

```c++
/* 递增单调栈: 输出左侧第一个比它小的元素 */
int stk[N], idx;

while (idx && stk[idx] >= x) idx--;  // 如果栈顶元素大于当前值 栈顶元素出栈 保证栈中元素递增
if (idx == 0) cout << -1 << " ";  // 不存在比它小的元素
else cout << stk[idx] << " ";  // 查找成功
stk[++idx] = x;  // 当前值插入单调栈
```



## 单调队列 (滑动窗口)

题型: 查找滑动窗口中的最小值/最大值

```c++
// 假设窗口大小 k
for (int i = 0; i < n; i++) {
    while (q.size() && check_head(q.front()) q.pop();
    while (q.size() && check(q.front(), i)) tt -- ;
    q.push(i);
}
```



## KMP 模式匹配

```c++
char s[1000010], p[100010];  // s 和 p 从索引 1 开始存储字符串
int ne[100010];  // 使用 ne 避免和标准库 next 重名
// 计算 next 数组
for (int i = 2, j = 0; i < m; i++) {
    while (j && p[i] != p[j+1]) j = ne[j];
    if (p[i] == p[j+1]) j++;
    ne[i] = j;
}
// 匹配过程
for (int i = 1, j = 0; i < n; i++) {
    while (j && s[i] != p[j+1]) j = ne[j];
    if (s[i] == p[j+1]) j++;
    if (j == m) {
        // 成功匹配 输出 s 的起始坐标
        cout << i - m << endl;
        // 继续匹配 如果不需要继续匹配可以直接 break
        j = ne[j];
    }
}
```



## Tire 字典树

```c++
int son[N][26], cnt[N], idx;

void insert(char *str) {
    int p = 0;
    for (int i = 0; str[i]; i++) {
        int u = str[i] - 'a';
        if (!son[p][u]) son[p][u] = ++idx;
        p = son[p][u];
    }
    cnt[p]++;
}

int query(char *str) {
    int p = 0;
    for (int i = 0; str[i]; i++) {
        int u = str[i] - 'a';
        if (!son[p][u]) return 0;
        p = son[p][u];
    }
    return cnt[p];
}
```



## 并查集

```c++

```



## Heap

### Heap sort



### Heap



## Hash Table

### Hash Table



### String Hash





## Graph

Dijkstra: 不允许负权和自环  源点到汇点的最短路

bellman-ford: 适用于存在经过的边数有限制的问题  全局最短路

Bellman - ford 算法是求含负权图的单源最短路径的一种算法，效率较低，代码难度较小。其原理为连续进行松弛，在每次松弛时把每条边都更新一下，若在 n-1 次松弛后还能更新，则说明图中有负环，因此无法得出结果，否则就完成。

### Dijkstra

```c++
int edges[N][N];  // 使用邻接矩阵保存边
int distances[N];  // 1号点到其他点的距离  从索引1开始
int state[N];  // 是否已经找到到达该点的最短距离

int dijkstra(int n) {  // n: 表示节点的数量
    memset(distances, 0x3f, sizeof(distances));  // 初始化起点到终点的距离
    distances[1] = 0;  // 起点
    for (int i = 1; i < n; i++) {  // 控制循环数量 无实际意义
        int tmp = -1;  // 保存最短距离的节点
        for (int j = 1; j <= n; j++) {  // 循环遍历未找到最短路的节点
            if (!state[j] && (tmp == -1 || distances[j] < distances[tmp])) tmp = j;
        }
        state[tmp] = 1;  // 本轮循环中找到的最短距离的点
        for (int j = 1; j <= n; j++) {  // 使用这个点更新其它未找到最短距离的点的距离
            distances[j] = min(distances[j], distances[tmp] + edges[tmp][j]);
        }
    }
    return distances[n] == 0x3f3f3f3f ? -1 : distances[n];  // 判断是否找到最短路
}
```

使用堆优化的`Dijkstra`

```c++
const int N = 1000010;
int h[N], e[N], w[N], ne[N], idx;
int n, m;
int distances[N], states[N];

void add(int a, int b, int c) {
    e[idx] = b;
    w[idx] = c;
    ne[idx] = h[a];
    h[a] = idx++;
}

int dijkstra() {
    memset(distances, 0x3f, sizeof(distances));  // 初始化距离
    distances[1] = 0;  // 起点
    // priority_queue<pair<int, int>> heap;  // 默认最大堆
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> heap;  // 最小堆
    heap.push({0, 1});  // <距离, 编号>
    while (heap.size()) {
        auto pr = heap.top();  // 堆优化加速了查找最小距离的节点的时间
        heap.pop();
        int d = pr.first, ver = pr.second;
        if (states[ver]) continue;
        states[ver] = 1;
        for (int i = h[ver]; i != -1; i = ne[i]) {
            int j = e[i];
            if (distances[j] > distances[ver] + w[i]) {
                distances[j] = distances[ver] + w[i];
                heap.push({distances[j], j});  // 注意
            }
        }
    }
    return distances[n] == 0x3f3f3f3f ? -1 : distances[n];
}
```

### Bellman Ford

```c++
const int N = 510, M = 10010;
struct {
    int a;
    int b;
    int w;
} edges[M];  // 边
int dist[N], back[N];
int n, m, k;

int bellman_ford() {
    memset(dist, 0x3f, sizeof(dist));
    dist[1] = 0;
    // k次循环 k是经过边数的限制
    for (int i = 0; i < k; i++) {
        memcpy(back, dist, sizeof(dist));
        // 遍历所有边
        for (int j = 0; j < m; j++) {
            int a = edges[j].a, b = edges[j].b, w = edges[j].w;
            dist[b] = min(dist[b], back[a] + w);
        }
    }
    return dist[n];  // 注意根据 dist[n] > 0x3f3f3f3f / 2  判断是否有解
}
```

### SPFA

```c++
int h[N], e[N], w[N], ne[N], idx;
int dist[N], st[N];

void add(int a, int b, int c) {
    e[idx] = b;
    w[idx] = c;
    ne[idx] = h[a];
    h[a] = idx++;
}

int spfa() {
    memset(dist, 0x3f, sizeof(dist));
    dist[1] = 0;
    queue<int> q;
    q.push(1);
    st[1] = 1;
    
    while (q.size()) {
        int t = q.front();
        q.pop();
        st[t] = 0;
        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            if (dist[j] > dist[t] + w[i]) {
                dist[j] = dist[t] + w[i];
                if (!st[j]) {
                    q.push(j);
                    st[j] = 1;
                }
            }
        }
    }
    
    return dist[n];
}
```

### Floyd

```c++
// 初始化：
    for (int i = 1; i <= n; i ++ )
        for (int j = 1; j <= n; j ++ )
            if (i == j) d[i][j] = 0;
            else d[i][j] = INF;

// 算法结束后，d[a][b]表示a到b的最短距离
void floyd() {
    for (int k = 1; k <= n; k ++ )
        for (int i = 1; i <= n; i ++ )
            for (int j = 1; j <= n; j ++ )
                d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
}
```

### Prim

```c++
void prim() {
    memset(dt, 0x3f, sizeof(dt));
    int ret = 0;  // 最小生成树的权重之和
    dt[1] = 0;
    for (int i = 0; i < n; i++) {
        int t = -1;  // 距离连通块权重最小的节点
        for (int j = 1; j <= n; j++) {
            if (!st[j] && (t == -1 || dt[j] < dt[t])) t = j;
        }
        // 判断孤立点
        if (dt[t] == 0x3f3f3f3f) {
            cout << "impossible";
            return;
        }
        st[t] = 1;
        ret += dt[t];
        // 更新其它节点的距离
        for (int j = 1; j <= n; j++) {
            if (!st[j] && g[t][j] < dt[j]) {
                dt[j] = g[t][j];
                // pre[j] = t;  // 标记节点的前驱 如果需要输出最小生成树的结构的话 需要这行代码
            }
        }
    }
    cout << ret;
}
```

### Krskl

```c++

```

### 匈牙利算法 $O(nm)$

```c++
int h[500], e[100010], ne[100010], idx;  // 邻接表
int st[510], match[510];

void add(int a, int b) {
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}
// 匹配
bool find(int x) {
    for (int i = h[x]; i != -1; i = ne[i]) {
        int b = e[i];
        if (!st[b]) {
            st[b] = 1;
            if (match[b] == 0 || find(match[b])) {
                match[b] = x;
                return true;
            }
        }
    }
    return false;
}

int ret = 0;
for (int i = 1; i <= n1; i++) {
    memset(st, 0, sizeof st);
    if (find(i)) ret++;
}
```





## Tricks

- 当数据规模超过一百万量级的时候，需要使用快速输入输出或者`scanf`和`printf`

  ```c++
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);
  // 注意使用以上设置的时候  scanf 和 printf 失效无法使用
  ```

- `cout << fixed << setprecision(6) << var << endl; // #incldue <iomanip>` cout 设置小数点后位数

- 无穷大一般使用 0x3f3f3f3f 表示 1061109567 `const int INF = 0x3f3f3f3f;`

- `memset(arr, 0x3f, sizeof(arr));  // #include <cstring>` memset() 以字节为单位批量赋值数组 常用的填充值有 0 (全零) , -1 (全1) 和 0x3f

