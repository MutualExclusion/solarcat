# Linux远程主机软件安装 #
-	[1.安装git](1-安装git仓库在远程主机（Linux）)
-	[2.安装jdk1.8](2-安装java在远程虚拟主机上)
-	[3.安装maven](3-安装maven在远程虚拟主机上)
## 1.安装git仓库在远程主机（Linux） ##
### 版本 ###
CentOS6.5
### 步骤 ###
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
### 至此 	*MutualExclusion 12/19/2018 8:12:44 PM* ###

## 2.安装java在远程虚拟主机上 ##
### 版本 ###
系统版本：CentOS
软件版本：java8
### 步骤 ###
#### 1.官网下载JDK ####
#### 2.将下载的JDK解压缩 ####
	# tar -zxvf jdk-8u191-linux-x64.tar.gz
#### [【tar】命令详解]() ####
#### 3.将JAVA HOME移动到/usr/local目录下 ####
	# cp java1.8.0_191 /usr/local -c
#### 4.修改配置文件 ####
	# vim /etc/profile
按i进入vim编辑模式，在文件末尾追加

	JAVA_HOME=/usr/local/jdk1.8.0_191
	CLASSPATH=$JAVA_HOME/lib/
	PATH=$PATH:$JAVA_HOME/bin
	export PATH JAVA_HOME CLASSPATH
按esc退出，shift+zz保存退出。
#### 5.重新加载资源文件 ####
	# source /etc/profile
#### 6.查看安装情况 ####
	#java -version
### 至此 	*MutualExclusion 12/19/2018 8:15:58 PM* ###

## 3.安装maven在远程虚拟主机上 ##
### 版本 ###
apache-maven-3.6.0<br>
jdk1.8版本
### 步骤 ###
#### 1.解压缩 ####
#### 2.将maven移动到/usr/local/maven3中 ####
	mv apache-maven-3.6.0-bin.tar /usr/local/maven3
#### 3.修改环境变量 ####
在/etc/profile中添加以下几行

	MAVEN_HOME=/usr/local/maven3
	export MAVEN_HOME
	export PATH=${PATH}:${MAVEN_HOME}/bin 

重启加载环境source /etc/profile
#### 验证 ####
	mvn -version
### 至此 	*MutualExclusion 12/19/2018 8:20:22 PM* ###

## 4.安装mail邮件 ##
### 版本 ###
### 步骤 ###
#### 直接安装 ####
	yum -y intall mailx
### 使用 ###
### 报错：发送失败 ###
	send-mail: warning: inet_protocols: IPv6 support is disabled: Address family not supported by protocol
	send-mail: warning: inet_protocols: configuring for IPv4 support only
	postdrop: warning: inet_protocols: IPv6 support is disabled: Address family not supported by protocol
	postdrop: warning: inet_protocols: configuring for IPv4 support only
待更新
### 至此 *MutualExclusion 12/19/2018 8:52:58 PM * ###