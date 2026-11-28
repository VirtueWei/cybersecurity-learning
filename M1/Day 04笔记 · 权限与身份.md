
**今日目标**
1. 能推导任意 umask 下的默认权限
	1. 文件默认权限为666，目录默认权限为777
	2. 然后umask可以查询默认值，默认权限减掉umask值，就是最终的默认权限。
	3. 减的时候要用符号减，不要用数字减。因为umask是拿掉哪个权限，而不是数字的加减。
2. 讲清 SUID/SGID/Sticky 三个特殊位各自的作用场景
	1. SUID：以属主的身份执行文件。通常用来给普通用户root的权限。
	2. SGID：以所属群组的身份执行。通常设置在目录，然后群组内的成员都能互相修改该目录内的文件。
	3. Sticky：只有属主与root能删除文件，特定情况下让其他人可以看，但不能删除。即使其他人有w权限。
3. 会用 sudoers 做最小授权，而不是"全给 sudo"
```bash
 sudo visudo -f /etc/sudoers.d/alice
 alice ALL=(root) NOPASSWD: /usr/bin/systemctl restart nginx
```
**sudo** 以管理员身份运行
**visudo** 编辑sudoers的专用编辑器。保存时会矫正语法，写错会提示并拒绝保存，防止把sudo改坏导致谁也sudo不了。
**-f <文件>** 指定编辑这个文件，而不是默认的/etc/sudoers。
**/etc/sudoers.d/alice** 放在/etc/sudoers.d/下的独立片段。主/etc/sudoers里通常有一行#includedir /etc/sudoers.d，会把该目录下所有文件自动纳入。按用户拆文件，好管理、好删除、互不影响。

为什么不直接改`/etc/sudoers`？因为那是全局大文件，多人维护易冲突；拆成`sudoers.d/alice`、`sudoers.d/bob` 每个用户一个文件，删用户就删文件，干净。

**规则本体**
```bash
alice ALL=(root) NOPASSWD: /usr/bin/systemctl restart nginx
```
sudoers的语法骨架是：

谁    在哪台主机 = (以谁的身份) 标签: 命令
User   Host      = (Runas)      Tag:  Command

所以规则的意思就是alice在本机中以root的身份，不需要密码，可以运行后面的命令。
要注意规则只授权了这一条指令，那么其它的sudo命令就 会全部默认拒绝。这就是最小授权。


**rwx对文件跟目录是不同的**：对文件 r=读取，w=修改，x=执行；对目录 r=列文件名，w=增删条目，x=进入(cd)。所以对目录而言，只有r时只能用ls列文件名，而只有w时基本没用，因为操作文件本身需要x权限，所以如果要操作文件，就要给x权限。w权限可以视情况不给。

**umask**：默认文件：666 - umask；目录：777 - umask。查询umask直接输入umask即可，修改则在命令后加数字即可，比如umask 022。

-  umask 实验：`umask` 看当前值，`umask 027` 后 `touch f1 && mkdir d1 && ls -l`，推导结果对不对
umask当前值为0002，umask027之后变成：
文件默认-rw-rw-rw减-----w-rwx=-rw-r-----
目录默认drwxrwxrwx减d--- -w- rwx =drwxr-x---
测试；
![image.png](https://bytewall-obsidian-1318337223.cos.ap-shanghai.myqcloud.com/inages/20261127210404847.png)
