git checkout master // 切换到旧的分支

git reset --hard develop // 将本地的旧分支 master 重置成 develop
git push origin master --force // 再推送到远程仓库

当把强制覆盖后的分支推送到远程分支时，可能会提示无权限，这个时候需查看该分支是否处于被保护状态，取消该选项就可以提交
