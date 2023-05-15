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

3. `codesign -dr - /path/to/yourapp.app`
举例如下：
```shell
codesign -dr - /Applications/ClashX.app
Executable=/Applications/ClashX.app/Contents/MacOS/ClashX
designated => anchor apple generic and identifier "com.west2online.ClashX" and (certificate leaf[field.1.2.840.113635.100.6.1.9] /* exists */ or certificate 1[field.1.2.840.113635.100.6.2.6] /* exists */ and certificate leaf[field.1.2.840.113635.100.6.1.13] /* exists */ and certificate leaf[subject.OU] = MEWHFZ92DY)
```

