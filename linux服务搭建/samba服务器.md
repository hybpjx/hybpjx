# samba服务器

- samba介绍
- 应用场景
- samba安装部署
- samba配置文件详解
- samba文件共享
- samba共享访问

------

## 一、samba介绍

在日常办公环境中，操作系统除了windows以外，还有linux或者UNIX。windows和linux或UNIX之间共享文件是无法直接完成的，为了解析不同系统之间的文件和打印机等资源的共享，我们今天来介绍一下samba服务。他可以解决不同系统平台之间的共享问题。

Samba是在Linux和UNIX系统上实现SMB协议的一个免费软件，由服务器及客户端程序构成，也是一个C/S软件。



```undefined
 SMB（Server Messages Block，信息服务块）是一种在局域网上共享文件和打印机的一种通信协议，它为局域网内的不同计算机之间提供文件及打
 印机等资源的共享服务。SMB协议是客户机/服务器型协议，客户机通过该协议可以访问服务器上的共享文件系统、打印机及其他
```

## 二、应用场景

局域网内的不同计算机之间提供文件及打印机等资源的共享服务

## 三、samba安装部署

**约定：本实验中使用过的机器为centos7.5_x86_64系统，计算机名称:baism.ayitula.com,IP地址192.168.11.16/24.请关闭防火墙和SELINUX。**



```csharp
a、安装软件包
[root@baism ~]# yum -y install samba samba-client
samba: 服务端
samba-client：客户端 提供了smbclient命令

b、设置服务开机启动
[root@baism ~]# systemctl enable nmb smb
Created symlink from /etc/systemd/system/multi-user.target.wants/nmb.service to /usr/lib/systemd/system/nmb.service.
Created symlink from /etc/systemd/system/multi-user.target.wants/smb.service to /usr/lib/systemd/system/smb.service.

c、开启服务
[root@baism ~]# systemctl start nmb smb
```

## 四、samba配置文件详解

主配文件路径: /etc/samba/smb.conf



