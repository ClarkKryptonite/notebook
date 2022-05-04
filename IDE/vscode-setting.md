# vscode设置和坑

## 代码模板
vscode代码模板有多种分类，如语言模板和全局语言模板，在创建的时候一定要选择清楚。
- 添加全局代码片段的时候，文件扩展名为 .code-snippets，例如 blog.code-snippets；
- 添加语言特定的代码片段的时候，扩展名为 .json，如 markdown.json

## 修改全局默认模板
举个例子，如果需要修改markdown文件的默认模板
1. 进入到`/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/markdown-basics/snippets`中，修改对应`markdown.code-snippets`的模板。
2. 重启VSCode!!!!!
