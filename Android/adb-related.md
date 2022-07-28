# adb 相关

- [adb 相关](#adb-相关)
  - [adb获取栈顶信息](#adb获取栈顶信息)
  - [adb查看UI性能](#adb查看ui性能)
  - [adb查询错误报告(包括anr之类)](#adb查询错误报告包括anr之类)
  - [查看安装包的路径](#查看安装包的路径)
  - [清除安装包数据与缓存](#清除安装包数据与缓存)
  - [adb修改sdcard内容后，对应文件管理器没有立刻更新](#adb修改sdcard内容后对应文件管理器没有立刻更新)
  - [读取剪切板内容](#读取剪切板内容)
  - [adb pull 多个文件](#adb-pull-多个文件)
  - [adb 无线调试](#adb-无线调试)

## adb获取栈顶信息
1. 查看当前activity
`adb shell dumpsys window w | grep name=`
2. 查看栈顶activity
`adb shell dumpsys activity activities | grep mResumedActivity`
3. 查看栈顶activity的fragment
`adb shell dumpsys activity your.package.name`
搜出结果后可以用'Added Fragments'或'Active Fragments'进行检索
   - 比如说：`adb shell dumpsys activity com.qizhidao.clientapp`  

## adb查看UI性能
1. 在开发者模式中打开GPU调试
2. 获取数据
   `adb shell dumpsys gfxinfo [package_name]`
3. 获取结果如下
   ```shell
   Applications Graphics Acceleration Info:
   Uptime: 102809662 Realtime: 196891968

   ** Graphics info for pid 31148 [com.android.settings] **

   Stats since: 102794621664587ns
   Total frames rendered: 105
   Janky frames: 2 (1.90%)
   50th percentile: 5ms
   90th percentile: 7ms
   95th percentile: 9ms
   99th percentile: 19ms
   Number Missed Vsync: 0
   Number High input latency: 0
   Number Slow UI thread: 2
   Number Slow bitmap uploads: 0
   Number Slow issue draw commands: 1
   HISTOGRAM: 5ms=78 6ms=16 7ms=4 8ms=1 9ms=2 10ms=0 11ms=0 12ms=0 13ms=2 14ms=0 15ms=0 16ms=0 17ms=0 18ms=0 19ms=1 20ms=0 21ms=0 22ms=0 23ms=0 24ms=0 25ms=0 26ms=0 27ms=0 
   ```
   
- Graphics info for pid 31148 [com.android.settings]: 表明当前dump的为设置界面的帧信息，pid为31148
- Total frames rendered: 105 本次dump搜集了105帧的信息
- Janky frames: 2 (1.90%) 105帧中有2帧的耗时超过了16ms，卡顿概率为1.9%
- Number Missed Vsync: 0 垂直同步失败的帧
- Number High input latency: 0 处理input时间超时的帧数
- Number Slow UI thread: 2 因UI线程上的工作导致超时的帧数
- Number Slow bitmap uploads: 0 因bitmap的加载耗时的帧数
- Number Slow issue draw commands: 1 因绘制导致耗时的帧数
- HISTOGRAM: 5ms=78 6ms=16 7ms=4 ... 直方图数据，表面耗时为0-5ms的帧数为78，耗时为5-6ms的帧数为16，同理类推。
 总结：这些高级统计数据在很高的层次上向我们传达了app的渲染性能，以及它在许多帧中的稳定性。

4. 结果分析/Profile data in ms
- Draw: 表示在Java中创建显示列表部分中，OnDraw()方法占用的时间
- Prepare: 准备时间
- Process： 表示渲染引擎执行显示列表所花的时间，view越多，时间就越长
- Execute： 表示把一帧数据发送到屏幕上排版显示实际花费的时间，其实是实际显示帧数据的后台缓存区与前台缓冲区交换后并将前台缓冲区的内容显示到屏幕上的时间
- 将上面的四个时间加起来就是绘制一帧所需要的时间，如果超过了16.67就表示掉帧了

> **说明:**
> - Android定义了流畅度的数据标准,以60FPS为标准(FPS为每秒绘制的帧数),帧数过小就会出现卡顿感
> - 每一帧在安卓系统中分4个阶段,4个阶段的总和超过16.67(1秒60帧,算下来平均1帧的间隔就约是16.67ms)就认为丢帧
> - 这个定义在Android6.0以前是一定的,但是现在已经没有固定的标准了,因为目前安卓系统有3层缓存机制,加上硬件上的进步,即使超过16.67,也不一定会出现卡顿感。所以这个数据在测试时作为一种对比和相对衡量标准，也可根据需求自定义标准。

## adb查询错误报告(包括anr之类)
使用 `adb bugreport` 获取错误报告，默认会保存在本地。
默认情况下，ZIP文件名称为 `bugreport-BUILD_ID-DATE.zip`, 其中最重要的是 `bugreport-BUILD_ID-DATE.txt`。
使用 `adb shell dumpsys batterystats --reset`重置错误报告。

## 查看安装包的路径
`adb shell pm path <PackageName>`

## 清除安装包数据与缓存
`adb shell pm clear <PackageName>`

## adb修改sdcard内容后，对应文件管理器没有立刻更新
操作完后需要执行如下命令 `adb shell am broadcast -a android.intent.action.MEDIA_SCANNER_SCAN_FILE -d file:///sdcard/Movies`

## 读取剪切板内容
1. 需要安装软件
[https://github.com/majido/clipper](https://github.com/majido/clipper)
2. 启动软件，获取对应权限
3. 设置shell代码,添加到对应的sh配置文件中
```shell
adb shell am start ca.zgrs.clipper/.Main
sleep 0.5
if [[ -z $1 ]]; then
    adb shell am broadcast -a clipper.get
else
    adb shell am broadcast -a clipper.set -e text "\"$1\""
fi
adb shell am force-stop ca.zgrs.clipper
```

## adb pull 多个文件
比如我要获取某天的截图，就可以使用如下命令
`adb shell 'ls /sdcard/Pictures/Screenshots/Screenshot_20220526_110*.jpg' |tr -d '\r' | sed -e 's/^\///' | xargs -n1 adb pull`

## adb 无线调试
如果直接调用`adb connect [ip]`失败的话。
需要先用数据线连接手机进入调试模式，再在终端输入`adb tcpip 5555`，再调用`adb connect [ip]`