```php
[root@baism ~]# cat smb.conf.example 
# This is the main Samba configuration file. For detailed information about the
# options listed here, refer to the smb.conf(5) manual page. Samba has a huge
# number of configurable options, most of which are not shown in this example.
#
# The Samba Wiki contains a lot of step-by-step guides installing, configuring,
# and using Samba:
# https://wiki.samba.org/index.php/User_Documentation
#
# In this file, lines starting with a semicolon (;) or a hash (#) are
# comments and are ignored. This file uses hashes to denote commentary and
# semicolons for parts of the file you may wish to configure.
#
# NOTE: Run the "testparm" command after modifying this file to check for basic
# syntax errors.
#
#---------------
#
#SAMBA selinux相关设置，如果你开启了selinux，请注意下面的说明
#
#
#Security-Enhanced Linux (SELinux) Notes:
#
# Turn the samba_domain_controller Boolean on to allow a Samba PDC to use the
# useradd and groupadd family of binaries. Run the following command as the
# root user to turn this Boolean on:
# 如果你在域环境中使用samba那么请设置下面的bool值
# setsebool -P samba_domain_controller on
#
# Turn the samba_enable_home_dirs Boolean on if you want to share home
# directories via Samba. Run the following command as the root user to turn this
# Boolean on:
#
# 假如希望通过samba共享用户家目录请设置下面的bool值
# setsebool -P samba_enable_home_dirs on
#
# If you create a new directory, such as a new top-level directory, label it
# with samba_share_t so that SELinux allows Samba to read and write to it. Do
# not label system directories, such as /etc/ and /home/, with samba_share_t, as
# such directories should already have an SELinux label.
#
#加入你想将目录通过samba共享，请确认其目录标签为sambe_share_t
# Run the "ls -ldZ /path/to/directory" command to view the current SELinux
# label for a given directory.
#
# Set SELinux labels only on files and directories you have created. Use the
# chcon command to temporarily change a label:
# 标签设置方法
# chcon -t samba_share_t /path/to/directory
#
# Changes made via chcon are lost when the file system is relabeled or commands
# such as restorecon are run.
#
# Use the samba_export_all_ro or samba_export_all_rw Boolean to share system
# directories. To share such directories and only allow read-only permissions:
# 对共享目录的权限的bool设置，只读或读写
# setsebool -P samba_export_all_ro on
# To share such directories and allow read and write permissions:
# setsebool -P samba_export_all_rw on
#
# To run scripts (preexec/root prexec/print command/...), copy them to the
# /var/lib/samba/scripts/ directory so that SELinux will allow smbd to run them.
# Note that if you move the scripts to /var/lib/samba/scripts/, they retain
# their existing SELinux labels, which may be labels that SELinux does not allow
# smbd to run. Copying the scripts will result in the correct SELinux labels.
# Run the "restorecon -R -v /var/lib/samba/scripts" command as the root user to
# apply the correct SELinux labels to these files.
#
#--------------
#
#======================= Global Settings =====================================
#全局设置，对整个服务都生效
[global]

#网络设置
# ----------------------- Network-Related Options -------------------------
#
# workgroup = the Windows NT domain name or workgroup name, for example, MYGROUP.
#
# server string = the equivalent of the Windows NT Description field.
#
# netbios name = used to specify a server name that is not tied to the hostname,
#                maximum is 15 characters.
#
# interfaces = used to configure Samba to listen on multiple network interfaces.
# If you have multiple interfaces, you can use the "interfaces =" option to
# configure which of those interfaces Samba listens on. Never omit the localhost
# interface (lo).
#
# hosts allow = the hosts allowed to connect. This option can also be used on a
# per-share basis.
#
# hosts deny = the hosts not allowed to connect. This option can also be used on
# a per-share basis.
#
#定义计算机的工作组,如果希望和windows共享，可以设置为workgroup，这样就可以在windows的网上邻居中找到linux计算机
    workgroup = MYGROUP
#对samba服务器的描述信息
    server string = Samba Server Version %v
#设置netbios计算机名称
;    netbios name = MYSERVER

#samba使用本机的那块网卡
;    interfaces = lo eth0 192.168.12.2/24 192.168.13.2/24
#允许那个网段访问samba服务器共享
;    hosts allow = 127\. 192.168.12\. 192.168.13.

#
#日志选项
# --------------------------- Logging Options -----------------------------
#
# log file = specify where log files are written to and how they are split.
#
# max log size = specify the maximum size log files are allowed to reach. Log
# files are rotated when they reach the size specified with "max log size".
#
    #samba日志文件路径
    # log files split per-machine:
    log file = /var/log/samba/log.%m
    #日志文件大小，0为不限制，注意不建议这样设置
    # maximum size of 50KB per log file, then rotate:
    max log size = 50

#独立服务选项
# ----------------------- Standalone Server Options ------------------------
#
# security = the mode Samba runs in. This can be set to user, share
# (deprecated), or server (deprecated).
#
# passdb backend = the backend used to store user information in. New
# installations should use either tdbsam or ldapsam. No additional configuration
# is required for tdbsam. The "smbpasswd" utility is available for backwards
# compatibility.
#
#samba安全级别
#share:  不需要账号密码，公开共享
#user:   需要提供sam账号密码才能访问共享，私密共享
#server：依靠其他Windows NT/2000或Samba Server来验证用户的账号和密码,是一种代理验证。此种安全模式下,系统管理员可以把所有的Windows用户和口令集中到一个NT系统上,>使用Windows NT进行Samba认证, 远程服务器可以自动认证全部用户和口令,如果认证失败,Samba将使用用户级安全模式作为替代的方式。
#domain：域安全级别,使用主域控制器(PDC)来完成认证。
#
#一般情况下我们使用share和user的比较多，除非公司有完整的域环境
    security = user

#该方式则是使用一个数据库文件来建立用户数据库。数据库文件叫passdb.tdb，默认在/etc/samba目录下。passdb.tdb 用户数据库可以使用smbpasswd –a来建立Samba用户，不过要建立的Samba用户必须先是系统用户。我们也可以使用pdbedit命令来建立Samba账户并由其pdbedit管理。
    passdb backend = tdbsam

#域成员选项
# ----------------------- Domain Members Options ------------------------
#
# security = must be set to domain or ads.
#
# passdb backend = the backend used to store user information in. New
# installations should use either tdbsam or ldapsam. No additional configuration
# is required for tdbsam. The "smbpasswd" utility is available for backwards
# compatibility.
#
# realm = only use the realm option when the "security = ads" option is set.
# The realm option specifies the Active Directory realm the host is a part of.
#
# password server = only use this option when the "security = server"
# option is set, or if you cannot use DNS to locate a Domain Controller. The
# argument list can include My_PDC_Name, [My_BDC_Name], and [My_Next_BDC_Name]:
#
# password server = My_PDC_Name [My_BDC_Name] [My_Next_BDC_Name]
#
# Use "password server = *" to automatically locate Domain Controllers.
#设置域共享
;    security = domain
;    passdb backend = tdbsam
#定义域名称
;    realm = MY_REALM
#域验证服务器
;    password server = <NT-Server-Name>

#域控选项
# ----------------------- Domain Controller Options ------------------------
#
# security = must be set to user for domain controllers.
#
# passdb backend = the backend used to store user information in. New
# installations should use either tdbsam or ldapsam. No additional configuration
# is required for tdbsam. The "smbpasswd" utility is available for backwards
# compatibility.
#
# domain master = specifies Samba to be the Domain Master Browser, allowing
# Samba to collate browse lists between subnets. Do not use the "domain master"
# option if you already have a Windows NT domain controller performing this task.
#
# domain logons = allows Samba to provide a network logon service for Windows
# workstations.
#
# logon script = specifies a script to run at login time on the client. These
# scripts must be provided in a share named NETLOGON.
#
# logon path = specifies (with a UNC path) where user profiles are stored.
#
#
;    security = user
;    passdb backend = tdbsam

;    domain master = yes
;    domain logons = yes

    # the following login script name is determined by the machine name
    # (%m):
;    logon script = %m.bat
    # the following login script name is determined by the UNIX user used:
;    logon script = %u.bat
;    logon path = \\%L\Profiles\%u
    # use an empty path to disable profile support:
;    logon path =

    # various scripts can be used on a domain controller or a stand-alone
    # machine to add or delete corresponding UNIX accounts:

;    add user script = /usr/sbin/useradd "%u" -n -g users
;    add group script = /usr/sbin/groupadd "%g"
;    add machine script = /usr/sbin/useradd -n -c "Workstation (%u)" -M -d /nohome -s /bin/false "%u"
;    delete user script = /usr/sbin/userdel "%u"
;    delete user from group script = /usr/sbin/userdel "%u" "%g"
;    delete group script = /usr/sbin/groupdel "%g"

#这些设置选项主要用于SMB网络中进行浏览时，设置samba服务器的行为。缺省情况不让 samba服务器参加broswser的推举过程，为了使得samba服务器能成为browser，就需要设定local master =yes。然后samba服务就可以根据os level设置的权重进行推举，缺省的os level为0，这个权重不会赢得推举。但可以取消注释，将os level设置为33，这将在与所有Windows计算机（包括Windows NT）的推举竞赛中获得胜利，因为NT server的权重为32。设置比33更高的权重，只是在不同的samba 服务器之间进行选择时才有意义。
#
# preferred master  可以设置自己优先成为浏览服务器候选人
#
# ----------------------- Browser Control Options ----------------------------
#
# local master = when set to no, Samba does not become the master browser on
# your network. When set to yes, normal election rules apply.
#
# os level = determines the precedence the server has in master browser
# elections. The default value should be reasonable.
#
# preferred master = when set to yes, Samba forces a local browser election at
# start up (and gives itself a slightly higher chance of winning the election).
#
;    local master = no
;    os level = 33
;    preferred master = yes

#
#
#wins服务，如果网络中配置了wins服务器可以在此设置wins相关项
#----------------------------- Name Resolution -------------------------------
#
# This section details the support for the Windows Internet Name Service (WINS).
#
# Note: Samba can be either a WINS server or a WINS client, but not both.
#
# wins support = when set to yes, the NMBD component of Samba enables its WINS
# server.
#
# wins server = tells the NMBD component of Samba to be a WINS client.
#
# wins proxy = when set to yes, Samba answers name resolution queries on behalf
# of a non WINS capable client. For this to work, there must be at least one
# WINS server on the network. The default is no.
#
# dns proxy = when set to yes, Samba attempts to resolve NetBIOS names via DNS
# nslookups.
#设置nmb进程支持wins服务
;    wins support = yes
#设置wins服务器ip
;    wins server = w.x.y.z
#设置wins代理IP
;    wins proxy = yes
#设置Samba服务器是否在无法联系WINS服务器时通过DNS去解析主机的NetBIOS名
;    dns proxy = yes

#该部分包括Samba服务器打印机相关设置
# --------------------------- Printing Options -----------------------------
#
# The options in this section allow you to configure a non-default printing
# system.
#
# load printers = when set you yes, the list of printers is automatically
# loaded, rather than setting them up individually.
#
# cups options = allows you to pass options to the CUPS library. Setting this
# option to raw, for example, allows you to use drivers on your Windows clients.
#
# printcap name = used to specify an alternative printcap file.
#
#是否启用共享打印机
    load printers = yes
    cups options = raw
#打印机配置文件
;    printcap name = /etc/printcap
    # obtain a list of printers automatically on UNIX System V systems:
;    printcap name = lpstat
#打印机的系统类型,现在支持的打印系统有：bsd, sysv, plp, lprng, aix, hpux, qnx,cups
;    printing = cups

#该部分包括Samba服务器如何保留从Windows客户端复制或移动到Samba服务器共享目录文件的Windows文件属性的相关配置.
# --------------------------- File System Options ---------------------------
#
# The options in this section can be un-commented if the file system supports
# extended attributes, and those attributes are enabled (usually via the
# "user_xattr" mount option). These options allow the administrator to specify
# that DOS attributes are stored in extended attributes and also make sure that
# Samba does not change the permission bits.
#
# Note: These options can be used on a per-share basis. Setting them globally
# (in the [global] section) makes them the default for all shares.

#当Windows客户端将文件复制或移动到Samba服务器共享目录时，是否保留文件在Windows中的存档属性。默认no。
;    map archive = no
#当Windows客户端将文件复制或移动到Samba服务器共享目录时，是否保留文件在Windows中的隐藏属性。默认no。
;    map hidden = no
#当Windows客户端将文件复制或移动到Samba服务器共享目录时，是否保留文件在Windows中的只读属性。默认为no。
;    map read only = no
#当Windows客户端将文件复制或移动到Samba服务器共享目录时，是否保留文件在Windows中的系统文件属性。默认为no。
;    map system = no
#当Windows客户端将文件复制或移动到Samba服务器共享目录时，是否保留文件在Windows中的相关属性（只读、系统、隐藏、存档属性）。默认为yes。
;    store dos attributes = yes

#共享设置
#============================ Share Definitions ==============================
#用户家目录共享
#共享名称
[homes]
#描述
    comment = Home Directories
#是否支持浏览
    browseable = no
#是否允许写入
    writable = yes
#允许访问该共享资源的smb用户，@组
;    valid users = %S
;    valid users = MYDOMAIN\%S

#打印机共享
[printers]
#描述
    comment = All Printers
#路径
    path = /var/spool/samba
#是否可浏览，no类似隐藏共享
    browseable = no
#是否支持guest访问，和public指令类似    
    guest ok = no
#是否可写
    writable = no
#是否允许打印
    printable = yes

# Un-comment the following and create the netlogon directory for Domain Logons:
;    [netlogon]
;    comment = Network Logon Service
;    path = /var/lib/samba/netlogon
;    guest ok = yes
;    writable = no
;    share modes = no

# Un-comment the following to provide a specific roaming profile share.
# The default is to use the user's home directory:
;    [Profiles]
;    path = /var/lib/samba/profiles
;    browseable = no
;    guest ok = yes

# A publicly accessible directory that is read only, except for users in the
# "staff" group (which have write permissions):
;    [public]
;    comment = Public Stuff
;    path = /home/samba
;    public = yes
;    writable = no
;    printable = no
#定义允许哪些smb用户写入
;    write list = +staff
```

