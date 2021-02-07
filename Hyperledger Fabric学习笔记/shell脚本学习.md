# 简介

Linux中`.sh`后缀文件称为shell脚本，因为是跑在Linux的shell中，所以叫shell脚本。简而言之，shell脚本就是一些命令的集合。

Shell脚本能帮助我们很方便的去管理服务器，因为我们可以指定一个任务计划定时去执行某一个shell脚本实现我们想要需求。这对于Linux系统管理员来说是一件非常值得自豪的事情。

# 基本结构

## 首行

第一行内容在脚本的首行左侧，表示脚本将要调用的shell解释器，内容如下：

```shell
#!/bin/bash
```

`#!`符号能够被内核识别成是一个脚本的开始，这一行必须位于脚本的首行，`/bin/bash`是bash程序的绝对路径，在这里表示后续的内容将通过bash程序解释执行。

## 注释

用`#`作为注释开头即可。

## 内容

可执行内容和shell结构。直接输入命令即可，如：

```SHELL
#!/bin/bash
echo "hello world"
```

# Shell变量

变量：是shell传递数据的一种方式，用来代表每个取值的符号名。当shell脚本需要保存一些信息时，如一个文件名或是一个数字，就把它存放在一个变量中。

## 变量设置规则

1. 变量名称可以由字母，数字和下划线组成，但是不能以数字开头，环境变量名建议大写，便于区分。
2. 在bash中，变量的默认类型都是字符串型，如果要进行数值运算，则必须指定变量类型为数值型。
3. 变量用等号连接值，等号左右两侧<u>不能有空格</u>。
4. 变量的值如果有空格，需要使用<u>单引号或者双引号包括</u>。

## 变量分类

Linux Shell中的变量分为用户自定义变量，环境变量，位置参数变量和预定义变量。可以通过set命令查看系统中存在的所有变量。

* 系统变量：保存和系统操作环境相关的数据。`$HOME、$PWD、$SHELL、$USER`等等
* 位置参数变量：主要用来向脚本中传递参数或数据，变量名不能自定义，变量作用固定。
* 预定义变量：是Bash中已经定义好的变量，变量名不能自定义，变量作用也是固定的。

## 用户自定义变量

用户自定义的变量由字母或下划线开头，由字母，数字或下划线序列组成，并且大小写字母意义不同，变量名长度没有限制。

## 设置变量

习惯上用**大写字母**来命名变量。变量名以字母表示的字符开头，不能用数字。

## 变量调用

在使用变量时，要在变量名前**加上前缀“$”**。

使用echo 命令查看变量值。`eg:echo $A`

## 变量赋值

```shell
# 1. 定义时赋值：
# 变量＝值 等号两侧不能有空格
# eg:
    STR="hello world"
    A=9

# 2. 将一个命令的执行结果赋给变量
  A=`ls -la`  # 反引号，运行里面的命令，并把结果返回给变量A
　A=$(ls -la) # 等价于反引号
  eg: 
    aa=$((4+5))
    bb=`expr 4 + 5 `

# 3，将一个变量赋给另一个变量
　eg:
　  A=$STR
```

单引号和双引号的区别：

现象：单引号里的内容会全部输出，而双引号里的内容会有变化

原因：单引号会将所有特殊字符**脱意**

```shell
NUM=10   

SUM="$NUM hehe"
echo $SUM
# 输出10 hehe

SUM2='$NUM hehe'
echo $SUM2
# 输出$NUM hehe
```

　　

# set命令

> 写的每个脚本都应该在文件开头加上`set -e`,这句语句告诉bash如果任何语句的执行结果不是true则应该退出。
> 这样的好处是<u>防止错误像滚雪球般变大导致一个致命错误</u>，而这些错误本应该在之前就被处理掉。如果要增加可读性，可以使用`set -o errexit`，它的作用与`set -e`相同。

`set -e`，作用是：脚本只要发生错误，就会终止执行。

```bash
#!/usr/bin/env bash
set -e

foo
echo bar
```

执行结果如下。

> ```bash
> $ bash script.sh
> script.sh: line4: foo: No such file or directory
> ```





# 判断

## 判断文件是否存在

* `-d`：判断文件夹(directory)
* `-f`：判断文件(file)
* `-n`：判断是否有值，如`-n "$A"`，如果A有值，则为true

```SHELL
#!/bin/sh
# 判断文件是否存在
 
myPath="/var/log/httpd/"
myFile="/var /log/httpd/access.log"
 
# 这里的-x 参数判断$myPath是否存在并且是否具有可执行权限
if [ ! -x "$myPath"]; then
 mkdir "$myPath"
fi
# 这里的-d 参数判断$myPath是否存在
if [ ! -d "$myPath"]; then
 mkdir "$myPath"
fi
 
# 这里的-f参数判断$myFile是否存在
if [ ! -f "$myFile" ]; then
 touch "$myFile"
fi
# 其他参数还有-n,-n是判断一个变量是否是否有值
if [ ! -n "$myVar" ]; then
 echo "$myVar is empty"
 exit 0
fi
 
# 两个变量判断是否相等
if [ "$var1" = "$var2" ]; then
 echo '$var1 eq $var2'
else
 echo '$var1 not eq $var2'
fi
```





# Shell脚本的权限

一般情况下，默认创建的脚本是没有执行权限的。并且没有权限不能执行，需要赋予可执行权限，命令如下：

```BASH
chmod +x xxxx.sh
```

# Shell脚本的执行

```SHELL
# 1. 输入脚本的绝对/相对路径
$ /root/helloWorld.sh
$ ./helloWorld.sh

# 2. bash/sh + 脚本
$ bash /root/helloWorld.sh
$ sh helloWorld.sh

# 3. 在脚本的路径前再加". " 或source
$ source /root/helloWorld.sh
$ . ./helloWorld.sh
```

注意：

* 方法二可以不提前设定脚本的执行权限，甚至都不用写shell文件中的第一行（指定bash路径）。因为方法二是将`helloWorld.sh`作为参数传给`sh(bash)`命令来执行的。这时不是`helloWorld.sh`自己来执行，而是被他人调用执行，所以不要执行权限。

* 执行区别：
  * 前两种方法执行shell脚本时是在**当前shell**（称为父shell）开启一个子shell环境，此shell脚本就在这个子shell环境中执行。shell脚本执行完后子shell环境随即关闭，然后又回到父shell在。
  * 但方法三是在当前shell中执行的。