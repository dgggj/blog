---
title: linux命令
tags: linux
categories: linux
abbrlink: 17973
date: 2022-04-05 12:47:30
---


#  Linux





## 一简介



**Linux一切皆文件**



![sdsda](https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/linux/linux1.png)

### 1.1具体的目录结构（了解）

- /bin[常用]（/user/bin、/usr/local/bin）是binary的缩写。存放最经常使用的命令



- /sbin[常用]（/user/sbin、/usr/local/sbin）s是super user存放的是系统管理员使用的系统管理程序



- /home 【常用】 存放普通用户的主目录，在Linux中每个用户都有一个自己的目录，一般该目录是以用户的账号命名



- /root 【常用】 该目录为系统管理员，也称作超级权限者的用户主目录



- /lib 系统开机所需要的最基本的动态链接共享库，起作用类似于Windows里的DLL文件。几乎所有的应用程序都需要用到这些共享库



- /lost+found 这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件



- /etc 【常用】所有的系统管理所需要的配置文件和子目录，比如安装mysql数据库，my.conf



- /usr 【常用】这是一个非常重要的目录，用户很多应用程序都放在这个目录下，类似于windows下的program files目录。



- /boot 【常用】存放的是启动Linux时使用的一些核心文件，包括一些链接文件以及镜像文件



- /proc 这个目录是一个虚拟的目录，他是系统内存的映射，访问这个目录来获取系统信息



- /srv      service缩写，该目录存放一些服务器启动后需要提取的数据



- /sys     这是linux2.6内核的一个很大的变化，该目录下安装了2.6内核中新出现的一个文件系统sysfs



- /tmp这个目录是用来存放一些临时文件的



- /dev类似于windows的设备管理器，把所有的硬件用文件的形式存储



- /media【常用】linux系统会自动识别一些设备，例如U盘，光驱等等，当识别后，linux会把识别的设备挂载到这个目录下



- /mnt 【常用】系统提供该目录是为了让用户临时挂载别的系统文件，我们可以将外部的存储挂载在/mnt/上，然后进入该目录就可以查看里面的内容了。D:/mushare



- /opt 这是给主机额外的安装软件所存放的目录。如安装ORACLE数据库就可以放到该目录下。默认为空



- /usr/local【常用】 这是另一个给主机额外安装软件所安装的目录。一般是通过编译源码的方式安装的程序



- /var 【常用】这个目录中存放着在不断扩充的东西 ，习惯的将经常修改的目录放在这个目录下。包括各种日志文件



- /selinux 【security-enhanced linux】

SELinux是一种安全子系统，他能控制程序自能访问特定的文件，有三种工作模式，可以自行设置







- 查看IP地址ens33:

```
ifconfig
```



- 重启linux系统

```bash
reboot
```



## 二介绍

### 2.1vi和vim

Linux系统会内置vi文本编辑器

Vim具有程序编辑的能力，可以看做是Vi的增强版本，可以主动的以字体颜色辨别语法的正确性，方便程序设计。代码补完、编译错误跳转等方便编程的功能相当丰富，在程序员中被广泛使用。（vim就是vi的增强版）



### 2.2 vi和vim常用的三种模式

正常模式
以vim打开一个档案就直接进入一般模式了（这是默认的模式）。在这个模式中，可以使用【上下左右】键来移动光标，你可以使用【删除字符】或【删除整行】来处理档案内容，也可以使用【复制、粘贴】来处理你的文件数据。

插入模式
按下i，I，o，O，a，A，r，R等任何一个字母之后才会进入编辑模式，一般来说按i就可

命令行模式
在这个模式中，可以提供你相关指令，完成读取、存盘、替换、离开vim、显示行号等动作则是在此模式中达成的！



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210620104147374.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjMyNDE0,size_16,color_FFFFFF,t_70)

使用

在一般模式下

1.拷贝当前行 yy，拷贝当前行向下的5行  5yy，并粘贴（输入p）

2.删除当前行 dd，删除当前向下5行 5dd

3查找某个单词按/关键次。回车查找。输入n是查找下一个

4设置文件的行号，取消文件的行号命令行下【:set nu和:set nonu】

5使用快捷键到该文档的最末行（G）和最首行【gg】

6撤销【u】

7.快速到达文档某一行。输入20，在输入shift+g





