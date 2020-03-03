# Linux学习笔记

## 第五章 Linux的目录结构

**常见目录说明：**

- **/bin：** 存放二进制可执行文件(ls、cat、mkdir等)，常用命令一般都在这里；
- **/etc：** 存放系统管理和配置文件；
- **/home：** 存放所有用户文件的根目录，是用户主目录的基点，比如用户user的主目录就是/home/user，可以用~user表示；
- **/usr ：** 用于存放系统应用程序；
- **/opt：** 额外安装的可选应用程序包所放置的位置。一般情况下，我们可以把tomcat等都安装到这里；
- **/proc：** 虚拟文件系统目录，是系统内存的映射。可直接访问这个目录来获取系统信息；
- **/root：** 超级用户（系统管理员）的主目录（特权阶级^o^）；
- **/sbin:** 存放二进制可执行文件，只有root才能访问。这里存放的是系统管理员使用的系统级别的管理命令和程序。如ifconfig等；
- **/dev：** 用于存放设备文件；
- **/mnt：** 系统管理员安装临时文件系统的安装点，系统提供这个目录是让用户临时挂载其他的文件系统；
- **/boot：** 存放用于系统引导时使用的各种文件；
- **/lib ：** 存放着和系统运行相关的库文件 ；
- **/tmp：** 用于存放各种临时文件，是公用的临时文件存储点；
- **/var：** 用于存放运行时需要改变数据的文件，也是某些大文件的溢出区，比方说各种服务的日志文件（系统启动日志等。）等；
- **/lost+found：** 这个目录平时是空的，系统非正常关机而留下“无家可归”的文件（windows下叫什么.chk）就在这里。

## 第六章 vi和vim

&emsp;&emsp;rz  		                  上传文件到Linux服务器  

&emsp;&emsp;sz  文件名/路径	 下载文件到本地

```
vim 的基本指令
0）输入模式 i、a、o、 s、 I、 A、 O、 S 区别
	 i、I：在光标所在字符前开始插入
     a、A：在光标所在字符后开始插入
     o、O：在光标所在行的下面另起一新行插入
     s、S：删除光标所在的字符并开始插入
1）拷贝当前行    yy，拷贝当前行向下的5行 	   5yy，并粘贴    【p】
2）删除当前行    dd，删除当前行向下的5行    5dd
3）在文件中查找某个单词【命令行下/查找的内容	然后按回车。输入n就是查找下一个】
4）设置文件的行号，取消文件的行号【命令行下    :set nu 和    :set nonu】
5）编辑    /etc/profile  文件，使用快捷键到达文档的最末行【G】和首行【gg】。注意要在正常模式下执行
6）在一个文件中输入  "hello"，然后又撤销这个动作，在正常模式下输入  u
7）编辑    /etc/profile wenjian1, 并将光标移动到  20 行 shift+g
     1、显示行号    :set nu
     2、输入20这个数
     3、输入 shift + g
```

## 第七章 开机、重启和用户登录注销

### 7.1 开机和重启

```
shutdown
       shutdown  -h now : 表示立即关机
       shutdown  -h 1 : 表示1分钟后关机
       shutdown  -r now : 立即重启
halt
       就是直接使用，效果等价于关机
reboot
       重启
sync
       把内存数据同步到磁盘，防止数据丢失，建议关机或者重启时，都先执行一边
```

### 7.2 用户登录注销  

&emsp;&emsp;登录时尽量少使用root账号登录，因为它是系统管理员，最大的权限，避免操作失误。可以利用普通用户登录，登陆后再用 “su - 用户名”命令来切换成系统管理员身份  

&emsp;&emsp;在提示符下输入logout即可注销用户

## 第八章 用户管理

### 8.1 基本概念

root ----》 root组  ----》 /root 目录  

用户 ----》 属于某个组（权限的集合）----》 用户家目录（在/home/目录下，当用户登录时，会自动进入到该目录）

说明：  

&emsp;&emsp;1）Linux系统是一个多用户多任务的操作系统，任何一个要使用系统资源的用户，都必须首先向系统管理员申请一个账号，然后以这个账号的身份进入系统。  

&emsp;&emsp;2）一个用户至少属于一个组。

### 8.2 用户的增删改查

```
useradd  【选项】 用户名  密码
     如果不指定组，会默认创建一个和用户名同名的组，并将创建的用户分配到改组
     也可以通过    useradd -d  指定目录 用户名  密码    将用户分配给指定的家目录
     创建时不指定密码，可以另外在命令行输入  passwd  用户名   修改密码

userdel  用户名
     1）删除用户，但是要保留家目录
	userdel    用户名
     2）删除用户以及用户的家目录
	userdel  -r  用户名
```

### 8.3 查询用户的信息

id  用户名  

&emsp;&emsp;可以查出uid gid 组 等信息

### 8.4 切换用户

su  - 切换用户名  

&emsp;&emsp;可以用exit命令回退到原来的用户  

&emsp;&emsp;可以使用  whoami  指令查询当前是哪个用户

### 8.5 用户组

&emsp;&emsp;用户组类似于角色，系统可以对有共性的多个用户进行统一的管理

#### 8.5.1 创建组

 &emsp;&emsp;groupadd  组名

#### 8.5.2 删除组

&emsp;&emsp;groupdel  组名

