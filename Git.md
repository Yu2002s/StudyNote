#### 基础指令

```shell
# 初始化 git
git init
# 从工作区添加到暂存区
git add .
# 从暂存区添加到本地仓库
git commit -m "描述信息"
# 查看日志
git log # --pretty=online --all --graph
# 查看状态
git status
# 版本回退
git reset --hard commitId
# 查看所日志
git reflog 
# 查看所有分支
git branch
# 创建分支
git branch a
# 切换分支
git checkout master
# 创建并切换分支
git checkout -b a
# 合并分支
git merge a
# 删除分支
git branch -d a
# 强制删除分支
git branch -D a
# 生成公钥
ssh-keygen -t rsa
# 查看公钥
cat ~/.ssh/id_rsa.pub
# 验证公钥
ssh -T git@gitee.com
# 添加到远程仓库
git remote add origin xxxxx.git
# 查看远程仓库
git remote
# 推送到远程仓库
git push origin master:master
# 查看本地分支和远程分支的关系
git branch -vv
# 推送到远程仓库并设置分支关联
git push --set-upstream origin master:master
# 直接推送
git push
```

