+++
date = '2025-07-11T15:00:00+08:00'
draft = true
title = 'Linux'
math = true
license = ""
description = ""
categories = [
    "学习笔记"
] 
tags = [
    "Linux"
]
image = ""

+++



## shell命令

### 复制 cp

#### 语法 

cp [选项] 复制目标 复制路径

#### 参数

- `-r` 或 `-R`：递归复制目录及其内容（用于复制目录）。
- `-i`：交互模式，覆盖前提示用户确认。
- `-f`：强制复制，覆盖目标文件而不提示。
- `-v`：显示详细的复制过程（verbose）。
- `-p`：保留文件的原始属性（如权限、时间戳等）。
- `-a`：归档模式，等同于 `-dpR`，保留所有文件属性和递归复制目录。
- `-u`：仅当源文件比目标文件新时才复制（更新模式）。
- `-l`：创建硬链接而不是复制文件。
- `-s`：创建符号链接（软链接）而不是复制文件。

### 删除 rm

#### 语法 

rm [options] name

#### 参数

- -i 删除前逐一询问确认。**有只读文件时用。**
- -f 即使原档案属性设为唯读，亦直接删除，无需逐一确认。
- -r 将目录及以下之档案亦逐一删除。

#### 举例

1. 删除目录下所有文件

```
rm  -r  * 
```

    1. ls : 目录信息查看
       1. ls -a : 显示目录所有文件及文件夹，包括隐藏文件
       2. ls -l : 所有文件信息用单列格式输出
       3. ll = ls -l
    2. cd : 目录切换
       1. cd / 进入根目录
       2. cd - 进入用户目录
       3. cd ../ 返回上一级目录
    3. pwd : 显示当前路径
    4. uname : 系统信息查看
    5. clear : 清屏
    6. cat : 显示文件内容
    7. sudo : 切换用户身份
    8. su : 切换用户
    10. touch : 创建文件
    14. mv : 移动文件(重命名)
    15. ifconfig : 显示网络信息 
    16. reboot : 重启系统
    17. poweroff : 关机
    18. man : 系统帮助
    19. sync : 数据同步写入磁盘
    20. find : 查找文件
        1.  find -name 文件 (./表示当前目录)
    21. grep : 查找内容
        1.  grep -nr
        2.  grep -ir 忽略大小写
        3.  grep -nir
    22. du : 查看文件夹大小
        1.  du -sh 加单位
    23. df : 磁盘空间检测
    24. gedit : 打开某个文件
    25. ps : 查看当前系统进程
    26. top : 进程实时运行状态查看
    27. file : 文件类型查看


## Ubuntu 软件安装
1. sudo apt-get install 
2. sudo dpkg -i xxx.deb
3. 自己下载源码安装
   1. make
   2. make install
4. appstore


## Ubuntu 文件系统结构
### 根目录
    "/"
### 文件系统
1. /bin 可执行文件
2. /boot 启动
3. /dev 设备驱动文件
4. /etc 配置文件
5. /home 用户文件夹
6. /lib lib64 库文件
7. /media 可插拔设备
8. /proc 系统运行文件、运行信息
### 绝对路径和相对路径
1. 绝对路径 : 从根目录"/"算起的路径
2. 相对路径 : 不是以"/" 开头的路径
   1. "." "./"代表当前路径
   2. ".." "../"代表上一层目录

## 磁盘管理
### 磁盘文件
1. /dev/sd* 磁盘设备文件，不能直接访问磁盘，必须先挂载到某一目录
### 磁盘和目录容量查询
1. df 主要查看文件系统使用量
2. du 主要查看单个文件大小
   1. du -h --max-depth=1(查看目录后1层)
### 磁盘挂载、卸载、分区、格式化
1. 挂载、卸载
   1. sudo umount /media/heimi/udisk
   2. sudo mount -o iocharset=utf8 /dev/sdb1 /media/heimi/udisk/