## 五、samba文件共享

拓扑图



![img](https:////upload-images.jianshu.io/upload_images/18562821-fd8165d5be339e71.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

[image](https://links.jianshu.com/go?to=http%3A%2F%2Fbook.ayitula.com%2Fassets%2Flinuxc%3As%E6%9C%8D%E5%8A%A1%E6%8B%93%E6%89%91.png)



教学案例:

**1)新建文件夹/common**

**2)在server上配置SMB服务**

**3)您的 SMB 服务器必须是 workgroup 工作组的一个成员**

**4)共享 /common 目录 共享名必须为 common**

**5)只有 192.168.11.0网段内的客户端可以访问 common 共享**

**6）common 必须是可以浏览的**

**7）用户 baism 必须能够读取共享中的内容，如果需要的话，验证的密码是 ayitula**

**8）用户 sko 必须能够拥有写权限，如果需要的话，验证的密码是 ayitula**



```php
a、创建共享目录
[root@baism ~]# mkdir /common

b、设置共享目录权限，因为默认权限是755除了管理员外，其他人只能读不能写，本实验中要求sko能写，所以其他人加写权限
[root@baism ~]# chmod 757 /common

[root@baism ~]# cat /etc/samba/smb.conf
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
    #设置工作组为workgroup
    workgroup = workgroup
    security = user

    passdb backend = tdbsam

    printing = cups
    printcap name = cups
    load printers = yes
    cups options = raw

[homes]
    comment = Home Directories
    valid users = %S, %D%w%S
    browseable = No
    read only = No
    inherit acls = Yes

[printers]
    comment = All Printers
    path = /var/tmp
    printable = Yes
    create mask = 0600
    browseable = No

[print$]
    comment = Printer Drivers
    path = /var/lib/samba/drivers
    write list = @printadmin root
    force group = @printadmin
    create mask = 0664
    directory mask = 0775

#共享设置，参考配置文件详解理解以下选项
[common]
    comment = samba file share test
    path = /common
    browseable = Yes
    hosts allow  = 192.168.11.0/255.255.255.0
    valid users = baism,sko
    writable = no
    write list = sko
    public = no 

c、重启服务生效
[root@baism ~]# systemctl restart smb nmb

    配置smb用户
    关于samba用户的补充知识
    由于系统本地用户无法直接登录，必须是samba用户才可以登录
    创建samba的方法
    1) 创建系统用户
    2）将系统用户转为samba用户

    smbpasswd命令介绍
    smbpasswd - 改变用户的SMB口令
    -a 添加用户 #smbpasswd -a ayitula
    -x 删除用户 #smbpasswd -x ayitula
    -d 禁用帐号 #smbpasswd -d ayitula
    -e 取消禁用 #smbpasswd -e ayitula
    -n 清除密码 #smbpasswd -a ayitula

d、创建samba用户
[root@baism ~]# smbpasswd -a baism
New SMB password:
Retype new SMB password:
Added user baism.
[root@baism ~]# smbpasswd -a sko
New SMB password:
Retype new SMB password:
Added user sko.
```

