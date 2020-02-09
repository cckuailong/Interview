### rsync：同步两个目录
	1) rsync -r src dst

### lsof：查找所有进程打开文件的信息
	1) lsof file 		查看单个文件相关进程
	2) lsof -c process	查看某个进程打开的文件，同lsof | grep process
	3) lsof -u user		查看某个用户打开的文件
	
### echo：打印字符串
	1) -e 激活转义字符
	
### grep：文件中查找字符串 [grep string file]
	1) -i 不区分大小写
	2) -r 递归查找文件夹中的文件
	
### find：查找文件名 [find path -name=file]
	1) -name 查找的文件名
	2) -iname 文件名不区分大小写

### sed：处理文本文件
	1) 整行删除 sed '2,5d' file (sed '2d' file)(sed '2,$d' file)
	2) 加内容在第二行前后 sed '2i add_thing' (sed '2a add_thing')
	3) 列出第几行 sed -n '5,7p'
	4) 寻找打印（删除） sed -n '/string/p' (sed '/string/d')
	5) 寻找替换 sed 's/old_string/new_string/g'
	
### awk：处理文本文件（便于信息统计）
	1) 输出第一列 awk -F ':' '{print $1}' file
	2) $1,$2....  $NF(最后一列), $NR(最后一行),$0(全部)
	
### diff：比较两文件不同 [diff file1 file2]

### sort：对文件内容排序 [sort file]
	1) sort -r file 逆序排序
	2) -t<分隔符>
	3) -k 指定排序区间
	4) sort -t: -k 3n /etc/passwd 将每一行以':'分隔，并以第三个字段进行排序
	
### export：输出环境变量
	1) export | grep ORACLE 配合grep使用，检索环境变量
	
### ls：列出目录中的文件
	1) ls -a 列出全部文件（包括隐藏文件）
	2) ls -l 列出文件详细信息
	3) ls -h 易读模式
	
### pwd：输出当前绝对路径

### service：操作服务(/etc/init.d/)
	1) service ssh status 查看单个服务状态
	2) service --status-all 查看所有服务状态
	3) service ssh start(stop,restart) 服务开启（关闭，重启）
	
### ps：显示正在运行的进程信息
	1) ps aux 显示所有的进程详细信息，包括守护进程
	2) 一般配合grep进行查找
	
### free：显示内存使用情况

### df：显示磁盘使用情况
	1) df -h 易读模式
	
### kill：杀死一个进程 [kill process_num]
	1) kill -9 process_num
	
### rm：删除文件/文件夹 [rm file]
	1) rm -r path 删除文件夹
	2) rm -f 强制删除
	3) rm -i 删除前会询问
	
### shred：重复覆盖文件，内容不可恢复
	1) -n 覆盖次数
	2) -z 最后一次用0覆盖，掩饰shred操作
	3) -u 覆盖后删除文件
	
### cp：拷贝文件 [cp src_file dst_file]
	1) -p 保持文件权限，属主和时间戳

### mv：移动文件 [mv src_file dst_file]

### cat：查看显示文件内容 [cat file1 file2 .....]

### mount：挂载文件系统 [mount /dev/sdb1 /file]

### chmod：修改文件或目录权限 [chmod 777 file]
	1) -R 修改文件夹目录（子文件也被修改）
	2) chmod +x file 增加执行权限
	3) rwx-->421
	
### chown：修改文件或文件夹的owner

### passwd：修改密码
	1) passwd USERNAME 超级用户修改其他用户密码
	
### mkdir：创建目录

### touch：创建空文件

### ifconfig：查看或配置网络接口
	1) ifconfig -a 查看所有
	2) ifconfig eth0 up(down) 启动或停止某个网络接口
	
### uname：显示操作系统信息
	1) uname -a 显示所有信息
	
### locate：找文件 [locate file]

### head/tail：显示文件前几行（后几行）

### wget：下载文件

### 后台运行
	1) nohup python test.py &
	2) screen (-ls -S -r)
	3) tmux