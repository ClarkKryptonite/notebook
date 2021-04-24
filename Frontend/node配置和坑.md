# node配置和坑

### npm install报错：chromedriver哈
先执行
- `npm install chromedriver --chromedriver_cdnurl=http://cdn.npm.taobao.org/dist/chromedriver`

再执行
- `npm install`

### npm死活安装不成功，需要node降级，安装nvm
nvm安装使用
> 查看可以安装的LTS版本
> `nvm ls-remote --lts`
> 安装指定版本的node
> `nvm install <版本号>`
> 查看已安装的node
> `nvm ls`
> 切换 node 版本
> `nvm use <版本号>` 再用`nvm current` 或 `node -v`确认
> 设定默认的 node 版本
> `nvm alias default <版本号>`
> 删除指定版本 node
> `nvm uninstall <版本号>`

### npm设置和取消代理
设置代理
- `npm config set proxy=http://127.0.0.1:8889`
- `npm config set registry=http://registry.npmjs.org`

关于https
- `npm config set https-proxy http://server:port`

取消代理
- `npm config delete proxy`
- `npm config delete https-proxy`

### 使用nrm快速切换npm源
安装
- `sudo npm install -g nrm`

列出可用源
- `nrm ls`

切换
- `nrm use taobao`

增加源
- `nrm add <registry> <url> [home]`

增加源
- `nrm del <registry>`

增加源
- `nrm test`