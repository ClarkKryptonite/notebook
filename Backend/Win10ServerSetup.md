# Windows(Win10)通过Virtual Box搭建服务器注意事项

### 无法ping通windows地址
1. 打开**网络和Internet设置**,对应网络设置成**专用**(位置：以太网->网卡)
2. 在**Windows Defender 安全中心**的**高级设置**中，左侧**入站规则**,找到**文件和打印机共享(回显请求-ICMPv4-In)**,点击后找到右侧**开启规则**

### Virtual Box网络设置
1. virtual box要使用nat+hostonly(可不加)+桥接的设置，nat为的是虚拟机能正常上网，桥接为了其他机器能ping通该虚拟机
2. vagrant配置Vagrantfile前，先去Virtual Box检查虚拟机网络选择桥接时是否有对应网卡。如果没有需要进行如下操作。
    <div style="text-align: center"><img src="IMG/VirtualBox-Bridge-01.png" /></div><br/>

    > 1. 找到VirtualBox安装目录
    > 2. 定位到目录drivers\network\netlwf
    > 3. 在网络属性中点击安装，选择对应的.inf文件，操作后就有VirtualBox的桥接选项
    >   <div style="text-align: center"><img src="IMG/VirtualBox-Bridge-02.png" /></div>
3. 配置Vagrantfile，将注释的private_network打开，同时配置的ip地址网段要和host-only的网段相同,如host-only的虚拟网卡地址为192.168.53.1,那么此处的ip为192.168.53.[xxx]。同时打开注释的public_network。
4. 接着在cmd调用vargrant reload

### Vagrant 命令
- `vagrant up` 启动
- `vagrant halt` 关机
- `vagrant reload` 重启
- `vagrant ssh` 进入命令终端

### docker下载安装
参考菜鸟教程
最后设置开机自启动docker：`sudo systemctl enable docker`

### docker mysql8
- `docker pull mysql`
- `docker run -p 3306:3306 --name mysql -v /mydata/mysql/log:/var/log/mysql -v /mydata/mysql/data:/var/lib/mysql -v /mydata/mysql/conf:/etc/mysql -v /mydata/mysql/mysql-files:/var/lib/mysql-files/ -e MYSQL_ROOT_PASSWORD=root -d mysql` 启动容器
- 然后在/mydata/mysql/conf/下创建my.cnf配置mysql,输入以下内容
  > ```
  > [client]
  > default-character-set=utf8
  >
  > [mysql]
  > default-character-set=utf8
  >
  > [mysqld]
  > init_connect='set collation_connection=utf8_unicode_ci'
  > init_connect='set names utf8'
  > character-set-server=utf8
  > collation-server=utf7_unicode_ci
  > skip-character-set-client-handshake
  > skip-name-resolve
  > ```
  然后重启mysql，`docker restart mysql`
- `docker exec -it mysql /bin/bash`进入mysql交互界面，`ctrl p + ctrl q`退出交互界面
- 进入mysql后开启远程连接
  > 1. 进入对应数据库
  > 2. 更新域属性，'%'表示允许外部访问：`update user set host='%' where user ='root';`
  > 3. 执行以上语句之后再执行：`FLUSH PRIVILEGES;`
  > 4. 最后执行：`GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;`
- 设置自启动mysql：`docker update mysql --restart=always`

### docker redis
- `docker pull redis`
- `mkdir -p /mydata/redis/conf`和`touch /mydata/redis/conf/redis.conf`创建配置文件，在redis.conf中添加appendonly yes
- `docker run -p 6379:6379 --name redis -v /mydata/redis/data:/data -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf -d redis redis-server /etc/redis/redis.conf`
- `docker exec -it redis redis-cli`进入redis控制台，`ctrl p + ctrl q`或`exit`退出交互界面
- 设置自启动redis：`docker update redis --restart=always`

### Windows10上vagrant root免密码登录
在root用户下
> vim /etc/ssh/sshd_config
打开两个关于秘钥的配置
RSAAuthentication yes
PubkeyAuthentication yes
> 
> 配置公钥
vim ~/.ssh/authorized_keys
将自己的公钥添加到这里。
重启服务
service sshd restart
exit
退出虚拟机

修改vagrant配置数据，注释密码，加上私钥地址
> config.ssh.username = "root"
> #config.ssh.password = "vagrant"
> config.ssh.private_key_path = "/Users/XXX/.ssh/id_rsa"

### Windows10 ssh-agent启动失败
<div style="text-align: center"><img src="IMG/ssh-agent.png" /></div>

### Windows10 选择模式复制有个坑
选择后复制，首字母不会添加！！！

### Vagrant设置共享文件夹
1. 先把虚拟机的linux升级，具体有yum和kernel
`yum update`
`yum install kernel-devel`
2. 再退出linux，安装vagrant-vbguest插件
`vagrant plugin install vagrant-vbguest`
3. 配置vagrantfile,增加两行配置
`config.vm.synced_folder ".", "/vagrant", disable: true`
`config.vm.synced_folder "F:\\VagrantShare", "/vagrant_data", owner: "root", group: "root"`