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