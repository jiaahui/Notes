# Search

## Binary Search

### 整数二分

```c++
int left = -1, right = n;
while (left + 1 != right) {
    int mid = left + right >> 1;
    // 重点是左右两侧按什么条件划分，根据这个划分条件来更新 left 和 right
    if (check(arr[mid])) right = mid;
    else left = mid;
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
}
```

### 浮点数二分

```c++
const double eps = 1e-6;  // e.g. 要求精确到1e-4, 那么 eps = 1e-6
double left = -1, right = n;
while (right - left > eps) {
    double mid = (left + right) / 2;
    if (check(mid)) right = mid;
    else left = mid;
}
```