### 2.3关机重启命令

 ```bash
  shutdown -h now   立刻进行关机-h是halt停止的意思
  shutdown -h 1					1分钟后关机
  shutdowm -r now    立刻重启计算机
  halt						立刻进行关机
  reboot          立刻重启计算机
  sync					把内存的数据同步到磁盘
  ```







### 2.4用户登录和注销

基本介绍
1、登陆时尽量少用root账号登录，因为他是系统管理员，最大的权限，避免操作失误。可以利用普通用户登录，登陆后再用”su - 用户名“命令来切换成系统管理员身份

2、在提示符下输入logout即可注销用户

使用细节
logout注销指令在图形运行级别无效，在shell界面有效







### 2.5、用户管理

#### 2.5.1基本介绍

Linux系统是一个多用户多任务的操作系统，任何一个要使用系统资源的用户，都必须先向系统管理员申请一个账号，然后以这个账号的身份进入系统

#### 2.5.2添加用户

- 基本语法

```bash
useradd 用户名
```

实列(root权限下)
useradd che
添加一个用户chucheng，默认该用户的家目录在/home/che

细节说明
1、当用户创建成功后果，会自动的创建和用户同名的家目录
2、也可以通过useradd -d 指定目录  新的用户名     来给新创建的用户指定家目录



#### 2.5.3指定/修改密码

- 基本语法

```bash
passwd 用户名
```







#### 2.5.4 删除用户

```bash
userdel   用户名    删除用户，但是要保留目录
userdel -r 用户名		删除用户以及用户主目录 
```



#### 2.5.5查询用户信息指令

- 基本语法

```bash
id 用户名
```

 





#### 2.5.6切换用户

- 介绍

在操作Linux时，如果当前用户的权限不够，可以通过su - 指令，切换到更高权限的用户，如root

- 基本语法

```bash
su - 用户名
```

 

- 细节说明

1.从权限高的用户切换到权限低的用户时，不需要输入密码，反之需要

2.当需要返回到原来用户时，使用exit/logout指令





#### 2.5.7 查看当前用户/登录用户

- 基本语法

```bash
whoami/who am i
```

![sdss](https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/linux/linux2.png)







#### 2.5.8 用户组

- 介绍

类似于角色，系统可以对有共性/权限的多个用户进行统一的管理

- 新增组

```bash
指令：groupadd 组名
```

- 删除组

```bash
 指令（基本语法）：groupdel 组名
```

- 注意：如果当添加用户时没有指定组，会默认创建和这个用户同名的组，同时把用户放到该组里



- 增加用户时直接加上组

```bash
 指令：useradd -g 用户组 用户名
```



- 移动组

```bash
usermod -g 组名 用户名
```



#### 2.5.9 用户和组相关文件

/etc/passwd 文件
用户（user）的配置文件，记录用户的各种信息

每行的含义：用户名：口令：用户标识号：组标识号：注释性描述：主目录：登录Shell

/etc/shadow文件
口令配置文件

每行的含义：登录名：加密口令：最后一次修改时间：最小时间间隔：最大时间间隔：警告时间：不活动时间：失效时间：标志

/etc/group文件
组（group）的配置文件，记录Linux包含的组的信息

每行含义：组名：口令：组标识号：组内用户列表









## 三、Linux实践——实用指令

### 3.1指定运行级别

- 基本介绍

运行级别说明：

0：关机

1：单用户【找回丢失密码】

2：多用户状态没有网络服务

3：多用户状态有网络服务

4：系统未使用保留给用户

5：图形界面				graphical.target

6：系统重启

常用运行级别是3和5，也可以指定默认运行级别

- 应用实例

命令：init[0123456]应用案例：通过init来切换不同的运行级别，比如动5-3，然后关机

```bash
查看当前的运行级别   systemctl get-ddefault
```

```bash
改变为   systemctl set-default multi-user.target
```





### 3.2 root密码丢失

- 在启动开机页面按e进入编辑页面
- 在编辑页面，向下移动，找到以“Linux16”开头的所在行，在行的最后面输入：init=/bin/sh
- 输入完后按ctrl+x 进入单用户模式
- 接着在光标闪烁的地方输入： mount -o remount,rw / (注意空格)完成后输入enter
- 在新的一行输入   passwd   ，完成后回车键。输入密码，确认密码。完成后会出现passwd。。。式样。修改成功
- 接着在鼠标闪烁的地方输入：  touch /.autorelabel  (注意touch 与/后面有一个空格)
- 输入exec /sbin/init







