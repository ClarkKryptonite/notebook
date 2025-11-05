# Android刷机

这里不说sdk环境怎么配置的，一般android开发都早已配置了这玩意儿

## 1. 解锁Bootloader
这一步每个手机都不一样，需要单独搜索。但是在重启前的步骤都是一样的。

**预置条件：**
![rom_develop_setting](img/rom_develop_setting.png) 

**Enable Advanced Reboot:** On your phone go to Settings > Developer options. Then enable the 'Advanced Reboot' option.

**Remove password** 将一切密码移除

对于一加5:
1. 执行`adb reboot bootloader` 重启手机进入bootloader
2. 执行`fastboot devices`确保fastboot驱动已正确安装(执行后会显示对应devices), 如果没有显示一定要修复好才能做下面的步骤。
3. 执行`fastboot oem unlock`，然后右滑解锁
4. 上面步骤执行完成后执行`fastboot reboot`, 进入到Android系统界面, 重新设置开发者选项。

## 2. 安装TWRP
TWRP的官网在此：[https://twrp.me/Devices/](https://twrp.me/Devices/), 找对应的设备下载即可。

ps: 对于一加5来说，twrp-3.7.0_12-0-cheeseburger_dumpling.img 这个版本是有问题的，要下它前面的一个版本。

1. 在android界面执行`adb reboot bootloader`后，执行`fastboot devices` 确保已正常连接。

2. 将文件名改为twrp.img, 并执行`fastboot flash recovery twrp.img`  
![rom_twrp_flash_twrp](img/rom_twrp_flash_twrp.png)

3. 上面执行成功后，执行`fastboot boot twrp.img`   
![rom_twrp_interface](img/rom_twrp_interface.png)

## 3. 下载对应的ROM
去xda网站上找对应的rom进行下载，这里找的是一加5android13的rom，链接为

[https://forum.xda-developers.com/t/rom-13-0-oneplus-5-5t-aosp-for-oneplus-5-4-october-2022.4480549](https://forum.xda-developers.com/t/rom-13-0-oneplus-5-5t-aosp-for-oneplus-5-4-october-2022.4480549) 

1. 在步骤2中的界面先清除数据  
![rom_wipe_data](img/rom_wipe_data.png)
注意一定要执行成功，否则会有之前系统的数据残留可能导致别的问题

2. 进行MTP挂载  
![rom_mount](img/rom_mount.png)

3. 将上面下载的rom改名为rom.zip，执行`adb push rom.zip /sdcard`, 然后安装
![rom_install_rom_1](img/rom_install_rom_1.png)  
找到之前传输的rom.zip点击安装即可

4. 如果需要安装谷歌套件，从下载rom的论坛里中的评论中找，会比直接从网上搜的靠谱些。下载后改名为gapps.zip, 同样执行`adb push gapps.zip /sdcard`, 等步骤3安装完成后再安装就好。

5. 以上步骤完成后再执行如下步骤重启就行了  
![rom_reboot_after_install](img/rom_reboot_after_install.png)

**刷新后千万不要锁OEM！**  
**刷新后千万不要锁OEM！**  
**刷新后千万不要锁OEM！**  

参考链接：  
> [How to Install Android 13 on OnePlus 5/5T](https://www.droidwin.com/how-to-install-android-13-on-oneplus-5-5t/#STEP_4_Download_OnePlus_55T_Android_13_ROM_GApps)  
> [How to Download and Install Android 13 GApps](https://www.droidwin.com/how-to-download-and-install-android-13-gapps/#Full_GApps)  
> [Download and Install AOSP Android 13 on OnePlus 5 and 5T](https://www.getdroidtips.com/android-13-oneplus-5-5t/#Install-TWRP-Recovery)  
> [[ROM][13.0][OnePlus 5/5T] AOSP for OnePlus 5](https://forum.xda-developers.com/t/rom-13-0-oneplus-5-5t-aosp-for-oneplus-5-4-october-2022.4480549/)  
> [TWRP for OnePlus 5/5T](https://twrp.me/oneplus/oneplus5-5t.html)  



# 救砖
如果锁OEM后导致无法进入主界面，需要人工救砖。同样的每个厂家救砖的方式不一样，这里还是记录一加5救砖。

## 准备工作
下载9008驱动工具，MSM解砖工具(只有Windows版本), 一加初始版本的镜像。

## 具体操作
1. 关闭Windows的`turn off Driver Signature Enforcement`, 一般来说按住Shift键重启，在启动设置中关闭即可。
2. 让手机完全关机
3. 知道到Windows的设备管理器
4. 长按'音量+'十秒，连接到电脑, 观察设备管理器是否有设备加入，如果新设备是9008版本则直接进行下一步，否则要装对应的驱动后才能接着往下走。
5. 右键管理员启动MSM Tool, 如果里面有连接设备直接开始即可，否则要重新检查驱动是否安装完成。开始过程中可能碰到`Sahara Communication Fail`错误，此时直接按`音量+`+`开机键`即可。

## 修复些问题
按照上诉的操作后，就能进入最初始的一加5系统界面，但是会有两个问题。
1. 无法刷最新的系统
2. 无法连接网络

这时候就要去论坛里面找离该版本比较近的新系统下载到本地，在设置里进行手动升级。
这里用就`Hydrogen_23_OTA_001_all_Oreo.zip`升级就行，`OnePlus5Hydrogen_23_OTA_037_all_1812102153_android8_1.zip`可能也可以具体没试过。
然后再升官方最新的包。地址是[https://www.oneplus.com/cn/support/softwareupgrade](https://www.oneplus.com/cn/support/softwareupgrade)这样就能愉快玩耍了。

## 修改时间同步服务器
升级后会发现时间无法同步
只需要执行`adb shell "settings put global ntp_server ntp.ntsc.ac.cn"`即可

链接：
> [[GUIDE] Unbrick Tool for OnePlus 5 with OOS 9.0.9 & OOS 9.0.11 (Untouched full stock)](https://forum.xda-developers.com/t/guide-unbrick-tool-for-oneplus-5-with-oos-9-0-9-oos-9-0-11-untouched-full-stock.3761706/)  
> [UnBrick OnePlus 5 Phone With Official Tool [Download]](https://www.technobuzz.net/unbrick-oneplus-5-phone/)  
> [一加5bbs](https://www.oneplusbbs.com/forum-117-1-filter-typeid-typeid-130.html)  
> [[教程] 【推荐】一加手机15种机型线刷救砖资源★附教程](http://www.oneplusbbs.com/thread-4446250-1-1.html)  
> [一加5刷机资源集合](https://jzsst.github.io/2019/oneplus5-brush-resource-collection/)
> [Android修改时间同步服务器](https://zhuanlan.zhihu.com/p/32518769)
