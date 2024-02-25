"# Distributed-homework" 
LKM：Linux内核为了扩展其功能所使用的可加载内核模块，动态加载，无须重新实现整个内核。
安装LKM模块：
1.	将系统的当前软件包更新到最新版本。
	sudo apt update && sudo apt upgrade -y
 
2.	下载并安装编译内核的基本软件包。
	sudo apt install build-essential libncurses-dev libssl-dev libelf-dev bison flex -y
	sudo apt install linux-headers-$(uname -r)
  
3.	清理已安装的软件包。
	sudo apt clean && sudo apt autoremove -y
 
4.	进入LKM文件夹，编译 LKM。
	sudo make ARCH=$(arch) -j$(nproc)
 
	报错
 
	解决方案，将Makefile：
	由：
 
	改为：
 
	成功编译：
 
5.	加载 LKM
	sudo insmod new_lkm.ko
	出现错误，原因是编译使用的内核头文件版本和正在运行的内核版本不一致 
 
	解决方案：make clean & make
 
	重新运行 sudo make ARCH=$(arch) -j$(nproc)
 
6.	检查 LKM 是否已成功加载
	lsmod
 

Auditd：Linux审核系统的用户空间组件。负责将审核记录写入磁盘。通过ausearch或aureport查看日志。审核系统或加载规则的配置是使用auditctl实用程序完成的。在启动过程中，/etc/audit/audit.rules中的规则由auditctl读取并加载到内核中。
安装 Auditd
1.	Ubuntu 系统安装 Bash
	sudo apt update
	sudo apt install bash-completion
 
2.	安装 Auditd
	sudo apt-get install auditd
 
 
安装 Apache2
1.	现在安装 Apache2
	sudo apt install apache2
 
	查看apache2
	apache2 -version
 
3.	防火墙配置
	sudo ufw app list，显示不同的 apache 配置文件。
 
	将使用高度限制的配置文件“Apache”在端口 80 上启用网络 ctivity。
	sudo ufw allow ‘Apache’ 
 
	sudo ufw status，显示防火墙中允许的 Apache
 
4.	检查apache服务是否可操作
	sudo systemctl status apache2
 
5.	转到浏览器，网页输入http://192.168.59.129，重定向到 apache 主页。
 

生成日志
	确保Auditd和Apache2打开服务
1.	然后进行一些网页浏览以生成日志
2.	运行以下脚本以停止服务并收集该会话的日志
	sudo bash stopnCollect.sh
	日志在当前目录的文件夹“LOGS_0311”中生成。
 
通用日志文件生成
1.	parsetojson.py ：将审核日志解析为 JSON 格式。
	Python3 parsetojson.py
 
2.	生成output.txt：python3  parsetojson.py <path of audit log> > output.txt
	python3 parsetojson.py audit_rules/LOGS_0311/audit_1708413571.log > /fbs_outputs/
	output.txt
	output.txt包含 JSON 格式的日志。
 
3.	merge_json.py ：根据 srn 合并 json 格式的审计日志。
	sort output.txt 
	python3 merge_json.py /home/fbs2/下载/DisProTrack-main/fbs_outputs/output.txt > /home/fbs2/下载/DisProTrack-main/fbs_outputs/output_merged.txt 
	安装matplotlib，出错，无法定位软件包
	输入sudo apt update 
	安装成功
 
 
 
 
4.	ULF_gen.py：将应用程序日志和审核日志合并到一个名为 data1.json 的文件中 
	用法：
	需要在此脚本中手动添加要解析的文件
 
5.	sort_ULF.py：根据 date/ts 对通用日志文件进行排序，生成universal_log.json
 
测试阶段（以apache2为例）
一、静态分析
	python3 .\static_analysis\lms_cfg_gen.py --exe ./binaries/apache2 
	程序成功执行后，创建一个graph.json，lms_gen_stats.txt，time_resource_util.txt。

二、动态分析：
1.	sudo insmod new_lkm.ko，获得增强的日志
 
2.	sudo bash stopScript.sh，停止所有服务
 
3.	sudo bash startScript.sh，添加审计规则并启动 apache2 服务
 
4.	检查状态，确保apache2正在运行，运行ps -eall | grep 'apache2'命令
 
5.	分别进行网络浏览操作，包括注册账号、修改密码和链接登录以生成日志

6.	sudo bash stopnCollect.sh，停止服务并收集该会话的日志，日志将在当前目录的文件夹“LOGS_0311”中生成
  
7.	python3 ULF_generation/parsetojson.py fbs_outputs/注册/audit.log > fbs_outputs/注册/audit.json，输出 json 格式的日志
 
8.	sort fbs_outputs/注册/audit.json > fbs_outputs/注册/audit_sorted.json， srn 合并 json 格式的审核日志
	python3 ULF_generation/merge_json.py fbs_outputs/audit_1708413571_sorted.json > fbs_outputs/audit_1708413571_merged.json
 
9.	python3 ULF_generation/ULF_gen.py，将应用程序日志（apache）和审核日志合并到一个名为 data1.json 的文件中
 
10.	python3 ULF_generation/sort_ULF.py，据 date/ts 对通用日志文件进行排序，生成universal_log.json
 
11.	python3 ./UPG_construction/upg_gen.py，构建来源图，用graph.json的路径，并根据本地计算机universal_log.json进行构建，生成provenanceGraph.html
 
 
