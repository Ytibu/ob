## 安装
```
# 安装(管理员权限)
npm install -g @anthropic-ai/claude-code

# Claude安装验证
claude --version
```
## 智谱接入
```
# 进入命令行界面，执行如下运行 Coding Tool Helper 
npx @z_ai/coding-helper
```
## 模型切换
1. 手动修改配置文件 `~/.claude/settings.json`，添加或替换如下环境变量参数：  
```
{
  "env": {
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "glm-4.5-air",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-4.7",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-4.7"
  }
}
```
***注：使用 GLM-5，需要在将上方的环境变量参数值手动修改模型为 “glm-5”。***

2. 启动一个新的命令行窗口，运行`claude`启动 Claude Code，在 Claude Code 中输入`/status`确认模型状态：