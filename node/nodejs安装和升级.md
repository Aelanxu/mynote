## node的安装和升级操作事项
1. 查看当前node的版本号
```shell
node --version
```
2. 清理npm的cache
```shell
npm cache clean -f
```
3. 安装版本管理工具 
```shell
npm install -g n
```
更新到最新稳定版

```shell
n stable
```
n 常用的命令有：
n 会列出所有安装的版本供你切换
n latest 安装最新版本
n stable 安装最新稳定版
n lts 安装最新长期支持版本
n rm [版本号] 删除某一版本
n -h 帮助命令
n [版本号] 安装指定版本node