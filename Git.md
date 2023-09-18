#### 基础指令

```shell
# 配置用户信息
git config --global user.name= "jdy2002"
git config --global user.email="2475058223@qq.com"
# 获取全局配置项
git config --list --global
# 查看本地帮助手册
git help config
# 查看帮助
git config -h
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
# 简短方式显示状态
git stauts -s
# 取消修改
git restore file
# 撤销文件修改
git checkout -- file
# 从暂存区里移除
git reset HEAD file(.)
# 版本回退
git reset --hard commitId
# 跳过暂存区直接把已跟踪文件提交到git仓库
git commit -a -m ""
# 移除工作区文件和仓库中的文件
git rm -f file
# 移除仓库中的文件
git rm --cached file
# 查看最近两条日志
git log -2
# 查看所有日志
git reflog --pretty=oneline
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
# ssh-keygen -t rsa # -b 4096 -C "你的邮箱"
ssh-keygen -t rsa -C "xxxx@xx.com"
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
# 查看远程仓库中的所有分支列表
git remote show origin
# 跟踪分支，从远程仓库中，把远程分支下载到本地仓库中
git checkout 远程仓库名称
# 从远程仓库中，把对应的远程分支下载到本地仓库，并把下载的本地分支进行重命名
git checkout -b 本地分支名称 远程仓库名称/远程分支名称
git checkout -b reg origin/register
# 从远程分支拉取代码
git pull
# 删除远程分支
git push origin --delete register

```

 

```bash
# 本地仓库与远程仓库进行关联
git remote add origin https://github.com/Yu2002s/project01.git`
# 重命名分支
git branch -M main
# 第一次推送分支到远程仓库
# git push -u 远程仓库别名 本地分支名称:远程仓库名称
git push -u origin main
```

```bash
# 解决无法连接到github问题
git config --global http.sslVerify "false"
```

### 检查ssh密钥

进入当前用户的**.ssh**文件夹，查看是否存在**id_rsa.pub**文件，文件内容就是密钥

```bash
# 检查ssh密钥
cd ~/.ssh
# 查看密钥文件
cat id_rsa.pub
```

### 生成ssh密钥

```bash
# 生成ssh密钥
ssh-keygen -t rsa -C "xxxx@xx.com"
```

执行后一直回车即可

### 添加密钥到gitee

### 验证密钥

```bash
ssh -T git@gitee.com
```

### 初始化仓库

```bash
# 初始化仓库
git init
# 添加到暂存区
git add .
# 提交
git commit -m "描述"
```

### 推送代码到远程仓库

```bash
# 与远程仓库关联
git remote add origin 远程地址
# 开始推送
git push -u origin main
# 如果本地分支名和远程分支名相同，则可直接push
```

### 克隆项目到本地

```bash
git clone 远程地址
```

### 分支操作

```bash
# 查看所有分支
git branch
# 切换并创建分支
git checkout -b xxx
# 删除本地分支
git branch -d xxx
```