#### 8.5.3 增加用户时直接加上组

 &emsp;&emsp;useradd  -g  用户组  用户名

#### 8.5.4 修改用户组

&emsp;&emsp;usermod  -g  用户组  用户名

### 8.6  用户和组的配置文件

用户配置文件（用户信息）：/etc/passwd    

&emsp;&emsp;每行的含义     用户名：口令（密码）：用户标识号（用户id）：组标识号（组id）：注释性描述：主目录：登录shell
组配置文件（组信息）：/etc/group  
&emsp;&emsp;每行的含义     组名：口令：组标识号：组内用户列表
口令配置文件（密码和登录信息，是加密的）：/etc/shadow  
&emsp;&emsp;每行的含义      登录名：加密口令：最后一次修改时间：最小时间间隔：最大时间间隔：警告事件：不活动时间：失效时间：标志

## 第九章  实用指令

### 9.1  运行级别和找回密码

```
linux共有7级运行级别
​        0：关机
​        1：单用户（找回丢失密码）
​        2：多用户无网络服务
​        3：多用户有网络服务（常用）
​        4：系统未使用保留给用户
​        5：图形界面
​        6：系统重启
系统运行级别配置文件：/etc/inittab   中  id:3:initdefault: 这一行
```

### 9.2  切换到指定运行级别的指令  

&emsp;&emsp;init [0123456]  

&emsp;&emsp;思考：如何找回root密码？  

&emsp;&emsp;思路：进入到单用户模式，然后修改root密码。因为单用户模式，root不需要密码就可以登录。

### 9.3 帮助指令

```
man  [命令或配置文件]（功能描述，获得帮助信息）jk或者上下键进行上下翻页，q退出来
help  [命令]
```

### 9.4 文件目录类

    pwd  显示当前目录的绝对路径
    ls  [选项]  [目录或是文件] 
    	-a    显示当前目录所有的文件和目录，包括隐藏的
    	-l     以列表的方式显示目录
    	-lh   将文件大小显示为K、M、G等直观可视的大小
      
    cd  [参数]
    	cd  ~  回到自己的家目录
    	cd  ..   回到当前目录的上一级目录
    
    mkdir   [选项]    要创建的目录
    	-p    创建多级目录
    rmdir  [选项]    要删除的空目录
    	-p     删除多级目录
    
    touch  文件名称（可以一次性创建多个指令）
    
    cp  [选项]  source  dest
    	-r     递归复制整个文件夹
    
    rm  [选项]    要删除的文件或目录
    	-r     递归删除整个文件夹
    	-f     强制删除不提示
    
    mv  source  dest
    	source和dest路径一致时，可以用于文件重命名
    
    cat  [选项]    要查看的文件
    	-n    显示行号（包括空格）
    	-b    显示行号（不包括空行）
    	|   more     管道符+more，分页查询，回车键下一行，空格键下一页，q退出
    
    more  要查看的文件
    	回车键下一行，空格键下一页，ctrl+b上一页，ctrl+f下一页，pageup和pagedown也支持上下翻页，q退出
    
    less  要查看的文件      
    	说明：less指令在显示文件内容时，并不是一次性将整个文件加载之后才显示。而是根据显示需要加载内容，对于显示大型文件具有较高的效率，翻页和more类似
    	-m     显示百分比
    	-N      显示行号
      
    >指令和>>指令
    >输出重定向和>>追加
    	1）ls  -l > 文件			（功能描述：列表的内容写入文件中（覆盖写））
    	2）ls  -al >>文件			（功能描述：列表的内容追加到文件的末尾）
    	3）cat  文件1  >  文件2	   （功能描述：将文件1的内容覆盖到文件2）
    	4）echo  "内容"  >>  文件
    
    echo  [选项]  [输出的内容]
    	常用于输出环境变量	echo  $PATH
    
    head  文件				（功能描述：默认查看文件前10行的内容）
    	head  -n  5  文件		（功能描述：查看指定文件的前5行）
    
    tail  文件				（功能描述：显示文件后10行）
    	tail  -n  5  文件		（功能描述：查看指定文件的后5行）
    	tail  -f  文件		（功能描述：实时总追文件的所有跟新）
    
    ln  -s  [源文件或目录]  [软件链接]		相当于windows的快捷方式
    	细节说明：通过软连接进入到某个目录时，当我们使用pwd指令查看目录时，仍然看到的是软连接所在的目录。
    
    history		查看已经执行过的历史指令，也可以执行历史指令
    	history  10	  （功能描述：查看最后10个执行的指令）
    	!100		  （功能描述：执行历史编号为100的指令）

### 9.5  时间日期类

    date							显示当前日期时间
    date  "+%Y-%m-%d   %H:%M:%S"	显示当前年月日时分秒
    
    设置日期
    date  -s  字符串时间，如"2020-2-23 17:40:00"
    
    cal  [选项]	      （功能描述：不加选项，显示本月日历）
    cal  2020        	显示2020年的所有月份

