## 开启`Adminstrator`账户

Terminal (Admin) 执行:

```powershell
net user administrator /active:yes
```



## 设置环境变量

添加`c:dev/MinGW64/bin`:

```powershell
setx PATH "%PATH%;C:\dev\MinGW64\bin"
```

检查是否成功添加: (需要重启Terminal)

```powershell
echo %PATH%
```