2. 分区
    fdisk
    1. sudo fdisk /dev/sdb 
    2. sudo fdisk -l 查看分区大小
3. 格式化   
   1. 磁盘分区创建好后可以格式化磁盘 mkfs
      1. mkfs -t vfta /dev/sdx


## 压缩 与 解压缩
linux 常用压缩格式 : .tar  .tar.bz2  .tar.gz
### gzip
1. gzip xxx  压缩
2. gzip -d 解压缩
3. gzip -r 对文件夹里的文件进行压缩但不打包
4. gzip -rd 对文件夹里的文件进行解压缩
### bzip2
1. bzip2 -z 压缩文件
2. bzip2 -d 解压缩文件
### tar 打包工具
1. 参数
   1. -c 创建新归档,创建压缩文件
   2. -x 从归档中解出文件，解压缩
   3. -j 使用bzip 压缩格式
   4. -z 使用gzip
   5. -v 打印出命令执行过程
   6. -f 使用归档文件
2. bzip2
   1. vcjf 压缩
      1. tar -vcjf xxx.tar.bz2 xxx
   2. vxjf 解压缩
3. gzip
   1. vczf 压缩
      1. tar -vczf xxx.tar.gz xxx
   2. vxzf 解压缩
      1. tar -vxzf xxx.tar.gz xxx

### rar
1. rar a xxx.rar xxx 压缩
2. rar x 解压缩

### zip
1. unzip 解压缩
2. zip -rv 压缩

## 文件权限
r=4  w=2  x=1
读 写 执行

### -rw-rw-r--

- 用户 - 用户组 - 其他用户
1. 用户 :  能读写 不能执行
2. 用户组 : 能读写 不能执行
3. 其他用户 : 只能读
### lrw-rw-rwx
    l 表示 该文件是个链接文件
### 修改权限
1. chmod 777(权限全开) xxx  修改文件权限
2. chown             修改文件所属用户
   1. sudo chown heimi.heimi hello 修改用户和用户组
   2. sudo chown .root hello 修改用户组
   3. sudo chown root hello 修改用户

### linux 连接文件
1. 软连接 ln -s 快捷方式
   1. 要使用绝对路径
   2. cp 默认复制，如果为软链接，会将源文件一同复制过去
   3. cp -d，代表复制时在源文件中除了links不复制其他符号链接
2. 硬连接 ln  inode 产生新的文件名
    防止文件被删除
    其中一个文件被修改 其他也会被改
    ll -i xx* 打印文件节点信息

## vim 编辑器
### vi 

退出 vi ESC + Shitf + :  wq
### vim模式
#### 输入模式

i 在当前光标所在字符的前面，转为输入模式。
I 在当前光标所在行的行首转换为输入模式。
a 在当前光标所在字符的后面，转为输入模式。
A 在光标所在行的行尾，转换为输入模式。
o 在当前光标所在行的下方，新建一行，并转为输入模式。
O 在当前光标所在行的上方，新建一行，并转为输入模式。
s 删除光标所在字符。
r 替换光标处字符。
#### 底行模式
x 保存当前文档并且退出
q 退出。
w 保存文档。
q! 退出 VI/VIM，不保存文档

#### 指令模式
1、移动光标指令：
h(或左方向键) 光标左移一个字符。
l(或右方向键) 光标右移一个字符。
j(或下方向键) 光标下移一行。
k(或上方向键) 光标上移一行。
nG 光标移动到第 n 行首。
n+ 光标下移 n 行。
n- 光标上移 n 行。
2、屏幕翻滚指令
Ctrl+f 屏幕向下翻一页，相当于下一页。
Ctrl+b 屏幕向上翻一页，相当于上一页。
3、复制、删除和粘贴指令
cc 删除整行，并且修改整行内容。
dd 删除该行，不提供修改功能。
ndd 删除当前行向下 n 行。
x 删除光标所在的字符。
X 删除光标前面的一个字符。
nyy 复制当前行及其下面 n 行。
p 粘贴最近复制的内容。