### 9.6  搜索查找类

    find  [搜索范围]  [选项]
    	-name		根据名字来查找，可以用通配符  *
    	-user		根据拥有者来查找
    	-size		注意：k要小写，M要大写。根据大小来查找，find / -szie +20M  在根目录下查找大于20M的文件，+大于，直接写20M就是等于20M，-小于
      
    locate  搜索的文件
    	说明：locate指令可以快速定位文件路径。locate指令利用事先建立的系统中所有文件名称及路径的locate数据库事先快速定位给定的文件。
        locate指令无需遍历整个文件夹，查询速度极快。为了保证查询结果的准确度，管理员必须定时更新locate时刻。
        由于locate指令基于数据库进行查询，所以第一次运行前，必须使用updatedb指令创建locate数据库。
    
    |   管道符：表示将前一个命令的处理结果输出传递给后面的命令处理
    grep  [选项]  查找的内容  源文件
    	-n	显示匹配行及行号
    	-i	忽略字体大小写

### 9.7  压缩和解压类

	gzip  文件		（功能描述：压缩文件，只能将文件压缩为*.gz文件）注意：压缩完之后，原来的文件不保留
	gunzip  文件.gz	（功能描述：解压缩文件命令）注意：解压完之后，原先的压缩文件也不存在了
	
	zip  [选项]  xxx.zip  要压缩的文件或文件夹		（功能描述：压缩文件和目录的命令）
		-r	递归压缩
		-q  不显示执行过程
	
	unzip  要解压的文件
	unzip  -d  指定解压后的文件存放目录  要解压的文件	
	
	tar  []  xxx.tar.gz  要压缩的文件或文件夹
	tar 是指打包指令，最后打包后的文件是.tar.gz的文件
		-c	产生.tar打包文件
		-v	压缩或解压缩时显示详细信息
		-f	指定压缩后的文件名
		-z	打包并同时压缩
		-x	解压
		解压到指定目录要 tar  -zxvf  xxx.zip  -C  指定目录（目录要预先创建好）

### 9.8  组管理和权限管理

Linux组的基本介绍  
&emsp;&emsp;在Linux中的每个用户必须属于一个组，不能独立于组外。在Linux中每个文件有所有者、所在组、其他组的概念。  

&emsp;&emsp;文件：1、所有者；2、所在组；3、其他组；4、改变用户所在的组

#### 9.8.1  文件/目录所有者

 &emsp;&emsp;一般为文件的创建者，谁创建了该文件，谁自然就成为该文件的所有者。

    查看文件的所有者
    ls  -ahl
    
    修改文件所有者（change owner）
    chown  用户名  文件名
    	-R	递归
    chown  newOwner:newGroup  file		同时改变文件的所有者和所有组
    
    修改文件所在的组（change group）
    chgrp  组名  文件名
    	-R	递归

```
权限的基本介绍
-rwxr--r--. 1 root police 0 2月  24 22:55 ok.txt
	最前面的-代表文件的类型：- 普通文件；    d 目录；    l 软链接；    c 字符设备；    b 块文件（如硬盘）
    第一组rwx三个字符代表文件所有的  读权限，写权限，操作权限
    第二组代表文件所有者所在的组的  读权限，写权限，操作权限
    第三组代表其他组拥有的权限
    1                     如果是文件，表示硬链接的数，如果是目录则表示该目录的子目录个数
    root                  代表文件所有者
    police                代表文件所在的组
    0                     代表文件大小，文件的每行结尾都会有\0，因此输入五个字符的hello，文件大小会显示6。如果是目录则大小都是4096（CentOS6）
    2月24  22:55			 代表文件最后修改时间
    ok.txt                代表文件名
```

```
rwx作用到文件
1）[r]代表可读（read）：可以读取，查看
2）[w]代表可写（write）：可以修改，但是不代表可以删除该文件，删除一个文件的前提条件是对该文件所在的目录有写权限，才能删除该文件
3）[x]代表可执行（execute）：可以被执行

rwx作用到目录
1）[r]代表可读（read）：可以读取，ls查看目录内容
2）[w]代表可写（write）：可以修改，目录内创建+删除+重命名目录
3）[x]代表可执行（execute）：可以进入该目录
```

#### 9.8.2  修改权限

&emsp;&emsp;通过chmod指令，可以修改文件或者目录的权限

```
方式一：+、-、=变更权限
	u：所有者	g：所在组	o：其他人	a：所有人（u、g、o的总和）
1）chmod  u=rwx,g=rx,o=x  文件目录名
2）chmod  o+w  文件目录名
1）chmod  a+x  文件目录名
```

```
方式二：通过数字变更权限
	规则：r=4  w=2  x=1  rwx=7
chmod  777  文件目录名
```

### 9.9  任务调度

&emsp;&emsp;任务调度：是指系统在某个时间的特别的命令或程序  

&emsp;&emsp;任务调度分类：1.系统工作：有些重要的工作必须周而复始地执行。如病毒扫描等。2.个别用户工作：个别  

用户可能希望执行某些程序，如数据备份

```
crontab  [选项]
	-e  编辑crontab定时任务
	-l  查询crontab定时任务
	-r  删除当前用户所有的crontab任务
```

| 项目     | 含义                 | 范围                      |
| -------- | -------------------- | ------------------------- |
| 第一个 * | 一小时当中的第几分钟 | 0-59                      |
| 第二个 * | 一天当中的第几个小时 | 0-23                      |
| 第三个 * | 一个月当中的第几天   | 1-31                      |
| 第四个 * | 一年当中的低级月     | 1-12                      |
| 第五个 * | 一周当中的星期几     | 0-7（0和7都该代表星期日） |

&emsp;&emsp;特殊符号的说明

