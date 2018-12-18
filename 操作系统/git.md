# git #
## 1.安装git仓库在远程主机（Linux） ##
### 版本 ###
CentOS6.5
#### 1.查看git是否已经安装在本机上 ####
	# git --version
安装了就会返回安装版本信息，没有安装就是命令没有发现（-bash: git: command not found）。
#### 2.更新yum ####
	# yum -y update //升级系统包和系统内核。
此外，yum -y upgrade只升级所有包，不升级软件和系统内核。更新完成后使用：

	#cat /etc/issue

命令可以查看系统内核版本。如下：

	[root@VM_0_7_centos git]# cat /etc/issue
	CentOS release 6.10 (Final)
	Kernel \r on an \m

### 3.安装依赖包 ###
	# yum -y install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker
### 4.下载最新git版本并解压安装 ###
	# wget https://github.com/git/git/archive/v2.3.0.zip //下载完成后有一个v2.3.0.zip压缩包
	# unzip v2.3.0.zip //解压
	# cd git-2.3.0
	# make prefix=/usr/local/git all
	# make prefix=/usr/local/git install
这步完成完成后基本上部署完毕，输入git --version可以查看git版本信息。