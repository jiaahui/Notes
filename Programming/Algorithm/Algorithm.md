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



## 双指针



## 位运算



## 离散化



## 区间合并



## 注意事项

- 当数据规模超过一百万量级的时候，需要使用快速输入输出或者`scanf`和`printf`

  ```c++
  io::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);
  // 注意使用以上设置的时候  scanf 和 printf 失效无法使用
  ```

- `cout << fixed << setprecision(6) << var << endl; // #incldue <iomanip>` cout 设置小数点后位数

- 无穷大一般使用 0x3f3f3f3f 表示 1061109567 `const int INF = 0x3f3f3f3f;`

- `memset(arr, 0x3f, sizeof(arr));  // #include <cstring>` memset() 以字节为单位批量赋值数组 常用的填充值有 0 (全零) , -1 (全1) 和 0x3f