| 特殊符号 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| *        | 代表任何时间。比如第一个*就代表一小时中每分钟都执行一次的意思 |
| ,        | 代表不连续的时间。比如0 8,12 * * *命令，代表在每天的8点整，12点整都执行一次命令 |
| -        | 代表连续的时间范围。比如0 5 *  * 1-6命令，代表在周一到周六凌晨5点执行命令 |
| */n      | 代表每隔多久时间执行一次，比如*/10 * * * *命令，代表每隔10分钟就执行一遍命令 |

## 第十章  Linux磁盘分区、挂载

### 10.1 分区基础知识

&emsp;&emsp;windows分区的方式：

```
MBR分区
1）最多支持四个主分区
2）系统只能安装在主分区
3）扩展分区要占一个主分区
4）MBR最大只支持2TB，但拥有最好的兼容性
```

```
GPT分区
1）支持无线多个主分区（但操作系统可能限制，比如windows下最大128个分区）
2）最大支持18EB的大容量（EB=1024PB，PB=1024TB）
3）windows 64位以后支持GPP
```

### 10.2 Linux分区

&emsp;&emsp;1）Linux来说无论有几个分区，分给哪一目录使用，归根结底就只有一个根目录，一个独立且唯一的文件结  

构，Linux中每个分区都使用来组成整个文件系统的一部分。  

&emsp;&emsp;2）Linux采用了一种叫“载入”的处理方法，他的整个文件系统中包含一整套的文件和目录，且将一个分区和  

 一个目录联系联系起来。这是要载入的一个分区将使它的存储空间在一个目录下获得。

```
硬盘说明：
1）Linux硬盘分IDE硬盘和SCSI硬盘，目前基本上是SCSI硬盘
2）对于IDE硬盘，驱动器标识符为“hdx~”，其中“hd”表明分区所在设备的类型，这里是指IDE硬盘。“x”位盘号（a为基本盘，b为基本从属盘，c为辅助主盘，d为辅助从属盘），“~”代表分区，前四个分区用数字1到4表示，他们是主分区或扩展分区，从5开始就是逻辑分区。例如：hda3表示为第一个IDE硬盘上的第三个主分区或扩展分区，hdb2表示为第二个IDE硬盘上的第二个主分区或扩展分区。
3）对于SCSI银盘则标识为“sdx~”，SCSI硬盘是用”sd”来表示分区所在设备的类型的，其余则和IDE硬盘的表示方法一样。

lsblk		查看分区使用情况
	-f  	查看详细树状信息
```

```
增加一块硬盘
1）虚拟机添加硬盘
2）开始对/sdb分区
	fdisk  /dev/sdb
		m	显示命令列表
		p	显示磁盘分区 同 fdisk -l
		n	新增分区
		d	删除分区
		w	写入并退出
	说明：开始分区后输入n，洗澡能分区，然后选择p，分区类型为主分区。两次回车默认剩余全部空间。最后输入w写入分区并退出，若不保存退出输入q。
3）格式化磁盘
	分区命令：mkfs -t ext4 /dev/sdb1
	其中etx4是分区类型
4）挂载：将一个分区和一个目录联系起来
	mount 设备名称 挂载目录
	例如：mount /dev/sdb1 /newdisk
	unmount 设备目录或挂载目录
	例如：umount /dev/sdb1 或者 umount /newdisk
5)永久挂载
	通过修改/etc/fstab实现挂载
	添加完成后 执行 mount -a 立即生效
```

### 10.3 磁盘查询使用指令

```
查询系统整体磁盘使用情况
df
	-h  带计量单位
	
查询指定目录的磁盘占用情况，默认为当前磁盘
du  /目录
	-s  指定目录占用大小汇总
	-h  带计量单位
	-a  含文件
	--max-depth=1  子目录深度
	-c  列出明细的同时，增加汇总值
	
	实例：查询/opt目录的磁盘占用情况，深度为1
	du  -ach --max-depth=1  /opt
```

### 10.4 磁盘情况-实用指令举例

1）统计/home文件夹下文件的个数

```
ls -l /home | grep "^-" | wc -l
```

2）统计/home文件下目录的个数

```
ls -l /home | grep "^d" | wc -l
```

3）统计/home文件夹下文件的个数，包含子文件夹里的

```
ls -lR /home | grep "^-" | wc -l
```

4）统计/home文件夹下目录的个数，包含子文件夹里的

```
ls -lR /home | grep "^d" | wc -l
```

5）以树状显示目录结构

```
tree
```

## 第十一章  网络配置

&emsp;&emsp;修改配置文件指定ip地址

&emsp;&emsp;直接修改配置文件来指定ip，并可以连接到外网，编辑  

vim  /etc/sysconfig/network-scripts/ifcfg-eth0  将ip地址配置成静态的。

```
ifcfg-eth0文件说明

DEVICE=eth0									接口名（设备网卡）
TYPE=Ethernet								网络类型（通常是Ethernet）
UUID=e58067fe-1c6a-469f-aa4e-ebd88e281005	随机ID
ONBOOT=yes									系统启动时网络接口是否有效
NM_CONTROLLED=yes
BOOTPROTO=dhcp								IP的配置方法（none|static|bootp|dhcp）（引导时     不使用协议|静态分配IP|BOOTP协议|DHCP协议）
DEFROUTE=yes								默认网关
#IPADDR=192.168.25.130						IP地址，静态分配IP时使用
#GATEWAY=192.168.25.2						网关，静态分配IP时使用
#DNS1=192.168.25.1							域名解析器，静态分配IP时使用
IPV4_FAILURE_FATAL=yes
NAME="System eth0"							名称
HWADDR=00:0C:29:A2:A3:3B					MAC地址
PEERDNS=yes
PEERROUTES=yes

修改完文件后，需要重启网络服务或者重启才生效
	service  network  restart
	reboot
```

