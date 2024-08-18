# High Precision Arithmetic

## 加法

```c++
string add(string num1, string num2) {
    int carry = 0;  // 进位
    string result;  // 结果
    int n = num1.size();
    int m = num2.size();
    reverse(num1.begin(), num1.end());
    reverse(num2.begin(), num2.end());
    // 加法逻辑
    for (int i = 0; i < max(n, m) || carry; i++) {
        int x = i < n ? num1[i] - '0' : 0;
        int y = i < m ? num2[i] - '0' : 0;
        int sum = x + y + carry;
        carry = sum / 10;
        result += sum % 10 + '0';
    }
    // 处理剩余进位
    if (carry) result += carry + '0';
    reverse(result.begin(), result.end());
    return result;
}
```

# 减法

```c++
string sub(string num1, string num2) {
    reverse(num1.begin(), num1.end());
    reverse(num2.begin(), num2.end());
    int borrow = 0;  // 借位
    // 减法逻辑
    for (int i = 0; i < n; i++) {
        borrow = num1[i] - t;
        if (i < num2.size()) borrow -= num2[i] - '0';
        result += (borrow - '0' + 10) % 10 + '0';
        t = t < 0 ? 1 : 0;  // 借位标记
    }
    // 处理先导零
    while (result.size() > 1 && result.back() == '0') result.pop_back();
    reverse(result.begin(), result.end());
    return result;
}
```

## 乘法

```c++
string mul(string num1, int num2) {
    int n = num1.size();
    reverse(num1.begin(), num1.end());
    string result;
    // 乘法逻辑
    int t = 0;
    for (int i = 0; i < n || t; i++) {
        if (i < n) t += (num1[i] - '0') * num2;
        result += t % 10 + '0';
        t /= 10;
    }
    // 处理先导零
    while (result.size() > 1 && result.back() == '0') result.pop_back();
    reverse(result.begin(), result.end());
    return result;
}
```

## 除法

```c++
string div(string num1, int num2, int &rem) {
    reverse(num1.begin(), num1.end());
    string result;
    rem = 0;
    // 除法逻辑
    for (int i = 0; i < num1.size(); i--) {
        rem = rem * 10 + num1[i] - '0';
        result += rem / num2 + '0';
        rem %= num2;
    }
    reverse(result.begin(), result.end());
    // 处理先导零
    while (result.size() > 1 && result.back() == '0') result.pop_back();
    reverse(result.begin(), result.end());
    return result;
}
```
