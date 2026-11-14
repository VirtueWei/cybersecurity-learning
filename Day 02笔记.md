### 今日目标
1. 20 个核心命令能不看提示盲打
2. 能解释硬链接与软链接的区别（inode 层面）
3. `find` 的五类条件组合能随手写出

### 更改时区为上海
`timedatectl set-timezone Asia/Shanghai`
### 路径与连接

`.` 当前、`..` 上级、`~` 家目录、`-` 上一个目录。

### 熟练掌握二十个命令
- ls （显示目录内容）
	- -a：显示全部文件，连同隐藏文件。
	- -l：显示文件的详细信息，包括属性与权限等。
- cd（切换当前工作目录）
	- `cd "my dir"`目录含空格时用双引号。
	- `cd`回到家目录
- pwd（显示当前工作目录的绝对路径）
- mkdir（创建文件夹）
	- -p：创建多级目录
		- mkdir -p dir1/dir2/dir3
	- -m：指定权限
		- mkdir -m 755 test
		- mkdir -m u=rwx,go=rx test
- touch（创建空文件，更改时间）
	- mtime：内容变更时间
	- ctime：属性变更时间
	- atime：内容读取时间
	- -a：仅修改 access time。
	- -d：后面可以接已修改的日期而不用目前的日期。
	- -m：仅修改 mtime。
	- touch -d "2 days ago" bashrc 改为两天前
	- touch -t 201406150202 bashrc 将日期改为2014/06/15/02:02
		- 改动发生在mtime与atime上，ctime则不会改动，它只会记录当前变更的时间。
- cp（复制）
	- -a：相当于 -dr --preserve=all。
	- -i：若目标文件已经存在，在覆盖前会先询问。
	- -r：复制目录必须使用，递归复制。如果想要权限拥有者群组等都一样，可以用-a。-a是备份必用的参数。
- mv（移动文件或目录，或重命名）
	- -f：强制的意思，如果目标文件已经存在，不会询问而直接覆盖。
	- -i：若目标文件已经存在时，就会询问是否覆盖。
	- -u：若目标文件已经存在，且source比较新，才会更新。
- rm（移除文件或目录）
	- -r：递归删除
	- -f：忽略不存在的文件，不会出现警告讯息。
- find（全盘搜索）
	- 与时间有关的参数；
		- -mtime n：n为数字，意义为在 n 天之前的“一天之内”被更动过内容的文件；
		- -mtime +n：列出在n天之前（不含n天本身）被改动过内容的文件文件名；
		- -mtime -n：列出在n天之内（含n天本身）被更动过内容的文件文件名。
		- find / -mtime 0 ：0代表目前24小时内。
	- 与使用者或群组名称有关的参数；
		- -uid n：n为数字，这个数字是使用者账号ID，亦即UID，在/etc/passwd内。
		- -gid n：n为数字，数字为群组名称ID，亦即GID，在/etc/group内。
		- -user name：name为账号名称。
		- -group name：name为群组名称。
		- -nouser：寻找文件的拥有者不存在于/etc/passws的人。
		- -nogroup：寻找文件的有用群组不存在于/etc/group的文件。
			- 当自行安装软件时，很可能该软件的属性当中并没有文件拥有者，这时就可以使用-nouser与-nogroup搜寻。
	- 与文件权限及名称有关的参数；
		- -name filename：搜寻文件名称为filename的文件。
			- 默认为完整文件名，可以利用*来匹配关键字。
		- -size \[+-]SIZE：搜寻比SIZE还要大（+）或小（-）的文件。这个SIZE的规格有；
			- c：代表Byte，k：代表1024Bytes。所以要找比50KB还要大的文件，就是`find /home -size +50k`。
		- -type TYPE：搜寻文件类型为TYPE的。
		- -exec：额外动作
		- find /tmp -exec ls -l {} \\; 从-exec开始到\\;结束，是find额外的动作，find的结果将放在花括号内。
- grep
	- 用法：grep \[选项] "匹配模式" 文件...
	- grep "error" app.log    在app.log文件中搜索包含error关键字的行。
	- grep -i "error" app.log 忽略大小写。
	- grep -n "error" app.log    显示行号。
	- grep -r "error" /var/log/    递归搜索目录
	- cat app.log | grep "error" 从管道读取
- cat（由第一行开始显示文件内容）
	- -b：列出行号，仅针对非空白行。
	- -n：列出行号，连同空白行。
- less（分页显示内容）
	- -n，显示行号
	- -S，截断长行
	- /字符串：向下搜索“字符串”功能
	- ？字符串：向上搜索“字符串”功能
	- n：重复前一个搜索
	- N：反向的重复前一个搜索
	- g：前进到这个数据的第一行
	- G：前进到这个数据的最后一行。
- head（只看头几行）
	- -n：后面输入想要显示的行数。例：head -n 20 /etc/Demo
	- -c：显示多少字节，支持单位K、M、G。
- tail（只看最后几行）
	- -n：与head一样，只是tail看的是文件尾。
	- -f：持续监测，有变化随时更新。
- chmod（修改访问权限）
	- chmod 「选项」权限模式 文件...
	- 符号模式：「ugoa」「+-=」「rwxXst」
	- 八进制模式：如755、644
	- -R：递归修改
