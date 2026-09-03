## 列表查看
```powershell
#查看可安装子系统
wsl --list --online

#查看已安装子系统
wsl --list --verbose

#查看正运行子系统
wsl --list --running
```

## 常规设置
```powershell
wsl --help

# 设置默认子系统
wsl --set-default <Distribution Name>

# 导出分发
wsl --export <Distribution Name> <FileName.tar>
# 导入发行版
wsl --import <Distribution Name> <InstallLocation> <FileName.tar>
```
## 系统操作
```powershell
# 彻底关闭WSL
wsl --shutdown

# 终止
wsl --terminate <Distribution Name>

# 注销和卸载 WSL 分发版
wsl --unregister <DistributionName>
```