## EOF

如何在键盘上模拟`EOF`？

- UNIX (MacOS): `Ctrl + D`
- Windows: `Ctrl + Z, Enter`

## 输入输出重定向

使用输入参出重定向可以简化`debug`过程中重复输入的步骤

### 命令行

假设程序名为`prog`，将输入保存在`input.txt`文件中，程序将输出保存在`output.txt`文件中

```bash
prog <input.txt >output.txt
```
