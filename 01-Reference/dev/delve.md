## 安装
```bash
go install github.com/go-delve/delve/cmd/dlv@latest
dlv version
```

## 使用
```bash
# 启动调试
dlv debug main.go
# 设置断点
b 报名.函数名
# 运行到断点处
c
# 输出变量内容
p 变量名
# 逐步运行
n
# 单步进入函数
s
# 重新调试
r
# 查看堆栈
bt
# 退出
quit
```

## 线上调试（可能会暂停）
```bash
# 捕捉进程号
ps aux | grep 进程名
# 调试对应进程
dlv attach 进程号
# 设置断点
b 文件名.行号
```


## 并发调试
```bash
# 启动调试
dlv debug main.go
# 设置断点
b 函数/行号
# 查看所有线程
goroutines
# 切换到指定线程
goroutine 线程序号
# 查看当前线程信息
goroutine
```