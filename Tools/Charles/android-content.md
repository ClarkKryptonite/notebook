# android抓包使用指南

## 电脑端设置
1. 破解版地址 [https://macwk.com/soft/charles](https://macwk.com/soft/charles)
2. 启动Charles，选择Help-->SSL Proxying→Install Charles Root Certificate
3. 此时会启动KeyChain Access,找到刚安装好的证书，证书名字：Charles Proxy CA
4. 选中该证书，点击显示简介，修改信任权限为始终信任。并将该证书移动到左侧系统中
5. 设置HTTPS端口抓包，点击Charles-->Proxy-->SSL Proxying Settings，点击Enable SSL Proxying，添加“*:443”，保存

## 手机端设置
查看电脑IP地址，点击Charles-->Help-->Local IP Address,让手机和电脑在同一局域网内
### iPhone证书安装
1. 设置-->WIFI--》点击Wifi旁边的配置
2. 滑到底部，点击配置代理
3. 配置代理，IP就是你电脑的IP，端口输入8888，点击存储
4. 然后打开Safari浏览器，访问：chls.pro/ssl，此时电脑上连接提示，点击allow允许。
5. 此时手机会提示下载描述文件，点击允许
6. 保存后，点击 设置-->通用→描述文件，安装刚下载好的文件
7. 安装完成后，点击 设置-->通用→关于本机，下拉到底部，点击证书 信任设置，把刚信任开关打开。

### android证书安装
1. 设置Wi-Fi代理。设置→WI-FI→代理设置，IP就是你电脑的IP，端口输入8888，保存
2. 使用Chrome浏览器访问"chls.pro/ssl"（注意：一定要使用Chrome浏览器，安卓国产浏览器会将证书视为下载文件，不能直接安装），此时电脑上连接提示，点击allow允许。可以用电脑下载adb push进去
3. 下载证书并安装(需要提前设置好手机锁屏密码)，见下图，输入名称，点击确认就可以了

## 备注
有的手机app是不允许抓包的，具体实现原理大概两种：
1. app框架可以检测代理，如果你是用代理端口访问，则拒绝连接。
2. 手机内置HTTPS证书做了双端校验。具体原理感兴趣可以Google下。