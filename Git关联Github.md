# 关联Github
与Github关联，有两种方式，SSH方式与HTTP方式。SSH使用证书进行管理，配置好后，每次提交无需再输入用户名密码，本文主要讲解SSH方式的配置。


## 生成并关联本地的SSH密钥
1. 查看是否已有SSH key。执行命令：```$ ls -al ~/.ssh```，如有，则备份删除。如无，则继续执行。
2. 生成密钥。执行命令： ```$ ssh-keygen -t rsa -C "your_email@your_email.com"``` ，注意替换。
3. 会提示输入保存文件的名字，以及密码。文件名字可不选直接回车，系统会默认生成名为id_rsa的相关文件。
4. 将生成的密钥添加到ssh-agent，使用如下两个命令：
    ```$ eval "$(ssh-agent -s)"```
    ```$ ssh-add ~/.ssh/id_rsa```
5. 执行命令： ```$ ls -al ~/.ssh``` ，可以看到有**id_rsa**和**id_rsa.pub**两个文件。
6. 打开id_rsa.pub文件，并复制下全部内容（注意不要多复制空格）。也可以使用这个命令进行复制： ```$ pbcopy < ~/.ssh/id_rsa.pub```
7. 登录Github网站，登录后，进入**Setting—SSH keys—Add SSH Key**，将复制的内容添加至此。即可完成SSH key的添加。
8. 测试一下。输入命令： ```$ ssh -T git@github.com``` ，如果出现如下提示，则说明成功了。**Hi username! You’ve successfully authenticated, but GitHub does not provide shell access.**

## 提交代码至Github
1. 添加你的Git地址。在终端中执行如下命令，注意替换your-github-id与your-repository-id
    ```$ git remote add origin git@github.com:your-github-id/your-repository-id.git```
2. 上面的origin是为远程的Github repository在本地定义的用于关联的别名。添加完成后，可以在Xcode的**Source Control—your project name—Configure your project name—Remotes**看到已经添加的Remote repository。
3. 上一步提交时如果失败，提示冲突，则先将远程的内容先pull下来后再重新push。很多教程没有提到这一点，我就是在这里耽误了很久。
   ```$ git pull origin master```
   ```$ git push origin master```
   
这样，我们就完成了至Github的提交。进入网站中查看自己的repository，应该就可以看到自己的源代码了。