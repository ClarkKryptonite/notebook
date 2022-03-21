# Git一些命令

## git修改远程分支
> 注：修改远程分支先拉下来再进行以下步骤
- 旧分支：oldBranch
- 新分支：newBranch

步骤：
1. 先将本地分支重命名
    `git branch -m oldBranch newBranch`
2. 删除远程分支（远端无此分支则跳过该步骤）
   `git push --delete origin oldBranch`
3. 将重命名后的分支推到远端
   `git push origin newBranch`
4. 把修改后的本地分支与远程分支关联
   `git branch --set-upstream-to origin/newBranch`

## 获取远程服务器的最新改动
使用`git branch -a`看不到同事推送的分支时，应使用`git fetch --all`获取远程服务器的改动，再调用其他命令。
## Tag标签
### 查看
查看本地Tag List
`git tag`
查看远程Tag List
`git ls-remote --tags`
```shell
From git@github0123:jeremy0123/fetch.git
30f4e5cdfef2539b5e156a607f365fb457f309a4        refs/tags/v0.1
0efbfd03ed4b09647ef8a32db9c0a075f7d7dbeb        refs/tags/v0.2
6944954ffa18df994365e53e96d3826a3953890b        refs/tags/v0.2^{}
```
**注意**：refs/tags/v0.2^{}表示v0.2是含附注的标签。
另外，参数--tags可以简化为-t；--heads会获取远程仓库的分支信息。如果没有任何参数，将获取所有的分支和标签信息。
### 创建
在当前HEAD创建标签
`git tag [tag name]`
在指定提交创建标签
`git tag [tag name] [commit refs id]`

> 标签分为两种类型，轻量级(lightweight)标签，如v0.1；含附注(annotated)标签，如v0.2。轻量级标签就像是个不会变化的分支，实际上它就是个指向特定提交对象的引用。而含附注标签，实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用GNU Privacy Guard(GPG)来签署或验证。一般建议使用含附注型的标签，以便保留相关信息；当然，如果只是临时性加注标签，或者不需要旁注额外信息，用轻量级标签也没问题。

### 更新远程标签
仅仅获取标签的更新
`git fetch origin  --tags`

### 将标签推送至远程
提交代码顺便带上标签
`git push origin --tags`
只提交标签
`git push --tags`

### 删除标签
删除本地标签
`git tag -d [tag name]`
删除远程标签
`git push origin --delete tag [tag name]` or
`git push origin :refs/origin/[tag name]`
