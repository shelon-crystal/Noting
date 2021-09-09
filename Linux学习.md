## Linux学习





#### 网络模式



![1605439877832](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1605439877832.png)



##### 1.1 桥接模式

主机ip和虚拟机ip位于同一个网段（子网），如主机192.168.0.20，虚拟机192.168.0.30

**特点：Linux可以和其他系统通信，但是可能会造成ip冲突**



##### 1.2 NAT模式

网络地址转换模式

在主机新建立一个ip（不同于主机Windows系统ip的网段），然后Linux系统ip与该ip在同一个网段中 

**特点：可以通过主机的Windows的ip访问外网，不产生ip冲突**



##### 1.3 仅主机模式



**特点：Linux是一个独立的主机，不能访问外网**



#### 分区选择



引导文件：`/boot`，格式ext4，大小200MB

交换分区（虚拟内存）：`swap`，格式swap，大小2048MB

根目录：`/`，格式ext4，大小剩下的全部可用空间



#### vmtools安装

/opt目录下解压

![1605441825405](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1605441825405.png)



#### 目录结构

特点：所有东西皆是文件，即将所有物理设备、服务等都映射成为文件来管理



##### 4.1 目录详细介绍

| /boot  | 存放核心文件，系统启动相关的文件，如内核、initrd，以及grub（BootLoader）以及一些连接、镜像文件 |
| ------ | ------------------------------------------------------------ |
|        |                                                              |
| /etc   | 所有的系统管理所需要的配置文件和子目录                       |
| /home  | 用户的家目录，每一个用户的家目录通常默认为/home/USERNAME     |
| /root  | 管理员的家目录                                               |
| /lib   | 库文件静态库：单在程序中的库，其他程序不能使用该库文件动态库：在内存中，任何用到该库的程序都可以使用/lib/modules：内核模块文件。类似于Windows里的DLL文件。 |
| /media | 挂载点目录，移动设备（在windows中，插入一张光盘（或者U盘），系统会自动读取光盘，用户可以直接执行，但在linux中，插入光盘后需要在挂载点挂载这个设备之后才可以使用这个设备。） |
| /mnt   | 挂载点目录，系统提供该目录是为了让用户临时挂载额外文件系统，例如可以将外部的存储挂载在/mnt/上，进入该目录就可以查看里面的内容，例如共享文件夹：/mnt/hgfs/文件夹名 |
| /opt   | 可选目录，第三方程序的安装目录，例如MYSQL数据库等等          |
| /proc  | 伪文件系统，内核映射文件                                     |
| /sys   | 伪文件系统，跟硬件设备相关的属性映射文件                     |
| /tmp   | 临时文件，/var/tmp                                           |
| /var   | 可变化的文件，经常发生变化的文件，比如一些日志文件。         |
| /bin   | 可执行文件，用户[命令](https://www.linuxcool.com/)；其中用到的库文件可能在/lib，配置文件可能在/etc |
| /sbin  | 可执行文件，管理[命令](https://www.linuxcool.com/)；其中用到的库文件可能在/lib，配置文件可能在/etc |
| /usr   | 只读文件，shared read-only/usr/local：第三方软件（用户的应用程序和文件，类似于Windows的program files）**与opt一样是给主机安装软件的目录。一般是通过编译源码方式安装的程序。** |





#### Xftp传输



注意使用SFTP协议，才能连通到22号端口

##### 5.1 乱码问题

设置成UTF-8编码

![1605497164955](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1605497164955.png)



#### vi与vim编辑器



##### 6.1 正常模式



##### 6.2 插入模式

按下：i,I,o,O,a,A,r,R等任何一个字母才会进入到编辑模式。

##### 6.3 命令行模式

按下esc

`:wq`：保存并退出

`:q`：没有修改直接退出

`:q!`：强制退出



##### 6.4 常用快捷键



**指令的输入都在vim/vi的正常模式下进行（非insert模式）**

![1606228006864](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1606228006864.png)

1. 拷贝当前行：yy，再p粘贴
2. 删除当前行：dd，前面加数字就是表示操作包括当前行以及往下的行总和
3. 查找单词：/关键字+回车
4. 最末行：shift + g --> G
5. 最顶行：gg
6. 撤销动作：u
7. 定位行数：:set nu 显示行数， 输入数字 xx ，输入shift + g 即可定位





#### 基本命令



##### 7.1 关机/重启

1. shutdown：
   - shutdown -h now ：立即关机
   - shutdown -h 1 ：1分钟后关机
   - shutdown -r now：立即重启
2. halt：关机
3. reboot：重启s
4. sync （synchronized）： 将内存同步到磁盘上，**执行关机/重启前一定要sync保存数据**



####  用户管理

![1608089550735](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1608089550735.png)

##### 8.1 用户创建

useradd

**（1）如果未指定用户组默认加入到与用户同名的用户组内**

（2）通过useradd -d 指定目录 新的用户名，从而指定家目录下的某个目录，**前提：在家目录下没有这个目录。比如：useradd -d /home/dog xh**

（3）给用户指定密码：passwd 用户名

（4）创建用户时指定用户组：useradd -g 用户组 用户名



##### 8.2 用户删除

userdel 用户名

（1）删除用户，保留家目录：del 用户名；一般保留家目录

（2）删除用户以及其用户主目录：userdel -r xq



##### 8.3 查询用户信息

id 用户名

查看当前用户：whoami



##### 8.4 切换用户

su -切换的用户名



高权限切换到低权限不用输入密码

当返回到原来的用户的时，使用exit命令



##### 8.5 用户组

创建：groupadd 组名

删除：groupdel 组名

增加用户时直接加上组：groupadd -g 用户组 用户名

修改：usermod -g 组名 用户名



##### 8.6 用户和组的配置文件



用户配置文件：/etc/passwd

- 每行含义--->用户名：口令：用户标识号：组标识号：注释性描述：主目录：登录Shell

口令配置文件：/etc/shadow

- 每行含义--->登录名：加密口令：最后一次修改时间：最小时间间隔：最大时间间隔：警告时间：失效时间：标志

组配置文件：记录Linux包含组的信息

- 每行含义--->组名：口令：组标识号：组内用户列表



#### 实用指令



##### 9.1 指定运行级别

![1608283003901](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1608283003901.png)

centos7：/lib/systemd/system

命令：init



##### 找回root密码

进入到单用户模式，然后修改root密码

![1608290891329](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1608290891329.png)



##### 9.2 帮助指令

语法1：man [指令或者配置文件]     man ls

语法2：help [指令或配置文件]		 []内为系统的内置指令如：help cd 



##### 9.3 文件目录类



###### 9.3.1pwd

pwd 显示当前工作目录的绝对路径

![1608292853761](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1608292853761.png)



###### 9.3.2 ls

语法：ls [选项] [目录或是文件]

- 选项：
  - -a 显示当前目录所有文件和目录，包括隐藏
  - -l 以列表的方式显示信息，可显示文件权限信息

**选项可叠加**



###### 9.3.3 cd



![1608293804701](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1608293804701.png)

cd .. 回上一级

000000000

相对路径：

**从/usr/lib定位到/root：cd ../../root，每一个../就回上一级**



###### 9.3.4 目录操作

mkdir 创建目录

rmdir 删除目录

- 选项 -rf：递归强制删除 



###### 9.3.5 创建文件

语法：touch 文件名.文件格式[ ....可创建多个文件]



###### 9.3.6 复制

语法：cp [选项] source dest

选项：

- **-r ：递归复制整个文件夹**



使用细节：

- **如果不需要提示覆盖，则使用`\cp`**



例子：

1. `cp a.txt bbb/`  将a.txt拷贝带bbb/目录下
2. `cp -r test/ zwj/`  将test目录递归拷贝到zwj/目录下



###### 9.3.7 cat 查看文件内容



（1）语法：cat [选项] 要查看的文件

（2）常用选项：

- -n 显示行号



（3）使用细节：

- cat只能看不能改，一般会带上管道命令 `|`more





###### 9.3.8 more 

more 命令（分页）查看：more /etc/profile 

- 按enter一行行翻
- 按空格一页页翻
- 回到上一页：ctrl + b
- ![1608367884593](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1608367884593.png)



###### 9.3.9 *less



使用方法与more一样，但是有查找功能

![1608368163256](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1608368163256.png)



###### 9.3.10 > 与 >>



（1）基本语法：

![1608368367166](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1608368367166.png)



1. ls -l > 文件：将ls -l的内容写入到a.txt，如果a不存在则创建，存在则覆盖写入
2. ls -al >> 文件：将ls-l内容追加到>>后的文件的末尾
3. 

