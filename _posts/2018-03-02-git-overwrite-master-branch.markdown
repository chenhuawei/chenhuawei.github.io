---
layout: post
title: "git overwrite master branch"
date: 2018-03-02 23:37:00 +0800
categories: git
---

+ 切换到旧的分支
    {% highlight shell %}
    git checkout master 
    {% endhighlight %}


+ 将本地的旧分支 master 重置成 develop
    {% highlight shell %}
    git reset --hard develop 
    {% endhighlight %}

+ 再推送到远程仓库
    {% highlight shell %}
    git push origin master --force 
    {% endhighlight %}

当把强制覆盖后的分支推送到远程分支时，可能会提示无权限，这个时候需查看该分支是否处于被保护状态，取消该选项就可以提交