## linux c
### gcc
  -c   编译和汇编, 不链接   生成.o
  -o   output 生成可执行文件

### makefile

#### 命令

make
make clean
行首不能用空格

#### 语法
目标 : 依赖文件集合
命令1
命令2
...

最主要目标放第一条规则
#### 变量
makeflie变量类似c语言宏定义

变量引用 $(变量)

1. "="  变量类似指针
2. ":=" 变量类似c语言等号
3. "?=" 如果变量没被赋值 ,则赋值变量
4. "+=" 变量追加  字符串后拼接字符串

"%" 通配符

自动化变量
1. $@规则中的目标集合，在模式规则中，如果有多个目标的话，“$@”表示匹配模
式中定义的目标集合。
2. $< 依赖文件集合中的第一个文件 如果依赖文件是以模式(即“%”)定义的，那么
“$<”就是符合模式的一系列的文件集合。
3. $^所有依赖文件的集合，使用空格分开，如果在依赖文件中有多个重复的文件，
“$^”会去除重复的依赖文件，值保留一份。


#### 伪目标创建
.PHONY 伪目标

#### 函数


#### 例
object = main.o input.o caclu.o
main: $(object)
   gcc -o main $(object)

main.o: main.c
   gcc -c main.c

input.o: input.c
   gcc -c input.c

caclu.o: caclu.c
   gcc -c caclu.c

clean:
   rm *.o
   rm main

#### 例
  1 object = main.o input.o caclu.o
  2 main: $(object)
  3     gcc -o main $(object)
  4 
  5 %.o: %.c
  6     gcc -c $<
  7 
  8 clean:
  9     rm *.o
 10     rm main


## 环境变量
echo $PATH 查看环境变量

export PATH=命令行路径:$PATH  添加环境变量

## shell 脚本
.sh
第一行 #!/bin/bash
### 语法
echo 在终端上显示字符串]
1. cmd1 && cmd2 : cmd1执行完且执行正确再开始执行cmd2
2. cmd1 || cmd2 : cmd1执行完且执行正确则cmd2不执行
3. read 输入
    1. read -p 输出提示符 
4. 数值计算 $(表达式) 变量=$(表达式) "="两边不能有空格
5. test 文件,数值, 字符  
   [test]:(https://www.runoob.com/linux/linux-shell-test.html)
   1. 文件测试
      1. test -e
6. 中括号[]
   1. 只能输 == 或 !=
   2. ["$input1" == "$input2"] 需要加双引号
7. 默认参数
   1. $# 表示最后参数的编号
   2. $@ 输出各个参数
   3. $0-$n shell脚本参数  $0为shell本身
8. 条件判断
   exit 0 退出条件
   1. 
   if 条件判断 ; then
   ...
   elif 条件判断 ; then
   ...
   else 
   ...
   fi
   2.
   case $变量 in
   "变量值")
       程序段
       ;; //表示该程序块结束
   "变量值")
       程序段
       ;;
   *)  //其他
       程序段
   esac
9. 脚本函数
    function fname()
    {
      函数代码段
    }
    10 .循环
    while [条件]
    do
       循环代码段
    done


    for var in con1 con2 con3
    do
       ...
    done
    
    for((初始值;限制值;执行步长))
    do
        ...
    done

 






## linux alias命令查看和设定别名
### alias
   :查看系统中所有的命令别名
### 设定别名
   alias 别名='原命令'
### 删除别名
  unalias 别名
### 使别名永久生效
   vi  ~/.bashrc  写入这个文件中即可永久生效   编辑完之后记得使环境变量生效: source .bashrc


## pip 换源
   pip install [需要安装的库] -i https://pypi.tuna.tsinghua.edu.cn/simple



## 重启网络服务
service network-manager restart


## 利用wget命令即可从网页下载，下面的例子是将网页上的该文件下载到当前路径下

wget https://download.pytorch.org/models/resnet18-5c106cde.pth