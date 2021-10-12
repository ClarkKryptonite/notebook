# android业务相关，遇到的一些坑

- [android业务相关，遇到的一些坑](#android业务相关遇到的一些坑)
  - [Activity设置透明背景不成功](#activity设置透明背景不成功)
  - [`DialogFragment`中如果有`EditText`，点击后布局会往上顶](#dialogfragment中如果有edittext点击后布局会往上顶)
  - [`TabLayout`的 `indicator`长度设置](#tablayout的-indicator长度设置)

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
2. 如果`Activity`是使用代码设置`setContentView`的，需要在**设置**之后调用`setBackgroundResource(android.R.color.transparent)`而不是在**创建**的时候就设置

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
