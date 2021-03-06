# VV's SSH Tools



[TOC]

## English

### 1. What is it
Each time when I faced a release deployment, I faced countless numbers of physical or virtual PC devices and repeatedly tapped the keyboard and hit the "SSH" command. I am tired, I want to change. For this I learned [Expect](https://en.wikipedia.org/wiki/Expect) , which is too complicated. It also requires a lot of TK/TCL scripts to be installed on the system, which is too cumbersome. So I gave myself a birthday present and gave it to all of you.

**This is an automated tool for SSH remotely running scripts!** it can:
- Automatic  login SSH server by just wrtie  a simple configuration file
- Automatically run remote shell commands just like you type commands manually
- Automatically populated sudo password just like you entered it manually
- Automatically read remote command output just like you do it manually copying/pasting
- Built-in SCP functionality eliminates the need to retype command lines and passwords
- Other features, waiting for your contribution to join :-)
### 2. Usage
#### 2.1. Install
#### 2.2. Quick Start
#### 2.3. Commond Line Usage
#### 2.4. Write a Config File
#### 2.5. Write a Remote Shell Script
### 3. How Contribution
TODO 

   


## 中文版

### 1. 这是个啥货？

每次当我面临发布部署时，面对无数的无论是实体的还是虚拟的PC设备，无数次反复敲打键盘，敲打着“SSH”命令。我累了，我要改变。为此我学习了[Expect ](https://en.wikipedia.org/wiki/Expect) ，这货太复杂。而且需要在系统上装一大堆TK/TCL脚本，太麻烦。所以我给我自己一个生日礼物，也给你们大家。



**这是一个SSH远程运行脚本的自动化工具！** 它可以：

- 自动ssh登录服务器，一个配置文件就搞定
- 自动运行远程shell命令，就像你在手工输入
- 自动填充sudo密码，就像你在手工输入
- 自动读取远端的命令输出，就像你在手工复制/粘贴
- 整合SCP功能，不用再反复输入密码和命令行
- 其他功能，等待你的贡献加入 :-)





从此不用再：

- `scp`,`ssh`一个个命令反复敲打
- 反复密码输入
- 远程多机部署配置，一个脚本轻松搞定



> 感谢以下项目给予的启示与帮助
>
> 

### 2. 这货的使用方法

#### 2.1. 安装

