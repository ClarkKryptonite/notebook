# android业务相关，遇到的一些坑

- [android业务相关，遇到的一些坑](#android业务相关遇到的一些坑)
  - [Activity设置透明背景不成功](#activity设置透明背景不成功)
  - [`DialogFragment`中如果有`EditText`，点击后布局会往上顶](#dialogfragment中如果有edittext点击后布局会往上顶)
  - [`TabLayout`的 `indicator`长度设置](#tablayout的-indicator长度设置)
  - [ViewBinding java.lang.NullPointerException](#viewbinding-javalangnullpointerexception)
    - [解决方法](#解决方法)
  - [编译报错`Call requires API level 24 (current min is 23): java.lang.Iterable#forEach`](#编译报错call-requires-api-level-24-current-min-is-23-javalangiterableforeach)

## Activity设置透明背景不成功
1. `AndroidManifest.xml`中的theme不能设置为`@style/Theme.AppCompat.Translucent`，应该为如下设置
```xml
<style name="TranslucentStyle" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@android:color/transparent</item> <!-- 背景色透明 -->
    <item name="android:windowIsTranslucent">true</item> <!-- 是否有透明属性 -->
    <item name="android:backgroundDimEnabled">true</item> <!-- 背景是否半透明 -->
    <item name="android:statusBarColor">@android:color/transparent</item> <!-- 状态栏透明 -->
    <item name="android:windowAnimationStyle">@null</item> <!-- activity窗口切换效果 -->
</style>
```
2. 如果`Activity`是使用代码设置`setContentView`的，需要在**设置**之后调用`setBackgroundResource(android.R.color.transparent)`而不是在**创建**的时候就设置。
3. 要卸载后再安装，对于`AndroidManifest.xml`中的文件，部分手机会做缓存，坑的一匹

## `DialogFragment`中如果有`EditText`，点击后布局会往上顶
1. 需要在`onCreate`调用`setStyle(STYLE_NORMAL, android.R.style.Theme_Black_NoTitleBar_Fullscreen)`。但是直接这么设置是有问题的，这样背景颜色呈白色，而且灰色地带不会退出。
2. 自定义style，设置如下：
```xml
<style name="Dialog.FullScreen" parent="Theme.AppCompat.Dialog">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFullscreen">true</item>
    <item name="android:windowBackground">@android:color/transparent</item>
    <item name="android:windowIsFloating">false</item>
</style>
```
再在`onCreate`调用`setStyle(STYLE_NORMAL, R.style.Dialog_FullScreen)`就能正常显示了

## `TabLayout`的 `indicator`长度设置
```xml
<com.google.android.material.tabs.TabLayout
    android:id="@+id/phrase_select_indicator"
    android:layout_width="match_parent"
    android:layout_height="@dimen/common_108"
    app:layout_constraintTop_toTopOf="parent"
    app:tabGravity="center"
    app:tabIndicator="@drawable/indicator_phrase_select"
    app:tabIndicatorColor="@color/common_3981f4"
    app:tabIndicatorFullWidth="false"
    app:tabIndicatorHeight="@dimen/common_8"
    app:tabRippleColor="@android:color/transparent"
    app:tabSelectedTextColor="@color/common_3981f4"
    app:tabTextColor="@color/common_292a2d" />
```
对应的@drawable/indicator_phrase_select
```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <item
        android:width="@dimen/common_60"
        android:height="@dimen/common_8"
        android:gravity="center"
        tools:ignore="UnusedAttribute">
        <shape>
            <solid android:color="@color/common_3981f4" />
            <size
                android:width="@dimen/common_60"
                android:height="@dimen/common_8" />
            <corners android:radius="@dimen/common_4" />
        </shape>
    </item>
</layer-list>
```
适配android 5.0,对应blog [简书](https://www.jianshu.com/p/e559809b7697)
```kotlin
/**
 * 设置指示符固定宽度
 */
fun TabLayout.setSelectedTabIndicatorFixWidth(width: Float) {
    setSelectedTabIndicator(object : DrawableWrapper(tabSelectedIndicator) {
        override fun setBounds(left: Int, top: Int, right: Int, bottom: Int) {
            var realLeft = left
            var realRight = right
            if ((right - left).toFloat() != width) {
                val center = left + (right - left).toFloat() / 2
                realLeft = (center - width / 2).toInt()
                realRight = (center + width / 2).toInt()
            }
            super.setBounds(realLeft, top, realRight, bottom)
        }
    })
}
```

## ViewBinding java.lang.NullPointerException
```shell
java.lang.NullPointerException
   at android.databinding.tool.store.LayoutFileParser.parseOriginalXml(LayoutFileParser.java:139)

line 1:0 mismatched input '?' expecting {COMMENT, SEA_WS, '<', PI}
```
### 解决方法
**Android studio , 选中对应模块 ，右键 ，remove BOM 即可**
原因：
BOM即byte order mark，具体含义可百度百科或维基百科，UTF-8文件中放置BOM主要是微软的习惯，但是放在别的系统上会出现问题。不含BOM的UTF-8才是标准形式，UTF-8不需要BOM带BOM的UTF-8文件的开头会有U+FEFF，所以我新建的空文件会有3字节的大小。


## 编译报错`Call requires API level 24 (current min is 23): java.lang.Iterable#forEach`
```kotlin
mandatoryViews.forEach { view ->
   // my code here
}
```
代码在库中会报`Call requires API level 24 (current min is 23): java.lang.Iterable#forEach`

This issue is fixed in lint supplied with Android Gradle plugin 4.2, with 4.2.1 being the latest stable release right now.

Update your project-level build.gradle to have

`classpath 'com.android.tools.build:gradle:4.2.1'`