**共享指令拓展**



```cpp
valid users 指定能够进入此资源的特定用户和组
invalid users 指定不能够使用该共享资源的用户和组

read list 指定只能读取该共享资源的用户和组。 

write list 指定能读取和写该共享资源的用户和组。 
admin list 指定能管理该共享资源（包括读写和权限赋予等）的用户和组。 

public 指明该共享资源是否能给游客帐号访问，这个开关有时候也叫guest ok，所以有的配置文件中出现guest ok = yes其实和public = yes是一样的。 
hide dot files 指明是不是像unix那样隐藏以“.”号开头的文件。 

create mode 指明新建立的文件的属性，一般是0755。 
directory mode 指明新建立的目录的属性，一般是0755。 

sync always 指明对该共享资源进行写操作后是否进行同步操作。 
short preserve case 指明不管文件名大小写。 
preserve case 指明保持大小写。 
case sensitive 指明是否对大小写敏感，一般选no,不然可能引起错误。 

mangle case 指明混合大小写。 

default case 指明缺省的文件名是全部大写还是小写。 
force user 强制把建立文件的属主是谁。如果我有一个目录，让来宾用户可以写，那么来宾用户就可以删除，如果我用 force user=王老师 强制建立文件的属主是 王老师 ，同时限制create mask=0755，这样来宾用户就不能删除了
wide links 指明是否允许共享外符号连接，比如共享资源里面有个连接指向非共享资源里面的文件或者目录，如果设置wide links = no将使该连接不可用。 

max connections = n 设定同时连接数是n。 
delete readonly 指明能否删除共享资源里面已经被定义为只读的文件
```

