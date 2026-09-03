```bash
# 1. 配置（生成构建系统）
cmake -B build                      # 当前目录源码，build目录存放构建文件
cmake -B build -S .                 # 同上（显式指定源码目录）
cmake -B build -DCMAKE_BUILD_TYPE=Release  # 指定Release模式

# 2. 构建（编译）
cmake --build build                 # 编译（自动使用多核）
cmake --build build --target clean  # 清理
cmake --build build --target install # 安装
cmake --build build --parallel 4    # 指定4核编译

# 3. 运行测试
ctest --test-dir build              # 运行测试
ctest --test-dir build -R test_name # 运行匹配的测试

# 4. 一步命令
cmake -S . -B build && cmake --build build
```