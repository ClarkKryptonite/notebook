# 一些gradle的设置

- [一些gradle的设置](#一些gradle的设置)
    - [删除本地gradle库后，对应idea库并没有更新](#删除本地gradle库后对应idea库并没有更新)
  - [打包的时候添加源码](#打包的时候添加源码)
  - [查看依赖关系](#查看依赖关系)
    - [查看某个模块下所有库的依赖关系](#查看某个模块下所有库的依赖关系)
    - [查看某个模块下，某个库的依赖关系](#查看某个模块下某个库的依赖关系)


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

## 查看依赖关系
### 查看某个模块下所有库的依赖关系
如果直接使用 `./gradlew :[模块名]:denpendencies`，会显示所有task中的所有依赖。
我们这里只需要看具体某个task依赖即可。如：
> `./gradlew :[模块名]:dependencies --configuration debugCompileClasspath` 
> 或
> `./gradlew :[模块名]:dependencies --configuration debugRuntimeClasspath` 
> 或
> `./gradlew :[模块名]:dependencies --configuration implementationDependenciesMetadata` 

### 查看某个模块下，某个库的依赖关系
拿`androidx.exifinterface:exifinterface:1.2.0`举例，两个`:`中间的就是对应的模块名
> `./gradlew :lib_qim:qim:dependencyInsight --dependency exifinterface --configuration debugCompileClasspath` 
> 或
> `./gradlew :lib_qim:qim:dependencyInsight --dependency exifinterface --configuration debugRuntimeClasspath` 
> 或
> `./gradlew :lib_qim:qim:dependencyInsight --dependency exifinterface --configuration implementationDependenciesMetadata`