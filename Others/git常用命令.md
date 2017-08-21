## 本地的常用命令
* 初始化： ` git init`
* 添加文件到暂存区（`stage`）：` git add <filename> <filename> ...`
* 把暂存区的内容提交到当前分支： `git commit -m"message about this commit"`
* 查看当前状态： `git status`
* 查看对文件的改动：` git diff  <filename>`
* 查看提交历史：`git log`（按时间从近到远显示）（参数 `--pretty=oneline`，可以一行显示提交的信息）
* 回退到上一个版本：  `git reset --hard HEAD^`
* 查看命令历史： `git reflog` (回退错了，可以回到未来的版本)
* 回退到指定 `commit id` ： `git reset --hard commit_id`
* 撤销文件在工作区的修改： `git checkout --<filename>`
* 撤销文件在暂存区的修改： `git reset HEAD <filename>`
* 从版本库中删除文件： `git rm <filename>`
* 从版本库中恢复误删除的文件： `git checkout --<filename>`
* 保存当前工作现场： `git stash`
* 查看 `stash` : `git stash list`
* 恢复工作现场： `git stash apply,   git stash drop   或者 git stash pop`
* 恢复到指定的 `stash`： `git stash apply stash@{<stash_id>}`

## 远程库的常用命令
* 远程关联，远程库的名字是`origin`： `git remote add origin https://github.com/zhangsan/git_demo.git`
* 推送： `git push origin master` (第一次推送要用 `-u` 参数，可关联本地master分支和远程`master`分支)
* 克隆远程库： `git clone https://github.com/zhangsan/requests.git`
* 查看远程库的信息： `gjt remote -v`

## 分支
* 查看当前分支 : `git branch`
* 创建分支 `dev` ： `git branch dev`
* 切换到分支 `dev` ： `git checkout dev`
* 创建并切换到分支 `dev` ： `git checkout -b dev`
* 删除分支 `dev` :  `git branch -d dev`
* 合并指定分支到当前分支 （使用普通模式合并）： `git merge --no-ff dev`
* 创建远程 `origin` 的 `dev` 分支到本地： `git checkout -b dev origin/dev`

## 标签
* 查看标签： `git tag`
* 打标签： `git tag <tagname> -m"message about tag"` （默认打在最新提交的commit上）
* 打标签到指定 `commit id` 上： `git tag <tagname> <commit_id>`
* 查看标签信息： `git show <tagname>`
* 删除标签： `git tag -d <tagname>`
* 推送标签到远程： `git push origin <tagname>`
* 一次推送多个标签到远程： `git push origin --tags`
* 删除远程标签： `git tag -d <tagname>`