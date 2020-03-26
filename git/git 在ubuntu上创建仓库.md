# ubuntu的git 仓库搭建

1、首先是安装

```shell
git sudo apt-get install git
```

2、创建用户git

#生成公钥私钥的时候要切换到git用户否则会出现权限问题

```shell
adduser git
```

#在这个过程中会要求输入密码。 然后给git用户添加sudo权限，这部分涉及到后续切换到git用户下进行操作，部分操作需要sudo权限。当然权限熟的话，通过命令行进行设置可以跳过此步。

```shell
sudo  usermod -aG sudo git
```

3、切换到git用户

```shell
su - git
```

4、创建存储目录

```shell
mkdir code
```

5、初始化裸仓库

```shell
git init --bare test.git
```

#教程上都喜欢使用tree来查看文档目录，想看到话先装个tree

```shell
sudo apt-get install tree
```

```shell
tree
```

就可以看到文档目录了。

 6、然后就是创建公钥和私钥了，目的是允许用户不需要密码访问git服务器

#使用shh-keygen生成公钥和私钥，也可以使用其他工具

```shell
ssh-keygen -t rsa -C "youremail@example.com"
```

#会在/home/git/.ssh/路径下生成id_rsa和id_rsa.pub两个文件，其中id_rsa为私钥， 将其存储在本地机器windows系统/用#户/.ssh目录下；将id_rsa.pub内容添加到 /home/git/.ssh/authorized_keys中

```shell
cat id_rsa.pub >> .ssh/authorized_keys 
```

#修改ssh的配置文件/etc/ssh/sshd_config，去掉这一行的注释AuthorizedKeysFile  .ssh/authorized_keys....

#重启ssh

```shell
service ssh restart
```

#在整个过程中一定要注意，操作的都是git用户的根目录，即/home/git/这个路径下。

7、然后就是git服务器的安全性的一些设置，参考文档中这部分有问题，完全按照参考文档，是无法实现无密码访问的，主要原因就是ubuntu用户权限的问题。

#注意：按照我的操作，切换到git用户下是不需要这部分操作的。

#修改目录权限

```shell
chmod 755 /home/git
chmod 700 .ssh
chmod 600 .ssh/authorized_keys
```

8、在密钥生成之后可以把git的bash禁掉，禁用bash是因为git作为公用用户，无需让其登录shell 操作，仅限于使用git,具体操作，/etc/passwd

#将

```shell 
git:x:1001:1001:,,,:/home/git:/bin/bash
```

#修改为

```shell
 git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```

#最后回到本地机器，在目标目录右键选择git clone，然后在地址栏里输入

```shell 
ssh://git@192.168.7.73/home/git/code/test.git
```

#然后就可以正常使用了。

9、局域网映射

#然后得增强功能就是设置局域网内git服务器的远程访问的问题，首选是在路由器上做端口映射，这部分就跳过。 然后是设置git的监听端口号，我这里用的是60000-63000

#采用ssh连接所以我直接修改ssh的配置文件

#/etc/ssh/sshd_config 找到#Port 22 在后面添加Port 60001（此方法是否有效还需要进行验证，后续进行修改）