## 六、访问共享

创建好了共享了，如果用户希望访问samba共享，windows用户通过网上邻居或者在运行中输出[\IP\共享名]的方式访问samba共享，也可以通过网络映射的方式将共享挂载的本地。linux或者UNIX用户则可以使用samba提供的客户端smbclient或者通过CIFS协议将共享挂载到本地。

我来给大家介绍一下smbclient吧。



```objectivec
smbclient - 类似FTP操作方式的访问SMB/CIFS服务器资源的客户端
常用命令选项
-L     此选项允许你查看服务器上可以获得的服务资源
-U|--user=username[%password]  这个参数指定程序联接时使用的用户名或者用户名和密码，如果没指定%password，将提示用户输入。
-W|--workgroup=domain   设置用户名的SMB域。这个选项越过了smb.conf配置文件中的默认域。
-N     如果指定了这个选项，就会省略通常的口令提示。当访问无需口令的服务资源时它很有用。
```

linux下访问samba共享

a、客户端信息



```ruby
[root@slave ~]# ifconfig ens33
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.11.116  netmask 255.255.255.0  broadcast 192.168.11.255
        inet6 fe80::20c:29ff:fe8e:ea58  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:8e:ea:58  txqueuelen 1000  (Ethernet)
        RX packets 5166  bytes 3736352 (3.5 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2311  bytes 297960 (290.9 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[root@slave ~]# hostname
slave.ayitula.com
```

