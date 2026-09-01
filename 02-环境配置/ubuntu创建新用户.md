# Ubuntu/Debian 系统创建新用户并授权 root 权限流程文档

## 一、创建新用户

### 1.1 使用 adduser 命令（推荐，交互式）

```bash
sudo adduser username
```

执行后系统会提示：
- 输入密码
- 确认密码
- 输入全名（可选，直接回车跳过）
- 输入房间号（可选）
- 输入工作电话（可选）
- 输入家庭电话（可选）
- 其他信息（可选）
- 最后确认信息正确输入 `Y`

### 1.2 使用 useradd 命令（非交互式）

```bash
# 创建用户但不创建家目录
sudo useradd username

# 创建用户并创建家目录
sudo useradd -m username

# 创建用户并指定 shell
sudo useradd -m -s /bin/bash username

# 设置密码
sudo passwd username
```

| 参数 | 说明 |
|------|------|
| `-m` | 创建用户主目录 |
| `-s /bin/bash` | 指定默认 shell |
| `-d /home/username` | 指定家目录路径 |
| `-G group1,group2` | 添加到指定组 |

## 二、授权 root 权限

### 2.1 方法一：将用户添加到 sudo 组（推荐）

```bash
# 将用户添加到 sudo 组
sudo usermod -aG sudo username

# 验证用户是否在 sudo 组中
groups username
```

### 2.2 方法二：编辑 sudoers 文件

```bash
# 使用 visudo 编辑器（安全）
sudo visudo
```

在文件中添加以下内容之一：

```bash
# 方式1：允许用户执行所有sudo命令（需要密码）
username ALL=(ALL:ALL) ALL

# 方式2：允许用户执行所有sudo命令（无需密码）
username ALL=(ALL:ALL) NOPASSWD:ALL

# 方式3：允许用户执行特定命令
username ALL=(ALL) /bin/systemctl, /usr/bin/apt-get
```

**visudo 语法说明：**
```
用户名 主机=(以谁的身份) 允许执行的命令
  ↓       ↓          ↓            ↓
username ALL=(ALL:ALL) ALL
```

| 位置 | 说明 |
|------|------|
| `username` | 目标用户名 |
| `ALL` | 所有主机 |
| `(ALL:ALL)` | 可以切换到任何用户和组 |
| `ALL` | 可以执行任何命令 |
| `NOPASSWD:ALL` | 执行命令时无需输入密码 |

### 2.3 方法三：创建 sudo 组配置文件

```bash
# 创建用户专属 sudo 配置文件
sudo nano /etc/sudoers.d/username
```

添加内容：
```bash
username ALL=(ALL:ALL) ALL
```

设置权限：
```bash
sudo chmod 0440 /etc/sudoers.d/username
```

## 三、验证配置

### 3.1 验证用户组成员身份

```bash
# 查看用户所属组
groups username

# 或查看 /etc/group 文件
grep sudo /etc/group
```

### 3.2 测试 sudo 权限

```bash
# 切换到新用户
su - username

# 测试 sudo 命令（需要输入用户密码）
sudo whoami
# 输出应该是：root

# 测试列出 root 文件
sudo ls -la /root

# 如果配置了 NOPASSWD，再次测试应无需密码
```

### 3.3 查看 sudo 日志

```bash
# 查看 sudo 日志
sudo cat /var/log/auth.log | grep username
```

## 四、SSH 密钥配置（可选）

如果需要远程登录，配置 SSH 密钥更安全。

### 4.1 生成 SSH 密钥对

```bash
# 切换到新用户
su - username

# 生成密钥对
ssh-keygen -t ed25519 -C "user@example.com"
# 或使用 RSA
ssh-keygen -t rsa -b 4096 -C "user@example.com"
```

### 4.2 配置公钥

```bash
# 将公钥添加到 authorized_keys
mkdir -p ~/.ssh
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### 4.3 配置 SSH 服务

编辑 `/etc/ssh/sshd_config`：

```bash
sudo nano /etc/ssh/sshd_config
```

推荐配置：
```bash
# 禁用 root 登录
PermitRootLogin no

# 仅允许密钥登录
PasswordAuthentication no

# 限制可登录用户
AllowUsers username

# 禁用空密码
PermitEmptyPasswords no
```

重启 SSH 服务：
```bash
sudo systemctl restart sshd
# 或
sudo systemctl restart ssh
```

## 五、安全最佳实践

| 建议 | 说明 |
|------|------|
| 使用强密码 | 至少 12 位，包含大小写字母、数字、特殊字符 |
| 启用密码复杂度 | 安装 `libpam-pwquality` 配置密码策略 |
| 定期轮换密码 | 每 90 天更换一次密码 |
| 记录 sudo 操作 | 在 `/etc/sudoers` 添加日志记录 |
| 限制 sudo 权限 | 只授予必要的命令权限 |
| 启用双因素认证 | 使用 Google Authenticator 等工具 |
| 监控登录日志 | 定期检查 `/var/log/auth.log` |

### 5.1 配置密码策略

安装密码质量工具：
```bash
sudo apt update
sudo apt install libpam-pwquality
```

编辑 `/etc/security/pwquality.conf`：
```bash
# 最小长度
minlen = 12

# 至少包含小写字母
lcredit = -1

# 至少包含大写字母
ucredit = -1

# 至少包含数字
dcredit = -1

# 至少包含特殊字符
ocredit = -1
```

### 5.2 配置 sudo 日志

在 `/etc/sudoers` 中添加：
```bash
Defaults log_input,log_output
Defaults logfile="/var/log/sudo.log"
```

创建日志文件：
```bash
sudo touch /var/log/sudo.log
sudo chmod 600 /var/log/sudo.log
sudo chown root:root /var/log/sudo.log
```

## 六、完整示例

```bash
#!/bin/bash
# 创建新用户并配置 sudo 权限

# 设置用户名
USERNAME="newuser"

# 1. 创建用户
sudo adduser $USERNAME

# 2. 添加到 sudo 组
sudo usermod -aG sudo $USERNAME

# 3. 验证配置
echo "=== 用户信息 ==="
id $USERNAME

echo "=== 用户组 ==="
groups $USERNAME

echo "=== 测试 sudo 权限 ==="
su - $USERNAME -c "sudo whoami"

echo "=== 完成 ==="
```

## 七、常见问题

| 问题 | 解决方案 |
|------|----------|
| 用户不在 sudo 组 | 使用 `sudo usermod -aG sudo username` |
| sudo 命令需要 root 密码 | 确保用户在 sudo 组或配置了 sudoers |
| 无法执行某命令 | 检查 sudoers 中是否限制了该命令 |
| SSH 无法登录 | 检查 `/etc/ssh/sshd_config` 配置 |
| 提示 "is not in the sudoers file" | 使用 `visudo` 添加用户配置 |

## 八、删除用户（如需清理）

```bash
# 删除用户但保留家目录
sudo deluser username

# 删除用户和家目录
sudo deluser --remove-home username

# 删除用户、家目录和所有文件
sudo deluser --remove-all-files username
```

---

**注意事项：**
- 始终使用 `visudo` 而非直接编辑 `/etc/sudoers`
- 测试 sudo 权限前先确保有其他 root 访问方式
- 生产环境建议使用 SSH 密钥而非密码认证
