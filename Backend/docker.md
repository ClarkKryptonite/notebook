### Primer
首先要运行docker app
>  ```docker ps```
>  查看正在运行的容器

> ```docker ps -a```
  查看所有容器，包括已经结束的容器

> ```docker run -it ubuntu```
  -i表示要与容器互动，-t表示创建一个终端，docker会先检查有没有ubuntu镜像，没有则会从Docker Hub下载，Docker Hub是Docker官方的一个镜像仓库

> ```docker start [Container ID]```
  以Detached模式运行容器，[Container ID]为ps中的参数

> ```docker attach [Container ID]```
  进入容器

> 在容器操作界面中按**CTRL+P**以及**CTRL+Q**这两个组合键，可以以保持容器运行的方式退出

> ```docker stop [Container ID]```
  停止容器

> ```docker rm [Container ID]```
  移除容器

> ```docker image ls```
  查看下载到本地的镜像

> ```docker rmi [REPOSITORY]```
> 移除本地镜像，其中[REPOSITORY]是docker image ls的参数

#### 栗子:建立一个网页服务器
启动一个nginx网页服务器
> ```docker run --name web-server -d -p 8000:80 nginx```
> --name web-server ———— 将容器名改为web-server
> -d ———— 代表运行成Detached模式
> -p 8000:80 ———— 将本机8000端口映射到容器的80端口
>
> 打开浏览器输入localhost:8000
> 如果出现Nginx，代表网页服务器正常运行
>
> ```docker stop [Container ID]```
> 先停止网页服务器，准备前端文件，跳转到前端文件的目录下
> 
> ```docker run --name web-iphone12 -d -p 8000:80 -v $(pwd):/usr/share/nginx/html nginx```
> -v $(pwd):/usr/share/nginx/html ———— 将电脑档案映射到容器路径，/usr/share/nginx/html可以看DockerHub上的说明  


### 建立自己的Docer镜像(还是看CodingStartup的自建Docker镜像吧)
> ```docker diff [Container ID]```
> 查看和之前的容器修改了什么内容

> ```docker commit [Container ID] [name]```
> 提交新的image，其中[name]有个规范，一般是(自己名/镜像名:版本号)，如codingstartup/node_app:0.1