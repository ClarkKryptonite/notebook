# 一些gradle的设置

- [一些gradle的设置](#一些gradle的设置)
    - [删除本地gradle库后，对应idea库并没有更新](#删除本地gradle库后对应idea库并没有更新)
  - [打包的时候添加源码](#打包的时候添加源码)


### 删除本地gradle库后，对应idea库并没有更新
1. 打开工程目录下的.idea/libraries目录
2. 搜索对应的库关键字，注意库以`_`连接
3. 删除对应`.gradle/caches/modules-2/files-2.1/[module]`以及`.gradle/caches/transforms-2/files-2.1`对应文件即可

## 打包的时候添加源码
1. 生成对应`sources.jar`
```groovy
task androidSourcesJar(type: Jar) {
    classifier = 'sources'
    from android.sourceSets.main.java.sourceFiles
}
```

2. 在对应模块的`build.gradle`下添加如下代码
```groovy
android {
    // ...
    sourceSets {
        // ...
        main.java.include '**/*.java', '**/*.kt'
    }
}
```
对应的gradle android plugin文档在 [https://developer.android.com/reference/tools/gradle-api/4.2/classes#letter_S](https://developer.android.com/reference/tools/gradle-api/4.2/classes#letter_S)