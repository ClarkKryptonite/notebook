# adb 定位

1. 查看当前activity
`adb shell dumpsys window w | grep name=`
2. 查看栈顶activity
`adb shell dumpsys activity activities | grep mResumedActivity`
3. 查看栈顶activity的fragment
`adb shell dumpsys activity your.package.name`
搜出结果后可以用'Added Fragments'或'Active Fragments'进行检索
   - 比如说：`adb shell dumpsys activity com.qizhidao.clientapp`  
