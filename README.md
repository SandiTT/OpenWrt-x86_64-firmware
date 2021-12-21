# 云编译openwrt
## _自用备份操作手令_

It's the guide of config for openwrt.

- 
- 
- 

## 下载云编译文件
   -  download directly from release.
   -  为了解决国内被墙的问题，使用自动化脚本转换下载链接地址：https://github.com/SandiTT/Download-Git-Files-By-Wetransfer

## Windows下安装ubuntu

1. control panel ->turn windows features on or off ->(bottom of list) windows subsystem for linux->select->reboot system
2. microsoft store->find ubuntu->select one of them->install->launch

| 方法 | 命令 |
| ------ | ------ |
| 查看版本 | cat /etc/issue |
| 更新软件 |sudo apt-get update|
| 重启dns |sudo apt-get install nscd   《先运行，报错再安装》  sudo /etc/init.d/nscd restart |
| 修改host文件 | sudo gedit /etc/hosts |

> The overriding design goal for Markdown's
> formatting syntax is to make it as readable
> as possible. The idea is that a
> Markdown-formatted document should be
> publishable as-is, as plain text, without
> looking like it's been marked up with tags
> or formatting instructions.

This text you see here is *actually- written in Markdown! To get a feel
for Markdown's syntax, type some text into the left window and
watch the results in the right.

## 准备本地编译

start to complie on local:

1.修改镜像地址，此处使用阿里云镜像 https://developer.aliyun.com/mirror/ubuntu
2.保存备份原始文件
```sh
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```
3.修改为阿里云镜像，使用快捷键ctrl+k全部剪切掉，ctrl+o、enter回车保存，ctrl+x退出
```sh
sudo nano /etc/apt/sources.list
```
4.更新apt
```sh
sudo apt-get update
```
5.安装更新。。。不懂
```sh
sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync
```
6.防止github被墙无法克隆，全局配置代理
> 具体介绍搜索wsl，大意就是子系统和window共享网络接口，所以本地主机是可以相互访问的
> 测试下来虚拟机会继承主机的host文件列表，所以如果github host可以用，在windows配好就行了，不过速度是在太慢了。
> 测试中，仅仅配置https_proxy无法达到代理的效果，哪怕是配置environment文件也不行，ubuntu还是会用自己的http接口跑出去
```sh
export ALL_PROXY="http://172.19.80.1:7890"
```
7.防止github clone ssl报错，安装ssl
```sh
sudo apt-get install openssh-server
sudo /etc/init.d/ssh start
```
8.生成config
```sh
./scripts/feeds update -a
./scripts/feeds install -a
make menuconfig
```
> 一步步执行下去
> 第三步会要求配置config文件，选择想要的固件就好了，其它默认，云编译就加了两个包都成功了

9.接着执行
```sh
make -j8 download V=s
```
10.未完待续。。。。。。

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)