### 3.3帮助指令



- man获得帮助信息

```bash
基本语法：man [命令或配置文件]（功能描述：获得帮助信息）
```



在linux下隐藏文件是以 “.” 开头的

- help指令

```bash
基本语法：help 命令 （功能描述：获得shell内置命令的帮助信息）
```







### 3.4文件目录类



- pwd指令

```bash
基本语法：pwd    （功能描述：显示当前工作目录的绝对路径）
```

------

- ls指令

```bash
基本语法：ls [选项] [目录或是文件]
```

常用选项

-a ： 显示当前目录所有的文件和目录，包括隐藏的

-l ： 以列表的方式显示信息





- cd指令

```bash
基本语法：cd [参数] （功能描述：切换到指定的目录）
```

理解：绝对路径和相对路径（相对路径是针对当前位置的路径）

cd ~ 或者cd 回到自己的家目录

cd … 回到当前目录的上一级目录





- mkdir指令

```bash
基本语法：mkdir [选项] 要创建的目录
```

理解：mkdir指令用于创建目录（默认只能创建一级目录）

-p :创建多级目录

------

- rmdir指令（用于删除空目录）

```bash
基本语法：rmdir [选项] 要删除的空目录
```

使用细节：

rmdir删除的是空目录，如果欲删除的目录下有内容则无法删除

==如果需要删除非空目录，需要使用rm -rf 要删除的目录



- touch指令（用于创建空文件）

```bash
touch 文件名称
```



- cp指令（copy拷贝文件导指定目录）

```bash
cp [选项] source dest
```

常用选项：-r 递归复制真个文件夹

应用实例:

将 /home/hello.txt 拷贝到 /home/bbb 目录下

```bash
cp /home/hello.txt /home/bbb
```

递归复制整个文件夹，将/home/aaa 目录下的文件全部拷贝到/home/bbb下

```bash
cp -r /home/aaa /hom/bbb
(是将整个目录和目录本身拷贝进来)
```

使用强制覆盖不提示的方法：\cp





- rm指令（remove，移除文件或目录）=一定要小心！！！
  基本语法：rm [选项] 要删除的文件或目录

常用选项

-r : 递归删除整个文件夹

-f ：强制删除不提示

使用细节：强制删除不提示的方式，带上-f参数即可

- mv指令（move，移动文件与目录或重命名)（以可剪切文件夹）

```bash
mv oldNameFile newNamefile (功能描述：重命名)
mv /temp/movefile /targetFolder (功能描述：移动文件，剪切)
```







- cat指令（查看文件内容）

```bash
cat [选项] 要查看的文件
```



常用选项： -n 显示行号

使用细节：cat只能浏览文件，而不能修改文件，为了浏览方便，一般会带上管道命令 |more

```bash
cat -n /etc/profile |more
```



- more指令

more指令是一个基于VI编辑器的文本过滤器，它以全屏幕的方式按页显示文本文件的内容。more指令中内置了若干快捷键（交互的指令）