```
查看IP方法
	ifconfig
	ip addr
	
查看网关
	netstat  -rn
	
查看DNS
	cat  /etc/resolv.conf
```

## 第十二章  进程管理

### 12.1  进程的基本介绍

&emsp;&emsp;1）在Linux中，每个执行的程序都成为一个进程。每个进程都分配一个ID号。  

&emsp;&emsp;2）每一个进程，都会对应一个父进程，而这个父进程可以复制多个子进程。例如www服务器。  

&emsp;&emsp;3）每个进程都可能以两种方式存在的。前台与后台，所谓前台进程就是用户目前屏幕上可以进行操作的。  

后台进程则是实际在操作，但由于屏幕上无法看到的进程，通常使用后台方式执行。  

&emsp;&emsp;4）一般系统的服务都是以后台进程的方式存在，而且都会常驻在系统中，直到关机才结束。

### 12.2  显示系统执行的进程

```
UNIX风格：
ps
	-a  显示当前终端的所有进程信息
	-u	以用户的格式显示进程信息
	-x	显示后台进程运行的参数
	
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1   2900  1432 ?        Ss   13:28   0:01 /sbin/init
root         2  0.0  0.0      0     0 ?        S    13:28   0:00 [kthreadd]

	USER		用户名
	PID			进程ID
	PPID		父进程ID
	%CPU		占用的CPU
	%MEM		占用的内存
	VSZ			使用的虚拟内存
	RSS			使用物理内存情况
	TTY			使用的终端
	STAT		进程的状态，其中S-睡眠，s-表示该进程是会话的先到晋城，N-表示进程拥有比普通有限集更低的由下级，R-正在运行，D-短期等待，Z-僵死进程，T-被跟踪或者被停止等等
	START		启动时间
	TIME		占用CPU的总时间
	COMMAND		进程执行时的命令行
```

```
BSD风格
ps
	-e  显示所有进程
	-f	全格式
	
	UID			用户ID（User ID）
	PID 		如上
	PPID		父进程的进程ID（Parent Process id）
	C			CPU用于计算执行优先级的因子。数值越大，表明进程是CPU密集型运算，执行优先级会降低；数字越小，表明晋城市I/O密集型运算，执行优先级会提高
	STIME		进程开始时间
	TTY			使用的终端
	TIME		启动时间
	CMD			进程执行时的命令行
```

```
查看进程树pstree
pstree  [选项]
	-p  显示进程的PID
	-u	显示进程的所属用户
```

### 12.3  终止进程

```
kill  [选项]  进程号 （功能描述：通过进程号杀死进程）
	-9  表示强迫进程立即停止
killall  进程名称 （功能描述：通过进程名杀死进程，也支持通配符，这在系统因附在过大而变的很慢时很有用）
```

### 12.4  服务管理

&emsp;&emsp;服务（service）本质就是进程，但是运行在后台的，通常都会监听某个端口，等待其它程序的请求，比  

如（mysql、sshd、防火墙等），因此我们又称为守护进程，是Linux中非常重要的点。

```
service  服务名  [start|stop|restart|reload|status]
在CentOS7.0以后不再使用service，而是systemctl
```

应用实例：  

1）查看当前防火墙的状态，关闭防火墙和重启防火墙  

```
service  iptables  status
service  iptables  stop
service  iptables  start
```

细节讨论：  

1）关闭或者启用防火墙后，立即生效。在windows下 telnet ip 端口 即可验证  

2）这种方式只是临时生效，当重启系统后，还是回归以前对服务的设置  

3）如果希望某个服务自启动或者永久关闭生效，要使用chkconfig

### 12.5  查看服务名

&emsp;&emsp;1）使用 setup  ->  系统服务  就可以看到  

&emsp;&emsp;2）查看 ls  -l  /etc/init.d/  可以看到

### 12.6  chkconfig指令

&emsp;&emsp;通过chkconfig指令可以给每个服务的各个运行级别设置自启动/关闭

```
1）查看服务
	chkconfig  --list | grep xxx
2）  chkconfig  服务名  --list
3）  chkconfig  --level  5（某个运行级别） 服务名  on/off
```

应用实例：  

1）请显示当前系统所有服务的各个运行级别的运行状态  

&emsp;&emsp;chkconfig  --list  

2）请查看sshd服务的运行状态  

&emsp;&emsp;service  sshd  status  

3）将sshd服务在运行级别5下设置为不自动启动  

&emsp;&emsp;chkconfig  --level  5  sshd  off  

4）当运行级别为5时，关闭防火墙  

&emsp;&emsp;chkconfig  --level  5  iptables  on  

5）在所有运行级别下，关闭防火墙  

&emsp;&emsp;chkconfig  iptables  off  

6）在所有运行级别下，开启防火墙  

&emsp;&emsp;chkconfig  iptables  on  

注意：chkconfig重新设置服务后自启动或关闭，需要重启机器reboot才能生效