在[这里](https://github.com/iONCreate/vvssh/release/)下载对应的版本即可，放置在对应目录

| 操作系统 |  架构  |
| :------: | :----: |
| Windows  |  X86   |
| Windows  | X86-64 |
|  Linux   |  X86   |
|  Linux   | X86-64 |
|  Linux   | ARMv7  |
|  Linux   | ARMv8  |
|  MacOS   | X86-64 |
|  MacOS   |  X86   |

#### 2.2. 快速上手





#### 2.3. 命令行使用

按照一下方式运行

- `vvssh [远程脚本文件名]` 
- `vvssh [配置文件名] [远程脚本文件名]`
- `vvssh [-u 用户名] [-p 密码] [-s 服务器] [-t 超时] [配置文件名] [远程脚本文件名]`
- *待实现：`vvssh [-u 用户名] [-p 密码] [-s 服务器] [-t 超时] [-c] [简单脚本]`* 



#### 2.4. 配置文件编写说明

配置文件主要用于存储一些主机的信息，免得你一天到晚，输入密码。

配置文件只有一行，共四个字段，以空格间隔，为以下内容：

```
[user] [password] [Server Address] [Time out]
```

| 字段           | 说明                     | 例子           |
| -------------- | ------------------------ | -------------- |
| user           | 用户名                   | admin          |
| password       | 密码                     | thisispass     |
| Server Address | ssh 服务器地址与**端口** | 192.168.0.1:22 |
| Time out       | 超时时间设置（单位：秒） | 30             |

以下是一个例子🌰：

```
admin thisisPass 192.168.0.1:22 20
```

> PS: 我一般都是用一个Shell 文件套用，用`echo "root test1 192.168.0.1 30" >Server.conf` 这样的形式直接生成一个我配置文件



- 未来可以支持多行不同的配置，看看大家的要求投票吧



#### 2.5. 远程脚本编写说明

##### 2.5.1. 简介

远程脚本文件在远程主机上执行，就像你在终端中直接敲打命令一样。但是有一些非常简单特殊命令你需要了解。

PS:你可以使用一个支持shell脚本的语法高亮编辑器轻松编辑该文件。

- 使用`#==`作为单行命令的标记

  > `#` 符号是shell 脚本中的注释符号

- 使用`$[]`作为内部变量的标记

  > 我选这个标记的原因是，现在终端命令中很少用到这个，但这个标示在很多shell脚本环境中具有歧义，也许以后会调整，大家可以多提建议

##### 2.5.2. 内部变量

内部变量使用来存储一些临时的可变字符串，凡事使用`$[变量名]`形式的合法变量，都将被替换为变量对应的字符串，例如下列命令将从目标主机上取得当前目录绝对路径，然后建立目录，再将本地文件复制到目标主机上：

```bash
#==var_cmd $[HOME_DIR] pwd
mkdir -p $[HOME_DIR]/ttt
#==scp /test1.file $[HOME_DIR]/ttt
```

内部变量还可以使用环境变量用来在两个不同的本机Shell 脚本之间沟通。

比如：现有两个脚本分别叫做install.sh和remote-cmd.sh

~~~bash
#!/usr/bin/env bash
#this is install.sh
SOURCE="${BASH_SOURCE[0]}"
while [ -h "$SOURCE" ]; do # resolve $SOURCE until the file is no longer a symlink
  DIR="$( cd -P "$( dirname "$SOURCE" )" && pwd )"
  SOURCE="$(readlink "$SOURCE")"
  [[ $SOURCE != /* ]] && SOURCE="$DIR/$SOURCE" # if $SOURCE was a relative symlink, we need to resolve it relative to the path where the symlink file was located
done
RUNDIR="$( cd -P "$( dirname "$SOURCE" )" && pwd )"
echo "Runing From $RUNDIR"
export PUBLISH_DIR=${RUNDIR}

echo "test pass 192.168.0.1 30" >${RUNDIR}/Server.conf

vvssh ${RUNDIR}/Server.conf remote-cmd.sh
~~~



~~~bash
#!vvssh
#this is remote-cmd.sh
#==autosudo

#==var_env $[PUBLISH_DIR] PUBLISH_DIR

#==var_cmd $[SSH_HOME_DIR] pwd
sudo apt-get -y -q install build-essential
mkdir $[SSH_HOME_DIR]/test
#==scp $[PUBLISH_DIR]/test.file $[SSH_HOME_DIR]/test

~~~





##### 2.5.3. 单行命令

目前支持以下命令：

1. user 

   * **说明：**设置SSH用户名称，（没错！你可以在脚本文件中设置），这个设置将覆盖之前命令参数以及配置文件的的设置

   * **参数：**`[用户名]`

   * **例子：**

     ````bash
     #==user admin
     ````

     

2. server

   - **说明：**设置服务器地址，（没错！你可以在脚本文件中设置），这个设置将覆盖之前命令参数以及配置文件的的设置

   - **参数：**`[服务器地址端口]`

   - **例子：** 

     ```bash
     #==server 192.168.1.1:22
     ```

     

3. pass

   - **说明：**设置SSH登陆密码，（没错！你可以在脚本文件中设置），这个设置将覆盖之前命令参数以及配置文件的的设置

   - **参数：**`[密码]`

   - **例子：**

     ```bash
     #==pass test123
     ```

     

4. timeout

   - **说明：**设置超时(秒)，默认30秒,超时从开始读取控制台输出计算，到读取到控制台输出行，规则是超时后首先发送`\r\n` 再次超时中断，执行后重新计算

   - **参数：**`[时间]` 

   - **例子：**

     ```bash
     #==timeout 30
     ```

     

5. autosudo

   - **说明：**设置自动提供远程SUDO密码，同pass设置

   - **参数：** 无

   - **例子：**

     ```bash
     #==autosudo
     ```

     

6. scp

   - **说明：**执行SCP传送任务

   - **参数：**`[本地文件名/或目录名] [远程文件路径]`

   - **注意：** 

     - scp 命令尚未提供更改目标文件（目录）名的功能
     - scp命令必须确保目标主机上路径存在

   - **例子：**

     ```bash
     # 传送单个文件到目标主机上的/home/test目录中
     #==scp /test.file /home/test
     
     #传送目录到目标主机上的/home/test目录中
     #==scp /test1 /home/test
     ```

     

7. var_cmd

   - **说明：**在目标主机上执行命令后，将执行后的结果存入本地变量

   - **参数：**`$[本地变量名称] [远端命令]`

   - **例子：**

     ```bash
     # 将目标主机上 pwd 命令返回的字符串 存入 HOME 变量中
     #==var_cmd $[HOME] pwd
     ```

     

8. var_env

   - **说明：**将环境变量存入本地变量，

   - **参数：** `$[本地变量名称] [环境变量名称]`

   - **注意：** 该命令不会进行预先检查，在运行时若没有环境变量将报错

   - **例子：**

     ```bash
     #==var_env $[LOCAL_RUN_DIR] LOCAL_DIR
     ```

     





## 参考

- [Shell Prompt](https://en.wikibooks.org/wiki/Guide_to_Unix/Explanations/Shell_Prompt)
- 