```bash
基本语法：more 要查看的文件
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210622081656367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjMyNDE0,size_16,color_FFFFFF,t_70)





- echo指令

echo输出内容到控制台

基本语法

```bash
echo 【选项】【输出内容】
```

应用实例

案例：使用echo指令输出环境变量 $PATH  $HOSTNAME.      echo  $PATH

案例：使用echo指令输出helloworld.      echo "helloworld"







- head

  用于显示文件的开头部分内容。默认是前十行的内容

  基本语法

  head 文件

  head -n 5 文件（功能描述：查看文件头5行内容，5可以是任意数字）



- tail

用于显示文件的尾部部分内容。



tail 文件

tail -n 5 文件（功能描述：查看文件头5行内容，5可以是任意数字）

tail -f 文件（功能描述：实时追踪该文件的所有更新）

比如说。在一个窗口输入tail -f mydata.txt.此窗口就在实时监控这个文件

当再打开一个控制器echo “hello” >mydata.txt.则在监控窗口实时出现hello。



- \>重定向覆盖
- \>>追加

基本语法：

1）ls -l >文件     （功能描述：列表的内容写入文件a .txt中（覆盖写））

2）ls -al >>文件    （功能描述：列表的内容追加到文件a.txt的末尾）

3）cat 文件1 > 文件2 （功能描述：将文件1的内容覆盖到文件2）

4）echo “内容” >> 文件



应用案例：

- 将/home目录下的文件列表，写入到/home/info.txt中
- 将当前日历信息追加到/home/mycal文件中







- ln指令

软连接也称为符号链接，类似于windows里的快捷键方式，主要存放了连接其他文件的路径

```bash
基本语法：ln -s [原文件或目录] [软链接名]（功能描述：给原文件创建一个软链接）
```

案例一：在/home目录下创建一个软链接myroot，连接到/root目录

ln -s /root  /home/myroot

案例二：删除软链接myroot

rm /home/myroot



- history指令

查看已经执行过历史命令，也可以执行历史指令

```bash
history		  显示所有的历史命令
history 10    显示最近使用过的10个指令	
!5 			  执行历史编号为5的指令
```





### 时间日期类

- date指令-显示当前日期

```bash
基本语法：
date				显示当前时间
date "+%Y"			显示当前年份   小写y只显示后面2位22
date "+%m"			显示当前月份
date "+%d"			显示当前是哪一天
date "+%Y-%m-%d %H:%M:%S" 显示年月日时分秒
```



date指令还可以设置日期

```bash
date -s 字符串时间
date -s "2021-6-20 16:44:30"
```



- cal指令

查看日历指令

```bash
cal [选项]      （功能描述：不加选项，显示本月日历）
cal 2021      显示2021整年的日历
```







### 6.5搜索查找类

- find指令

  find指令指令 将从指定目录向下递归遍历各个子目录，将满足条件的文件或者目录显示在终端上

```bash
find [搜索范围] [选项]
```

选项说明（可填选项）

| 选项            | 功能                             |
| --------------- | -------------------------------- |
| -name<查询方式> | 按照指定的文件名查找模式查找文件 |
| -user<用户名>   | 查找属于指定用户名所有文件       |
| -size<文件大小> | 按照指定的文件大小查找文件       |

说明：当查询路径下无该文件，就不会有任何显示



应用实例：

- 按文件名：根据名称查找/home目录下的hello.txt文件

  ```bash
  find /home -name hello.txt
  ```

  

- 按拥有者：查找/opt目录下，用户名称为nobody的文件

- 查找整个linux系统下大于200M的文件（+n大于 -n小于 n等于,单位：k，M，G）





ls -lh









- locate指令

  

  locate指令可以快速定位文件路径。locate命令利用事先建立的系统中所有文件名称及路径的locate数据库实现快速定位的文件名。Locate指令无需遍历整个文件系统，查询速度较快。为了保障查询结果的准确度，管理员必须定期更新locate时刻

```bash
  基本语法：locate 搜索文件
```


特别说明：由于locate指令基于数据进行查询，所以第一次运行前，必须是使用updatedb指令创建locate数据库



- which指令 ，可以查看某个指令在哪个目录下





- grep指令和管道符号 |

grep过滤查找，管道符，“|“，表示将前一个命令输出传递给后面的命令处理

```bash
基本语法：grep [选项] 查找内容 源文件
```

选项：

-n 显示匹配行及行号

-i 忽略字母大小写



两种写法

```bash
cat /home/hello.txt |grep -n "yes"       hello.txt文件夹中输出同样的
grep -n "yes" /home/hello.txt
```





#### 6.6 压缩和解压

- gzip/gunzip指令

gzip用于压缩文件，gunzip用于解压文件的

```bash
gzip文件		（功能描述：压缩文件，只能将文件压缩为*.gz文件
gunzip文件    （功能描述：解压文件命令）
```



- zip/unzip指令

zip用于压缩文件，unzip用于解压的，在整个项目打包发布中很有用的

```bash
zip [选项] xxx.zip 将要压缩的内容（一般是文件和文件夹）
uzip [选项] xxx.zip （功能描述：解压文件）
```



zip的常用项： -r 递归压缩，即压缩目录

zip -r myhome.zip /home/

unzip的常用项 ： -d<目录> : 指定解压后文件的存放目录

unzip -d