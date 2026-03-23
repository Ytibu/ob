## 初始化
```
# 初始化本地全局信息
	git config --global user.name"dingjr"
	git config --global user.email"dar06@foxmail.com"
# 设置全局的初始化分支为main
	git config --global branch main
# 初始化一个本地git仓库
	git init 
```

## ssh连接
```
# 生成密钥
	ssh-keygen -t rsa -C "邮箱名"
	ssh-keygen
# 持续三次回车
	ENTER
# 查看本地.ssh/
	ls ~/.ssh/
# 查看公钥内容
	cat  ~/.ssh/id_ed25519.pub
```
## 工作树
```
# 查看本地仓库情况
	git status
# 追踪指定的本地文件
	git add . 
# 取消追踪指定的本地文件
	git restore --staged <file>
# 提交修改
	git commit -m <description>
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
```

## 代码合并
```
# 切换到主分支
	git checkout main
# 合并子分支
	git merge fix_bug
```