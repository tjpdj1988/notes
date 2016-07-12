# Samba 服务

## 安装 samba 服务
在终端中以root权限执行：
  
    apt-get install samba

启动脚本位于 */etc/init.d/* 目录，文件名为 samba。如果修改了 smb.conf 配置文件，需要重启重启 Samba 服务。

    /etc/init.d/samba restart

*/etc/default/samba* 文件中可设置 samba 服务的启动方式是 daemons 还是 inetd，默认的设置是采用daemons 方式的。

## 配置 samba 服务
Samba 服务的配置文件为 smb.conf， 位于 */etc/samba/* 目录下。在 */usr/share/samba/* 目录下也有一个 smb.conf 文件备份，如果你在配置服务器时把 /etc/samba/smb.conf 改乱了，就可以用该文件来恢复到初始状态。

1. 添加samba 访问用户
  
        smbpasswd -a [samba用户名]
    
2. 修改配置文件 */etc/samba/smb.conf*  
找到 usershare allow guests = yes所在位置（在240行左右），在下面添加：

        security = user
        guest ok =yes
        guest account = [samba用户名]
        
 附.设置目录共享及权限说明
> 
======================= Share Definitions =======================  
[share] #共享文件夹名称  
comment = 共享目录说明  
path = /data/temp #要共享目录的绝对位置  
browseable = no #目录是否可见,预设为可见  
writable = yes #目录是否为可写  
read only = no #目录是否为只读  
guest ok = yes #来宾是否可以访问,与"public = yes" 作用相同  
write list =user,@group #可写清单,@后表示某个群组  
valid users = ... #允许访问的使用者清单  
read list = ... #只可读的使用者清单  
invalid users = ... #禁止访问的使用者清单  
admin users = ... #有管理权限使用者清单  
create mask = 0755 #使用者建立档案的权限,预设为0744  

 然后在文件的尾部添加共享的目录信息：
 
        [shared]
        path = /shared
        writeable = yes
        browseable = yes
        
 [shared] 为共享目录名称，其他pc浏览共享时所显示的目录名称，path为共享的目录。
保存修改后的文件，重启服务。

        /etc/init.d/samba restart
        
## 测试 samba 服务
1. 测试samba配置是否正确

        testparm

2. 测试服务是否正常
    
        smbclient -L localhost

在Sharename项下会出现了所配置的共享的目录名称。

## 访问 samba 服务
* Linux

        sudo mount -t cifs -o username=[用户名],password=[密码] //samba服务地址/目录名/ [挂载目录]
    
 例:  

        sudo mount -t cifs -o username=sundy,password=sundypw //192.168.18.155/public/ /public/smb155
        
* Windows  
打开explorer，在地址栏输入:  

        \\samba服务地址
       
 按enter键，输入samba用户名和密码后便可进入共享目录。