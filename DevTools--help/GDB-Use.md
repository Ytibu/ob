
## GDB安装

```
# Ubuntu/Debian
sudo apt-get install gdb

# 安装验证
gdb --version
```

## 基本GDB命令

### gdb 图形界面
```
# 直接启动代码可视化窗口
gdb -tui ./program

# 使用过程中切换
(gdb) layout src  # 显示源代码窗口
(gdb) layout asm  # 显示汇编窗口
(gdb) layout regs # 显示寄存器窗口
(gdb) layout split # 同时显示源代码和汇编
```

### 启动和退出gdb
```
# 生成可执行文件
gcc -g program.c -o program

# 启动 gdb 并加载程序
gdb ./program

# 启动 gdb 并附加到正在运行的进程
gdb -p PID

# 退出 gdb
(gdb) quit
# 或简写
(gdb) q
```

### 运行程序
```
# 运行程序
(gdb) run
# 或简写
(gdb) r

# 带参数运行
(gdb) run arg1 arg2
```

### 断点管理
```
# 在指定行设置断点
(gdb) break 10
# 或简写
(gdb) b 10

# 在函数入口设置断点
(gdb) break main
(gdb) break function_name

# 查看所有断点
(gdb) info breakpoints

# 删除断点
(gdb) delete 1  # 删除编号为1的断点
(gdb) delete    # 删除所有断点
```

### 程序执行控制
```
# 继续执行直到下一个断点
(gdb) continue
# 或简写
(gdb) c

# 单步执行（进入函数）
(gdb) step
# 或简写
(gdb) s

# 单步执行（不进入函数）
(gdb) next
# 或简写
(gdb) n

# 执行完当前函数并返回
(gdb) finish
```

### 代码查看
```
# 查看当前行附近的代码
(gdb) list
# 或简写
(gdb) l

# 查看指定行附近的代码
(gdb) list 15

# 查看指定函数的代码
(gdb) list main
```

### 检查变量和内存
```
# 打印变量值
(gdb) print variable_name
# 或简写
(gdb) p variable_name

# 修改变量值
(gdb) print variable_name = new_value

# 查看变量类型
(gdb) ptype variable_name

# 查看内存内容
(gdb) x/10xw &amp;variable  # 以16进制查看10个字(word)
```

### 调用栈分析
```
# 查看调用栈
(gdb) backtrace
# 或简写
(gdb) bt

# 切换到指定栈帧
(gdb) frame 2
# 或简写
(gdb) f 2
```

## 高级调试技巧

### 条件断点
```
# 当i等于5时触发断点
(gdb) break 10 if i == 5
```

### 观察点
```
# 当变量被修改时暂停
(gdb) watch variable_name

# 当变量被读取时暂停
(gdb) rwatch variable_name

# 当变量被读取或修改时暂停
(gdb) awatch variable_name
```

### 多线程调试
```
# 查看所有线程
(gdb) info threads

# 切换到指定线程
(gdb) thread 2

# 只允许当前线程执行
(gdb) set scheduler-locking on
```

### 调试核心转储文件
```
# 加载核心转储文件
gdb ./program core

# 查看崩溃时的调用栈
(gdb) bt
```

## 实用GDB配置
```
# 显示漂亮的打印
set print pretty on

# 设置历史记录大小
set history save on
set history size 1000

# 自定义命令
define printarray
    set $i = 0
    while $i &lt; $arg0
        printf &quot;array[%d] = %dn&quot;, $i, $arg1[$i]
        set $i = $i + 1
    end
end
```

***注意***： 优化可能会改变代码执行顺序，使调试变得困难。建议调试时关闭优化
```
gcc -O0 -g program.c -o program
```