- chown（修改文件或目录的拥有者与群组）
	- 基本用法：chown「选项」用户：组 文件...
	- -R：递归修改
	- --reference=文件：参照某文件设置
		- 把目标文件的所有者和组设置成与参考文件一致；
		- chown --reference=template.conf target.conf
- ps（当前系统进程的快照）
	- ps「选项」
	- -e：显示所有进程
	- -f：完整格式输出
- top（实时动态查看系统整体运行情况和进程资源占用）
	- top「选项」
	- -d：指定刷新间隔
		- top -d 2 //每两秒刷新一次
	- -n：指定刷新次数后自动退出
		- top -n 5 //刷新5次后自动退出
- kill（控制进程的行为）
	- kill 「选项」 PID...
	- kill -15 1234    优雅的请求软件终止，较为客气。
	- kill -9 1234    强制斩杀，由内核直接强制结束，不能被进程捕获、阻塞或忽略。
- ssh（远程登录）
	- ssh 「选项」用户名@IP地址  命令
	- ssh alice@192.168.1.100  //以alice身份登录192.168.1.100
	- ssh example.com   //以当前本地用户名登录 example.com
	- ssh alice@example.com "ls -l /avr/log"  //登录后执行命令并退出

自我打分：90分


### find 十连
```bash
find /etc -name "*.conf" -type f
# 搜索根目录下的配置目录etc内所有的conf格式文件，类型为普通文件

find /var/log -size +10M
# 搜索日志目录内大小为10MB以上的所有文件

find / -mtime -1 -type f 2>/dev/null | head
# 搜索根目录内，内容更改时间1天内，类型为普通文件，并将错误信息消除，然后传给head，默认显示前十行。

find / -perm -4000 -type f 2>/dev/null
# 从根目录开始递归搜索所有设置了SUID的普通文件，并将标准错误输出丢弃。设置了SUID的文件在执行时将会以该文件所有者的权限运行，而不是以执行者的权限运行。比如说 su 命令的所有者是root，当普通用户执行它时就会以root的权限运行。

find . -maxdepth 2 -name "*.md"
# 从当前目录往下最多递归搜索2层目录，所有为.md的文件

find /tmp -type f -empty
# 类型为普通文件的空文件

find . -user secadmin -group secadmin
# 从当前目录开始递归查找“所有者是 secadmin 且所属组也是 secadmin”的所有文件和目录

find / -name "*.log" -exec ls -lh {} + | head
# 搜索所有log类型文件，并执行命令ls -lh，只显示前十行

find . -newer /etc/hostname
# 从当前目录开始递归查找“修改时间比 /etc/hostname 更新”的所有文件和目录

find / -type d -perm -0002 2>/dev/null | head 
#找出系统中所有“其他用户可写”的目录，并只显示前十行
```

#### grep 组合练习

```bash
grep -rn "PermitRootLogin" /etc/ssh/
#作用：在 SSH 配置目录里找 PermitRootLogin 这个选项在哪、在哪一行、当前值是什么。
#为什么：PermitRootLogin 控制 root 能否直接通过 SSH 登录。安全加固的标准动作就是把它设成 no 或 prohibit-password。这条命令是“查配置现状”的典型手法——先 grep 定位，在决定改不改。

grep -c "Failed password" /var/log/auth.log
#作用：统计认证日志里出现“Failed passworld”的行数，也就是 SSH 登录失败次数。
#为什么：这是排查暴力破解最经典的一招。如果这个数字几百上千，基本可以断定有人在爆破你的SSH。

grep -oE "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" /var/log/auth.log | sort -u | head
#作用：从日志里把所有 IP 地址抠出来，去重后看前几个
#为什么：这是入侵分析的第二步，上一条算出“失败很多”，这一条回答“是谁在试”。

ls -al /usr/bin/passwd  #看那个 S 位
#作用：看真实的 s 位权限是什么样。如果没有x权限则是大写S，如果有x权限则为小s。
#为什么：普通用户改密码要修改 passwd文档，能实现就是靠s权限。

stat /usr/bin/passwd
#作用：stat 显示文件的全部元数据，比ls -l 详细得多。
```

这 5 条不是随便凑的，它们构成一条**安全审计的完整小链路**，同时把前几天的知识点串起来：

查配置（grep -rn PermitRootLogin）   ← 看"门有没有关好"
        ↓
统计异常（grep -c Failed password）  ← 看"有没有人在撬门"
        ↓
定位来源（grep -oE IP | sort -u）    ← 看"撬门的是谁"
        ↓
验证权限（ls -al passwd）            ← 看"合法钥匙长啥样"
        ↓
深挖元数据（stat passwd）            ← 用 inode/权限/时间戳对账


### 总结
#### 学了什么 ?
学习了一些 Linux 的基础命令与参数。硬链接与软链接的区别。find与grep的熟练使用。
#### 卡在哪 ？
具体没有卡住，只是在 find 的十连与 grep 的练习上，到底要掌握到什么程度有一些不确定。目前find 能根据文字要求默敲出命令，grep 能看懂。因为网安的学习不是靠硬背，而是长时间使用的熟练记忆，所以这里我也不打算做到完全熟悉默敲的程度，后面有使用场景再熟悉即可。
#### 明天第一步！
开始 Day03 的学习。