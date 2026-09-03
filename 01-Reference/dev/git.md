## 初始化
```bash
# 初始化本地全局信息
git config --global user.name "dingjr"
git config --global user.email "dar06@foxmail.com"
# 设置全局的初始化分支为main
git config --global init.defaultBranch main
# 查看全局配置
git config --global --list
# 初始化一个本地git仓库
# git init 
```

## 代理设置
```bash
# 查看全局级别的 HTTP/HTTPS 代理：
git config --global --get http.proxy
git config --global --get https.proxy

# 设置全局级别的 HTTP/HTTPS 代理：
git config --global http.proxy http://127.0.0.1:7892
git config --global https.proxy http://127.0.0.1:7892

# 取消 HTTP/HTTPS 代理：
git config --global --unset http.proxy
git config --global --unset https.proxy
```
## ssh连接
```bash
# 生成密钥
	ssh-keygen -t rsa -b 4096 -C "邮箱名"
	ssh-keygen -t rsa -b 4096
# 持续三次回车
	ENTER
# 查看本地.ssh/
	ls ~/.ssh/
# 查看公钥内容
	cat  ~/.ssh/id_rsa.pub
```
## 工作树
```bash
# 查看本地仓库情况
	git status
# 追踪指定的本地文件
	git add . 
# 取消追踪指定的本地文件
	git restore --staged <file>
# 提交修改
	git commit -m <description>
```

## 分支操作
```
# 查看所有分支
git branch -a
# 创建新分支
git branch <branch_name>
# 重命名分支
git branch -m <old_name> <new_name>
# 删除本地分支
git branch -d <branch_name>
# 强制删除本地分支
git branch -D <branch_name>
# 查看远程分支
git branch -r
# 跟踪远程分支
git checkout -b <local_branch> origin/<remote_branch>
```

## 远程仓库
```
# 添加远程仓库
git remote add <name> <url>
# 查看远程仓库
git remote -v
# 删除远程仓库
git remote remove <name>
# 修改远程仓库地址
git remote set-url <name> <new_url>
# 拉取远程分支并合并
git pull <remote> <branch>
# 推送本地分支到远程
git push <remote> <branch>
# 推送所有分支
git push --all <remote>
# 删除远程分支
git push <remote> --delete <branch>
```

## 分支切换
```
# 创建分支
	git branch fix_bug
# 切换分支
	git checkout fix_bu
# 工作...
	git add .
	git commit -m "fix_bug info"
	git log --onelline --all --graph
# 回到主分支
	git checkout main
# 删除弃用分支
	git branch -d fix_bug
# 查看分支历史
	git log --graph --pretty=oneline --abbrev-commit
```

## 信息查看
```
# 推荐的git日志查看命令
git log --oneline --all --graph
# 查看某文件的提交历史
git log <file>
# 查看某次提交的详细内容
git show <commit_hash>
# 查看提交历史及变更内容
git log -p
# 查看简要状态
git status -s
```

## 代码合并
```
# 切换到主分支
git checkout main
# 合并子分支
git merge fix_bug
# 解决冲突后，标记为已解决并提交
git add <conflicted_files>
git commit -m "fix conflict"
# 放弃合并
git merge --abort
```


## 回到过去节点
```
# 回到某个提交节点（游离 HEAD）
git switch --detach <commit_hash>
# 回到正常分支
git switch <branch_name>
# 回退到上一个提交（保留修改）
git reset --soft HEAD~1
# 回退到上一个提交（丢弃修改）
git reset --hard HEAD~1
# 撤销某次提交但保留修改
git reset --mixed <commit_hash>
```

## 远程仓库添加忽略文件(已经存在文件)
```
## 将远程仓库内容添加到忽略中
# 1:移除单个文件
git rm --cached <file>
# 2: 提交忽略
git add .gitignore
# 3: 提交并推送
git commit -m "..." && git push
```

## 分支合作流程
echo "新内容" >> file.txt
git add file.txt
git commit -m "添加新功能"
git pull origin main
git checkout main
git merge new-feature
git commit -m "合并"
git push 
```
# 完整流程: 创建新分支、开发、合并、删除分支
git remote add <自定义仓库名> <仓库URL>
git checkout -b new-feature
# ...开发、修改文件...
git add .
git commit -m "添加新功能"
# 拉取主分支最新代码并合并（解决冲突）
git pull origin main
# 回到主分支
git checkout main
git merge new-feature
git commit -m "合并"
git push
# 删除本地分支
git branch -d new-feature
# 删除远程分支
git push origin --delete new-feature
```

## 标签管理（Tag）
```
# 创建标签
git tag <tagname>
# 创建带注释标签
git tag -a <tagname> -m "说明"
# 查看所有标签
git tag
# 推送标签到远程
git push origin <tagname>
# 删除本地标签
git tag -d <tagname>
# 删除远程标签
git push origin :refs/tags/<tagname>
```

## stash 临时保存
```
# 保存当前工作进度
git stash
# 查看所有 stash
git stash list
# 恢复最近一次 stash
git stash pop
# 恢复指定 stash
git stash apply stash@{n}
# 删除指定 stash
git stash drop stash@{n}
# 清空所有 stash
git stash clear
```

## 常见问题排查
```
# 取消本地所有未提交的更改（危险）
git reset --hard
# 强制拉取远程分支覆盖本地
git fetch --all
git reset --hard origin/<branch>
# 忽略文件未生效
git rm -r --cached .
git add .gitignore
git commit -m "fix .gitignore"
# 解决合并冲突
# 1. 手动编辑冲突文件，删除冲突标记
# 2. git add <file>
# 3. git commit -m "fix conflict"
```

## 常用配置
```
# 设置默认推送方式为 simple
git config --global push.default simple
# 设置自动补全
git config --global core.autocrlf input
# 设置颜色显示
git config --global color.ui auto
# 设置默认编辑器
git config --global core.editor "code --wait"
```