b、使用smbclient访问共享，验证第五点教学案例设置



```php
测试用户 baism
[root@slave ~]# smbclient //192.168.11.16/common -U baism%ayitula 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Feb 27 15:27:27 2019
  ..                                 DR        0  Wed Feb 27 14:58:26 2019
  initial-setup-ks.cfg                A     1803  Wed Feb 27 15:27:27 2019

        16558080 blocks of size 1024\. 11816876 blocks available
smb: \> lcd /etc/
smb: \> put passwd              写入失败
NT_STATUS_ACCESS_DENIED opening remote file \passwd 

测试用户sko
[root@slave ~]# smbclient //192.168.11.16/common -U sko%ayitula 
Try "help" to get a list of possible commands.
smb: \> lcd /etc/
smb: \> put passwd
putting file passwd as \passwd (728.2 kb/s) (average 728.2 kb/s)     可以上传
smb: \> exit

samba命令类似于ftp文本界面命令，参考上节。
```

c、linux挂载共享



```csharp
[root@slave ~]# mkdir /mnt/samba_share
[root@slave ~]# mount -o username=sko,password=ayitula -t cifs //192.168.11.16/common /mnt/samba_share
[root@slave ~]# mount
......
//192.168.11.16/common on /mnt/samba_share type cifs (rw,relatime,vers=1.0,cache=strict,username=sko,domain=,uid=0,
noforceuid,gid=0,noforcegid,addr=192.168.11.16,soft,unix,posixpaths,serverino,mapposix,acl,rsize=1048576,wsize=65536,
echo_interval=60,actimeo=1)

[root@slave ~]# ls /mnt/samba_share/
initial-setup-ks.cfg  passwd
```

d、windows访问共享

打开开始菜单如下输入



![img](https:////upload-images.jianshu.io/upload_images/18562821-8c3832526d990a41.png?imageMogr2/auto-orient/strip|imageView2/2/w/407/format/webp)

[image](https://links.jianshu.com/go?to=http%3A%2F%2Fbook.ayitula.com%2Fassets%2Fsamba-mount-1.png)



回车以后出现下图，输入账号密码



![img](https:////upload-images.jianshu.io/upload_images/18562821-a9b11efeb5cb790f.png?imageMogr2/auto-orient/strip|imageView2/2/w/439/format/webp)

[image](https://links.jianshu.com/go?to=http%3A%2F%2Fbook.ayitula.com%2Fassets%2Fsamba-mount-2.png)



点击确定按钮



![img](https:////upload-images.jianshu.io/upload_images/18562821-08bbba5b3991cbeb.png?imageMogr2/auto-orient/strip|imageView2/2/w/871/format/webp)



作者：亮仔_c1b5
链接：http://events.jianshu.io/p/f9bc164d1d7c
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。