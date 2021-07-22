# Mac系统命令相关

- [Mac系统命令相关](#mac系统命令相关)
  - [macOS获取应用BundleID](#macos获取应用bundleid)

## macOS获取应用BundleID
1. `osascript -e 'id of app "SomeApp"'`
举例如下：
```shell
osascript -e 'id of app "iterm"'
com.googlecode.iterm2
```
2. `mdls -name kMDItemCFBundleIdentifier -r SomeApp.app`
举例如下：
```shell
mdls -name kMDItemCFBundleIdentifier -r /Applications/Visual\ Studio\ Code.app
com.microsoft.VSCode
```
