/etc 所有配置文件
/var 会变大的数据，日志，缓存，数据库
/usr 程序本体
/home 普通用户的家目录
/root 管理员的家目录 
/tmp 临时文件，关机消失
/proc 内核暴露的伪文件系统
/sys
/opt 第三方程序
/srv 服务数据

**命令**
- uname -r：内核版本
- cat /etc/os-release：发行版本
- lsblk：磁盘分区
- free -h：内存
- ip a：IP
- df -h：磁盘占用