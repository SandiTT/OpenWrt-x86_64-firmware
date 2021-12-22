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
| 修改host文件 | sudo nano /etc/hosts |
| 修改environment变量（三个协议都需要配置） | sudo nano /etc/environment |
| 查看本地配置的proxy，用于检验是否配置成功 | env\|grep -i proxy |

## 准备本地编译

start to complie on local:

1.修改镜像地址，此处使用阿里云镜像 https://developer.aliyun.com/mirror/ubuntu
> Note: ubuntu 20.04(focal).
```sh
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```
> Note: ubuntu 18.04(bionic).
```sh
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```
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
> 
> 测试下来虚拟机会继承主机的host文件列表，所以如果github host可以用，在windows配好就行了，不过速度是在太慢了
> 
> 测试中，仅仅配置https_proxy无法达到代理的效果，哪怕是配置environment文件也不行，ubuntu还是会用自己的http接口跑出去
> 
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
> 
> 第三步会要求配置config文件，选择想要的固件就好了，其它默认，云编译就加了两个包都成功了

9.接着执行
```sh
make -j8 download V=s
```
10.该（wsl）环境下开始编译
>第一次一定要按照官方的说法，不能用多线程跑，否则大概率会失败，我也同样失败了
>
>默认config+单线程绝对能编译成功，lean说的不错，我确实成功了
>
>compile期间一定要准备好代理，因为每次生成ipk文件都会先下载源文件，但是到了后半段编译貌似就不需要代理了
```sh
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin make -j1 V=s 
```
11.搜索编译后的文件

Like This:


C:\Users\[windows username]\AppData\Local\Packages\......UbuntuonWindows......\LocalState\rootfs\home\[ubuntu username]

>lede
>> bin
>>> targets
>>>>>x86
>>>>>>64
>>>>>>>packages
>>>>>>>>- - -
>>>>>>>>- - -
>>>>>>>>* * *
>>>>>>>
>>>>>>>config.buildinfo
>>>>>>>
>>>>>>>feeds.buildinfo
>>>>>>>
>>>>>>>openwrt-x86-64-generic.manifest
>>>>>>>
>>>>>>>openwrt-x86-64-generic-kernel.bin
>>>>>>>
>>>>>>>***openwrt-x86-64-generic-squashfs-combined-efi.img*** ___This is what i need___
>>>>>>>
>>>>>>>openwrt-x86-64-generic-squashfs-combined-efi.vmdk
>>>>>>>
>>>>>>>openwrt-x86-64-generic-squashfs-rootfs.img
>>>>>>>
>>>>>>>sha256sums
>>>>>>>
>>>>>>>version.buildinfo
>>>>>>>
>>>
>>> packages 
>>>> x86_64
>>>>>
12.to be continued.
- - -
>原版官方不添加任何config参数，lean说的不错-保证成功，这对于虚拟机是在太方便了。初次编译加参数只会增加难度，建议什么都不选，另外我的云编译也成功了，也是什么都不加

## 修改openwrt默认管理ip

- 




1.打开windows文件夹
>C:\Users\<windows username>\AppData\Local\Packages\......UbuntuonWindows......\LocalState\rootfs\home\<ubuntu username>\lede\package\base-> >files\files\bin

2.打开编辑其中的config_generate文件

3.找到类似这段代码
```sh
case "$protocol" in
		static)
			local ipad
			case "$1" in
				lan) ipad=${ipaddr:-"192.168.1.200"} ;;
				*) ipad=${ipaddr:-"192.168.$((addr_offset++)).1"} ;;
			esac

			netm=${netmask:-"255.255.255.0"}

			uci -q batch <<-EOF
				set network.$1.proto='static'
				set network.$1.ipaddr='$ipad'
				set network.$1.netmask='$netm'
			EOF
			[ -e /proc/sys/net/ipv6 ] && uci set network.$1.ip6assign='60'
		;;
```
4.修改ipad-ip address地址为192.168.1.200等等

5.修改网关，这次没用到
```sh
set network.$1.gateway='192.168.1.1' 
set network.$1.dns='127.0.0.1 223.5.5.5 8.8.8.8'
```
6.保存

## 二次编译

Still includes bunch of problems:

1.更新lede
```sh
git pull
```
2.取消server certificate verification
```sh
git config --global http.sslverify false 
git config --global https.sslverify false
```
3.更新所有脚本
```sh
./scripts/feeds update -a && ./scripts/feeds install -a
```
4.读取/解析config文件
```sh
make defconfig
```
5.
```sh
make -j8 download
```
6.开始编译（wsl环境）,并且是多线程运行
```sh
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin make -j$(($(nproc) + 1)) V=s
```
7.失败，报错make[1]: *** [package/Makefile:110: /home/gu/lede/staging_dir/target-x86_64_musl/stamp/.package_compile] Error 2
添加命令，参考https://github.com/coolsnowwolf/lede/issues/4815
```sh
export GO111MODULE=on
export GOPROXY=https://goproxy.io
```
8.再次编译

## 重新再来编译
>make distclean （ make clean仅仅是清除之前编译的可执行文件及配置文件，而make distclean要清除所有生成的文件
```sh
make clean | make distclean 
```

```sh
export ALL_PROXY="http://127.0.0.1:10809"

source /etc/environment
export GO111MODULE=on
export GOPROXY=https://goproxy.cn,direct

git pull && ./scripts/feeds update -a && ./scripts/feeds install -a
make defconfig
make -j8 download
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin make -j$(($(nproc) + 1)) V=s


#暂时放弃了，windows+ubuntu编译太糟糕了，先用云编译吧
```

```sh

```

```sh

```

```sh

```
