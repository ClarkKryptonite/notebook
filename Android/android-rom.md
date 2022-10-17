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