```mermaid
graph LR
G[开机流程说明]
A[开机] --> B[BIOS] 
	 B --> C[boot]
	 C --> D[init进程]
	 D --> E[运行级别]
	 E --> F[运行级对应的服务]
	 
```

### 12.7  动态监控进程

&emsp;&emsp;top命令与ps很相似。他们都是用来显示正在执行的进程。top与ps最大的不同之处，在于top在执行一段时  

间可以更新正在运行的进程。

```
top
	-d		指定top指令每隔几秒更新，默认是3秒，在top命令的交互模式当中可以执行的命令
	-i		使top不显示任何闲置或者僵死进程
	-p		通过指定监控进程ID来仅仅监控某个进程的状态
	
交互操作说明
	p		以CPU使用率排序，默认就是此项
	M		以内存的使用率排序
	N		以PID排序
	q		退出top
	
top			当前时间
up			系统运行时间
users		当前登录系统用户数
load average负载均衡
Tasks		任务总数
cpu(s)		cpu使用情况
	us		用户已用cpu
	sy		系统已用cpu
	ni
	id		空闲cpu
Mem			内存使用情况
	total	总量
	userd	已用
	free	空闲
	buffers	缓冲
Swap		内存不够用才会使用swap替代内存
```

应用实例：  

1）监视特定用户  

&emsp;&emsp;top，然后输入"u"，输入用户名再回车即可。  

2）终止指定的程序  

&emsp;&emsp;top，然后输入"k"，输入PID再回车即可。  

3）指定系统状态的更新时间（每隔10秒自动更新）  

&emsp;&emsp;top  -d  10

### 12.8  监控网络状态

&emsp;&emsp;可用于查询开放的端口与外部的端口的连接情况。

```
netstat  [选项]
	-an		按一定顺序排列输出
	-p		显示哪个进程在调用
```

## 第十三章 RPM与YUM

### 13.1  RPM包的管理

&emsp;&emsp;一种用于互联网下载包的打包及安装工具，它包含在某些Linux分发版中。它生成具有.PRM扩展名的文件。  

RPM是RedHat  Package Manager（RedHat软件包管理工具）缩写，类似windows的setup.exe，这一文件格  

式名称虽然打上了RedHat的标志，但理念是通用的。

&emsp;&emsp;Linux的分发版本都有采用（suse，redhat，centos等等），可以算是公认的行业标准。

**rpm包的简单查询指令**

1、查询已安装的rpm列表

```
rpm  -qa | grep  xxx
```

2、查询安装包的详细信息

```
rpm  -qi  xxx
```

3、查询软件的安装的文件和路径

```
rpm  -ql  xxx
```

4、查询某个文件属于哪个软件包

```
rpm  -qf  /xxx/xxx
```

**rpm的卸载**

```
rpm  -e  rpm包名称
```

**rpm包的安装**

```
rpm  -ivh  rpm安装包的路径
	i=install  安装
	v=verbose  提示
	h=hash     进度条
```

### 13.2  YUM

&emsp;&emsp;Yum是一个Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以  

自动处理依赖性关系，并且一次安装所有依赖的软件包。

**yum的查看**

```shell
yum  list | grep  xxx
```

**yum的安装**

```shell
yum  install  xxx
```

## 第十四章 Shell编程

### 14.1  Shell

&emsp;&emsp;Shell是一个命令行解释器，它为用户提供了一个向Linux内核发送请求以便运行程序的界面系统级程序，用户  

可以用Shell来启动、停止甚至编写一些程序。

&emsp;&emsp;脚本以 #!/bin/bash 开头，表示以bash来运行该脚本。

### 14.2  Shell的变量

**Shell变量的介绍**

1）Linux Shell中的变量分为系统变量和用户自定义变量。  

2）系统变量：$HOME、$PWD、$SHELL、$USER等等。  

3）显示当前shell中所有变量：set  

**Shell变量的定义**

1）定义变量：变量 = 值

2）撤销变量：unset  变量  

3）声明静态变量：readonly变量，注意：不能 unset  

**应用实例：**

1）定义变量A，撤销变量A  

```shell
#!/bin/bash

A=100
echo "A=$A"
unset A
echo "A=$A"
```

2）声明一个静态变量，不能unset

```shell
#!/bin/bash

readonly  A=100
```

3）可以把变量提升为全局环境变量，可供其他Shell程序使用

**定义变量的规则**

1）变量名称可以有字母、数字和下划线组成，但是不能以数字开头。  

2）等号两侧不能有空格。  

3）变量名称一般习惯为大写。

**将命令的返回值赋给变量（重点）**

1）A=\`ls  -la\` 反引号，运行里面的命令，并把结果返回给变量A。  

2）A=$(ls  -la) 等价于反引号。

### 14.3  设置环境变量

**基本语法**

1）export  变量名=变量值						（功能描述：将shell变量输出为环境变量）  

2）source  配置文件								  （功能描述：让修改后的配置信息立即生效）  

3）echo  $变量名									   （功能那个描述：查询环境变量的值）  

**快速入门**

1）在/etc/prefile文件中定义JAVA_HOME环境变量  

```shell
JAVA_HOME=/usr/java/jdk1.8.0_11
export JAVA_HOME 
```

2）查看环境变量JAVA_HOME的值  

```shell
echo  $JAVA_HOME
```

3）在另外一个shell程序中使用JAVA_HOME  

```shell
echo  "java_home=$JAVA_HOME"
```

**注意：**在输出JAVA_HOME环境变量前，需要让其生效  

```shell
source  /etc/profile
```

**多行注释**

```shell
:<<!
  xxx
