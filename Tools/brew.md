# HomeBrew使用小技巧

## 利用brew来卸载软件
即使你的软件不是用 homebrew 安装的，你也可以用它来卸载（加 --force ），还可以加上 --zap 让 homebrew 把所有关联的数据文件都删除（可以用 `brew cat` 查看具体包括哪些文件），如 `brew uninstall --cask --force --zap zoom`
