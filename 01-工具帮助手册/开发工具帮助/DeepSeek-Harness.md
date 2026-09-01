## DeepSeek Harness（dsh）

DeepSeek 官方开源的 Agent 运行时框架（Node.js，MIT），"一切皆插件"，基于 Cordis。
官方仓库：https://github.com/deepseek-ai/deepseek-harness

### 本机安装情况（2026-09-02 检查）
- 已通过 npm 全局安装：`@deepseek-ai/dsh`（D:\Program Files\nodejs\node_modules\@deepseek-ai\dsh）
- 当前版本：0.1.1-rc.2，与 npm 最新版一致
- 运行环境：Node.js v24.19.0（需 Node 22+）

### 常用命令
```bash
dsh web                      # 启动 Web UI，自动打开浏览器，默认 http://127.0.0.1:3080
dsh web --no-open            # 只启动服务，不打开浏览器
dsh --version                # 查看版本
dsh --help                   # 查看帮助
dsh --profile headless "任务描述"   # 无界面模式直接执行一次任务
```

### 注意事项
- 配置文件与插件安装在用户目录 `C:\Users\dar06\.dsh` 下
- 开发者预览版迭代很快，会有破坏性变更，注意跟进更新
- 该框架可执行本地命令和代码，接入模型 API 密钥后请留意安全边界