!
```

### 14.4  位置参数变量

**介绍**

&emsp;&emsp;当我们执行一个shell脚本时，如果希望获取到命令行的参数信息，就可以使用到位置参数变量。  

&emsp;&emsp;比如：./myshell.sh  100  200，这个就是一个执行shell的命令行，可以再myshell脚本中获取到参数信息。  

**基本语法**

&emsp;&emsp;$n	（功能描述：n位数字，$0代表命令本身，$1-$9代表第一到第九个参数，十以上的参数需要用大括号  

包含，如${10} ）  

&emsp;&emsp;$*	（功能描述：这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体）  

&emsp;&emsp;$@	（功能描述：这个变量也代表命令行中所有的参数，不过$@把每个参数区分对待）  

&emsp;&emsp;$#	（功能描述：这个变量代表命令行中所有参数的个数）  

**应用实例：**

1）编写一个shell脚本 positionPara.sh，在脚本中获取到命令行的各个参数信息  

```shell
#!/bin/bash

#获取到各个参数
echo "$0 $1 $2"
echo "$*"
echo "$@"
echo "$#"
```

### 14.5  预定义变量

**介绍**

&emsp;&emsp;就是shell设计者实现已经定义好的变量，可以直接在shell脚本中使用  

**基本语法**

&emsp;&emsp;$$&emsp;&emsp;（功能描述：当前进程的进程号PID）  

&emsp;&emsp;$!&emsp;&emsp;（功能描述：后台运行的最后一个进程的进程号PID）  

&emsp;&emsp;$?&emsp;&emsp;(功能描述：最后一次执行的命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；如  

  果这个命令的值非0（具体哪个数，由命令自己决定)，则证明上一个命令执行不正确）  

**应用实例**

1）在一个shell脚本中简单实用一下预定义变量  

```shell
#!/bin/bash

echo "当前的进程号=$$"
echo "最后的进程号=$!"
echo "执行的值=$?"
```

### 14.6  运算符

**基本语法**  

1）"$((运算式))" 或 "$[运算式]"  

2）expr  m + n  

&emsp;&emsp;注意expr运算符间要有空格  

3）expr  m - n  

4）expr  \*，/，%    乘，除，取余  

**应用实例：**  

1）计算 (2+3)*4 的值  

```shell
#!/bin/bash

RESULT=$(((2+3)*4))
echo "result1=$RESULT"

RESULT2=$[(2+3)*4]
echo "result2=$RESULT2"

TEMP=`expr 2 + 3`
RESULT3=`expr $TEMP \* 4`
echo "result3=$RESULT3"
```

2）请求出命令行的两个参数的和

```shell
#!/bin/bash

RESULT=$[$1+$2]
echo "result=$RESULT"
```

### 14.7  条件判断

**基本语法**  

[ condition ]		（注意condition前后要有空格）  

非空返回true，可使用 $? 验证（0为true，>1 为false）

**应用实例：**

**判断语句**  

**1）两个整数判断  **

=  字符串比较  

-lt  小于  

-le  小于等于  

-eq  等于  

-gt  大于  

-ge  大于等于  

-ne  不等于  

**2）按照文件权限进行判断  **

-r  有读的权限  

-w  有写的权限  

-x  有执行的权限  

**3）按照文件类型进行判断 ** 

-f  文件存在并且是一个常规的文件  

-e  文件存在  

-d  文件存在并且是一个目录  

**应用实例：**  

1）"ok"是否等于"ok"  

```shell
#!/bin/bash

if [ "ok" = "ok" ]
then
        echo "equal"
fi
```

2）23是否大于等于22  

```shell
#!/bin/bash

if [ 23 -gt 22 ]
then
        echo "大于"
fi
```

3）/root/install.log 目录中的文件是否存在  

```shell
#!/bin/bash

if [ -e /opt/redis.conf ]
then
        echo "文件存在"
fi
```

### 14.8  流程控制

#### 14.8.1  if判断

**基本语法**  

```shell
if  [ 条件判断式 ]
	then  
		程序  
fi 

或者  

if [ 条件判断式 ]  
	then  
		程序  
	elif [ 条件判断式 ]  
		then  
			程序  
fi 
```

**应用实例：**  

1）请编写一个shell程序，如果输入的参数大于等于60，则输出“及格了”，如果小于60，则输出不及格

```
#!/bin/bash

if [ $1 -ge 60 ]
	then
        echo "及格了"
    elif [ $1 -lt 60 ]
        then 
        	echo "不及格"
fi
```

#### 14.8.2  case语句

**基本语法**    

case  $变量名  in  

"值1")  

如果变量的值等于值1，则执行程序1

;;  

"值2")  

如果变量的值等于值2，则执行程序2  

;;

...  

*)  

如果变量的值都不是以上的值，则执行此程序  

;;  

esac

**应用实例**  

1）当命令行参数是1是，输出“周一”，是2时，就输出“周二”，其他情况输出“other”  

```shell
#!/bin/bash

case $1 in
"1")
        echo "周一"
;;
"2")
        echo "周二"
;;
*)
        echo "other"
;;
esac
```

#### 14.8.3  for循环

**基本语法1：**  

for  变量   in  值1  值2  值3...  

do  

​		程序  

done

**基本语法2：**  

for  ((初始值;  循环控制条件;  变量变化))  

do  

​		程序  

done  

**应用案例：**  

1）打印命令行输入的参数  

```shell
#!/bin/bash

#加引号会整体处理，不加引号会分开遍历处理
for i in "$*"
do
        echo "the num is $i"
done
#测试结果
#the num is 10 20 30    加引号

#the num is 10			不加引号
#the num is 20
#the num is 30


#分开遍历处理，遍历应该用这个
for j in "$@"
do
        echo "the num is $j"
done
#测试结果
#the num is 10
#the num is 20
#the num is 30
```

2）从1加到100的值输出显示

```
#!/bin/bash

SUM=0
for ((i=1; i<=100; i++))
do
        SUM=$[$SUM+$i]
done
echo "sum=$SUM"
```

#### 14.8.4  while循环

**基本语法1（注意中括号前后要有空格）：**  

while  [ 条件判断式 ]  

do  

​		程序  

done  

**应用实例：**  

1）从命令行输入一个数n，统计从1+...+n的值是多少  

```shell
#!/bin/bash

SUM=0
i=1
while [ $i -le $1 ]
do
        SUM=$[$SUM+$i]
        i=$[$i+1]
done
echo "SUM=$SUM"
```

#### 14.8.5  read读取控制台输入

**基本语法：**  

read  [选项]  [参数]  

&emsp;&emsp;-p&emsp;&emsp;指定读取值时的提示符

&emsp;&emsp;-t&emsp;&emsp;指定读取值时等待的时间（秒），如果没有在指定的时间内输入，就不再等待了

&emsp;&emsp;参数&emsp;&emsp;指定读取值得变量名  

**应用实例：**  

1）读取控制台输入一个num值  

```shell
#!/bin/bash

read -p "请输入一个数字num=" NUM
echo "你输入的值是num=$NUM"
```

2）读取控制台输入一个num值，在10秒内输入  

```shell
#!/bin/bash

read -t 10 -p "请输入一个数字num=" NUM
echo "你输入的值是num=$NUM"
```

### 14.9  函数

#### 14.9.1  系统函数

**1、basename基本语法：**  

功能：返回完整路径最后/的部分，常用于获取文件名，如果指定了suffix后缀，则会将文件名的后缀也去掉   

basename  [pathname]  [suffix]   

**应用案例：**

1）请返回  /opt/shell/caseTest.sh 的 “caseTest.sh” 部分  

```shell
basename /opt/shell/caseTest.sh
#输出  caseTest.sh

basename /opt/shell/caseTest.sh .sh
#输出  caseTest
```

**2、dirname基本语法：**  

功能：返回完整路径最后/的前面部分

dirname  [pathname]  

**应用案例：**

1）请返回  /opt/shell/caseTest.sh 的 /opt/shell  

```shell
dirname   /opt/shell/caseTest.sh
#输出  /opt/shell
```

#### 14.9.2  自定义函数  

**基本语法：**  

[ function ] functionName [()]

{

​				Action;

​				[return int;]

}  

调用时直接写函数名：functionName  

**应用实例：**

1）计算输入两个参数的和  

```shell
#!/bin/bash

function getSum(){

        SUM=$[$n1+$n2]
        echo "和是=$SUM"
}

read -p "请输入第一个数n1=" n1
read -p "请输入第二个数n2=" n2

#调用函数
getSum $n1 $n2
```

### 14.10  Shell编程综合案例

**需求分析：**  

1）每天凌晨 2:10 备份数据库xianCanDB到 /data/backup/db  

2）备份开始和备份结束能够给出相应的提示信息  

3）备份后的文件要求以备份时间为文件名，并打包成 .tar.gz  的形式，比如：2020-3-2 233000.tar.gz  

4）在备份的同时，检查是否有10天前备份的数据库文件，如果有就将其删除  

```shell
#!/bin/bash

#备份的路径
BACKUP=/data/backup/db
#当前的时间作为文件名
DATETIME=$(date +%Y_%m_%d_%H%M%S)

echo "$DATETIME"
echo "========开始备份========"
echo "========备份的路径是 $BACKUP/$DATETIME.tar.gz"

#主机
HOST=localhost
#用户名
DB_USER=root
#密码
DB_PWD=root
#创建备份的数据库
DATABASE=xianCanDB

#创建备份的路径
#如果备份的路径文件夹存在，就使用，否则就创建
[ ! -d "$BACKUP/$DATETIME" ] && mkdir -p "$BACKUP/$DATETIME"

#执行mysql的备份数据库的指令
mysqldump -u$DB_USER -p$DB_PWD --host=$HOST $DATABASE | gzip > $BACKUP/$DATETIME/$DATETIME.sql.gz
#打包备份文件
cd $BACKUP
tar -zcvf $DATETIME.tar.gz $DATETIME
#删除临时目录
rm -rf $BACKUP/$DATETIME

#删除10天前的备份文件
find $BACKUP -mtime +10 -name "*.tar.gz" -exec rm -rf {} \;
echo "========执行备份成功========"
```

```shell
#在crontab中的配置
10 2 * * * /usr/sbin/mysql_db_backup.sh
```

