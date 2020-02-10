---
layout: default
---
[TOC]
# Linux常用命令
## 获取Linux命令的帮助
在开始Linux命令学习之前，我们需要了解如何获取命令的帮助。因为Linux下的命令非常之多，大多数命令的参数与选项也很多，记住所有的命令、参数、选项是几乎是不可能的事，所以学习Linux命令重要的不是全部记住，而是知道如何获得帮助。第一种常用的方法是在命令后面跟选项`--help`，第二种是用`“man 命令名字”`的方法查询命令帮助（man是manual(手册)的缩写），第三种是运行`“info 命令名字”`，第四种执行`“whatis 命令名字”`来查看命令的简要描述。
## 查看文件清单命令 ls
直接运行命令`ls`便可查看目录中所有非隐藏文件与目录的名字。增加选项`-l`列出详细信息，其输出的每一行含义如下：

| 部分 | 含义 |
| :-: | :-: |
| First | 第1个字符表示类型（如“-”为普通文件，“d”为目录，“l”为符号链接），后9个字符表示权限，“r”、“w”、“x”分别表示读、写、执行权限，“-”表示没有这种权限（第2\~4个字符表示所有者的权限，第5\~7个字符表示所属组的权限，第8\~10字符表示其他账户的权限） |
| Second | 如果是目录，表示子目录数（最少为2，每个目录都有.与..两个子目录）；否则，表示硬链接数 |
|Third | 表示文件所有者|
|Fourth | 表示文件所属组|
|Fifth | 表示文件大小|
|Sixth | 表示文件修改时间（如果再加上选项`-u`，即用选项`-lu`，看到的时间就是访问时间；加上选项`-c`，看到状态改动时间）|
|Seventh | 为文件名本身，如果是符号链接的链接文件还会显示链接关系|

选项`-a`或者`--all`用来查看目录下的所有目录与文件（包括“.”开头的隐藏文件），`-t`将按照修改时间由新到旧列出文件，`-i`或`--inode`可以显示文件的inode号。如果只想查看某个文件的信息用`“ls -l 文件名”`，而运行`“ls -l 目录名”`则会列出该目录所含的文件与目录，要查看目录本身的信息则需加上参数`-d`或者`--directory`。
## 浏览文件命令 cat、more、less、head、tail
`cat`命令会将文件内容“一股脑”地显示出来；对于长文件来说，用`more`更方便一些，它能将长文件分页显示，回车显示下一行，空格下一页，[B]上一页，[Q]退出；分页显示还可以使用`less`命令；使用`head`命令默认显示文件开头10行，可加`-n K`或`-K`显示文件的前K行；而`tail`命令默认显示文件结尾10行。

## 文件统计命令 wc
运行`“wc 文件名”`将会显示出文件的行数、单词数与字节数，增加选项`-l`只显示行数，`-w`只显示单词数，`-c`只显示字节数，`-m`只显示字符数。
##  改变当前工作目录命令 cd
可用绝对路径也可用相对路径。`..`表示父目录，`../..`表示父目录的父目录。
## 创建目录命令 mkdir
用`“mkdir 目录名”`来创建目录，如果要创建多层目录，则用选项`-p`或`--parents`来快速创建。
## 复制命令 cp
用`“cp 源文件 目的文件/目录”`来进行拷贝文件，若要拷贝多个文件，则用命令`“cp 源文件1 源文件2 …… 目的目录”`。如果需要复制目录，则增加选项`-r`或`-R`或`--recursive`。如果原来便存在名为b.txt的文件，那么执行`cp a.txt b.txt`会将原来的b.txt覆盖掉，为避免这种情况的发生，可以增加选项`-b`起备份作用，原来的b.txt将会被备份为b.txt~。如果原来存在的b.txt为一个只读文件（即r--r--r--），那么执行`cp a.txt b.txt`就会遇到权限不足的问题，这时可以增加选项`-f`或者`--force`强制覆盖，或增加选项`-i`或者`--interactive`在得到提示后键入y或Y后按回车确认。
## 重命名或移动命令 mv
用`“mv 源 目的”`可将源文件重命名为目的文件，或将源文件移动至目的目录下，或将源目录重命名为目的目录。同`cp`命令，可以加选项`-b`起到备份作用，如遇到原来存在的文件为只读文件，也可用`-f`或`-i`起到强制覆盖的作用。
## 创建符号链接与硬链接命令 ln
Windows下的快捷方式，在Linux下对应的是**符号链接**，建立符号链接用选项`-s`或`--symbolic`，即命令`“ln -s 目标文件(源文件) 链接文件”`。如果源文件不存在，该命令仍可运行成功，而且链接后将源文件删除也是被允许的，删除符号链接文件对源文件无影响。运行`ls -l`后可以看到符号链接文件的字节数为源文件字符串的长度，而第一项的第一个字母为“l”，表示符号链接。

格式为`“ln 目标文件(已存在的源文件) 链接文件”`。硬链接在建立之前源文件必须存在。删除目标文件或链接文件中的一个，另一个文件除了硬链接数会减1以外无其他不同。在运行`ls -l`可以发现，链接文件的信息除了名字外，与源文件的一模一样，修改其中一个文件的内容另一个文件的内容也会随之改变（包括修改时间），事实上，互为硬链接的文件inode号是一样的。有的Linux系统不允许建立目录的硬链接，而有的Linux系统只允许超级账户建立目录的硬链接。此外，硬链接有个限制，链接在一起的文件一定要在同一文件系统内，而符号链接就没有这个限制。
## 显示当前目录命令 pwd
直接运行`pwd`命令，可以查看当前路径，如果用选项`-L`则查看逻辑路径，用选项`-P`查看物理路径（实际路径）。当且仅当当前目录或其各级目录中存在符号链接时，两种路径才不一样。
## 产生空文件或者改变文件时间戳命令 touch
运行`touch file`命令，如果file不存在，则产生一个空文件file，如果存在，则该文件的修改时间设定为当前时间。事实上，不带参数的touch命令会将文件的访问时间、修改时间、状态改动时间都该为当前时间，用选项`-a`只设定访问(access)时间，选项`-m`只设定修改(modification)时间。
## 查看账户名及其所属组的命令 whoami、id、groups
查看账户名，可用`whoami`或`id -un`命令查看。运行`groups`可查看当前账户属于哪些组，输出的第一个组叫做首要组，其余的叫做次要组。运行`id`或者`id -a`也可以查询当前账户所在的组，同时可查看账户号uid与各个组的组号gid。如果需要查看其他账户属于哪些组，则将账户名作为参数跟在`groups`或`id`命令后面就行了。
## 修改账户密码命令 passwd
运行`passwd`命令，输入旧密码与两次新密码，便可修改密码。

加密后的密码存在/etc/shadow中，该文件的权限为“-rw-r----”，所有者为“root”，即只有root账户可以对该文件进行写。可是事实上，任何用户都可以运行`passwd`命令，即任何用户都可以使得/etc/shadow的内容更新。我们看一下可执行文件passwd:
```bash
$ which passwd
/usr/bin/passwd
$ ls - /usr/bin/passwd
-rwsr-xr-x 1 root root 59640 Jan 25  2018 /usr/bin/passwd
```
注意到账户root对该文件的权限为“rws”，而非“rwx”，“s”就是**SUID**（为set uid的缩写），其仅对可执行二进制文件起作用，它能使其他账户在执行时具有文件所有者的权限。执行`passwd`命令的时候，即是执行/usr/bin/passwd，而/usr/bin/passwd有SUID权限，则执行该命令的账户就临时具有了与root账户一样的权限以能够向文件/etc/shadow写入。

## 改变权限命令 chmod
该命令的选项为\[ugoa]\[+-=][rwx-]，其中u表示文件所有者(user)，g表示文件所属组(group)，o表示其他账户(other)，a表示所有账户(all)，+、-、=分别表示权限的增加、减少、定义。改变文件的权限还有一种“数值”表示方式，r、w、x、-可以分别用4、2、1、0来表示，然后不用的权限可以表示成0\~7这8种数值，例如3(=2+1)只表示“-wx”，，用`chmod ugo file`便可改变文件权限，这里ugo是3位0\~7的数字，表示三种情况下的权限，如“754”表示权限“rwxr-xr--”，运行`chmod 000 file`便将文件权限设置为“---------”。如果要对目录及其所有文件与子目录都做权限修改，则需要增加选项`-R`或`--recursive`(recursive意即递归)。

前面提到过有一种特殊的权限——“s”。要对文件所有者增加**SUID**属性，用`chmod u+s file`即可，需要注意的是设置SUID属性需要保证文件对所有者有执行权限，如果一个文件原来权限为“rw-r--r--"，在执行`chmod u+s file`后会发现其权限变为“rwSr--r--”，s是大写的，这是不正常的；同样的也有数值的方法，其实chmod后面可以跟4位八进制数，当第一位为4时表示设置SUIFD属性，如运行`chmod 4755 file`，文件file的权限便成了“rwsr-xr-x”。

学习了SUID以后，**SGID**就容易理解了，它就是set gid的缩写，同样设置SGID属性的文件/目录也需要所属组同时有执行权限。给可执行文件增加SGID属性后其作用与SUID类似，如果给目录增加SGID属性，任何账户（包括其他组的用户）在该目录下创建的的新文件或新子目录所属组与该目录所属组一样。可用`chmod g+s file`添加属性，也可让chmod后面的4位八进制数的第一位是2，设置SGID属性。

在这之后，我们还要学习一下**SBIT**属性，它是sticky bit的缩写，也叫粘滞位。如果一个目录权限是“rwxrwxrwx”，那么所有账户都可以对目录中的文件或子目录随意删除（包括不是自己的）。在运行`“chmod o+t 目录名”`后，目录对其他账户增加了属性SBIT（增加SBIT属性要确保其他账户有执行权），而目录权限将会变为“rwxrwxrwt”，此时任一账户不可删除别的账户的文件或目录。同样，也有数值表示的方法，当chmod后面的4位八进制数的第一位是1时，设置SBIT属性。

也许你发现了，设置SUID、SGID、SBIT属性分别用数字4、2、1表示，很熟悉对吧？这三种属性是可以同时设置的，将对应数字加起来表示成1~7的数字放在4位八进制数的第一位就好了。
## 查看文件状态命令 stat
用`“stat 文件名”`命令可以查看文件或文件系统的许多信息。用其查看的文件时间比用ls命令查看的更加准确，例如：
```bash
$ stat test.txt 
  File: test.txt
  Size: 259681    	Blocks: 512        IO Block: 4096   regular file
Device: 801h/2049d	Inode: 398144      Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1000/  vmware)   Gid: ( 1000/  vmware)
Access: 2019-08-02 19:03:37.846331358 -0700
Modify: 2019-08-02 09:37:39.478069425 -0700
Change: 2019-08-02 19:04:30.728469037 -0700
 Birth: -
```
如果只想查看文件的三种时间，则可运行如下命令（其中，%x、%y、%z分别对应访问时间，修改时间和状态改动时间）：
```bash
$ stat --printf="%x\n%y\n%z\n" test.txt
2019-08-02 19:03:37.846331358 -0700
2019-08-02 09:37:39.478069425 -0700
2019-08-02 19:04:30.728469037 -0700
```
## 删除命令 rm、rmdir
rm命令用于删除(remove)文件，对于只读文件，rm命令会收到提示，如要删除则需要输入y或者Y后回车，如果不想每次都看到提示可以增加`-f`选项。rmdir命令用于删除空目录，如果要删除非空目录，需要先将目录清空，一级级清空目录显然十分麻烦，实际上，用带`-r`、`-R`或`--recursive`选项的rm指令便可将非空目录直接删除，即`“rm -r 目录名”`。
## 编辑文件命令 vi
编辑器vi是*visual interface*的缩写，而vim是*vi improved*的意思，也就是升级版的vi。一般vi就够用了，如果想要代码高亮可以选择vim，我们不讨论vi与vim的区别，只涉及它们的基本操作。vi有两种模式：命令模式(command mode)与插入模式(insert mode)。运行`“vi 文件名”`或`“vim 文件名”`，进入编辑器，此时在命令模式，按[I]键，进入插入模式，在插入模式按[Esc]键回到命令模式。

在命令模式下，光标的移动操作如下表所示（用上下左右键也可以）：

键 | 功能 || 键 | 功能 
:-: | :-: | :-: | :-: | :-: 
`h / Backspace(删除键)` | 向左移动 || `l / 空格键` | 向右移动
`j` | 向下移动 || `k` | 向上移动
`Enter(回车键)` | 移动到下行行首 || `-` | 移动到上行行首

对文件修改的操作：（仍留在命令模式）

操作 | 功能 || 操作 | 功能
:-: | :-: | :-: | :-: | :-: 
`x` | 删除当前字符 || `nx` | 删除从光标开始的n个字符
`dd` | 删除当前行 || `ndd` | 向下删除当前行在内的n行
`u` | 撤销上一步操作 || `U` | 撤销当前行的所有操作

插入文本或行的操作：（进入插入模式）

操作 | 功能 || 操作 | 功能
:-: | :-: | :-: | :-: | :-: 
`a` | 在当前光标的左边添加文本 || `A` | 在当前行的末尾添加文本
`i` | 在当前光标的右边添加文本 || `I` | 在当前行的非空白字符的行首添加文本
`o` | 在当前行的下面新建一行 || `O` | 在当前行的上面新建一行

保存文件和退出的命令：（如在插入模式，需先按[Esc]键进入命令模式）

命令 | 功能 || 命令 | 功能 
 :-: | :-: | :-: | :-: | :-:
`:w` | 保存文件，不退出vi或vim || `:w filename` | 另存为filename，不退出vi或vim
`:q` | 退出vi或vim编辑器（如果无修改）|| `:q!` | 退出退出vi或vim编辑器，并放弃保存
`:wq` | 退出vi或vim编辑器，并保存文件

## 用于显示的命令 echo
echo命令默认禁止转义，用选项`-e`后使能转义；用echo显示参数时，行尾默认有换行符，选项`-n`去掉行尾的换行符。echo命令的转义字符如下：

\a | \b | \e | \f | \n | \r | \t | \v | \\\ 
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: 
响铃 | 退格 | Esc键 | 换页 | 换行 | 回车 | 横向Tab键 | 竖向Tab键 | 反斜杠 |

| \c   | \0NNN | \xHH |
| :-: | :-: | :-: |
|取消该字符后输出 | 八进制表示的ASICC码字符 | 十六进制表示的ASICC码字符|

## 查看文件类型命令 file
Linux系统不用文件扩展名来确定文件类型，要想知道文件的类型，可以用file命令加文件名来查看。
## 显示树状目录信息命令 tree
如果目录层次较多，用ls命令查看较不方便，这时我们就可以用`tree`来查看该目录的树状结构，需要注意的是，tree后面不需要跟目录名。如果只想查看各级子目录（不显示其中文件），用选项`-d`，如果只想最深显示第n层子目录，可运行命令`tree -L n`。选项`-p`用于显示类型与权限(permission)；`-s`用于显示大小(size)；`-D`用于显示文件修改时间(moDification time)。
## 查找命令 find
find命令常用格式为`“find [起始目录] 选项与查找条件 [操作]”`，find命令默认递归查找，如果没有起始目录，默认从当前目录查找，默认操作是`-print`。其常用选项见下表：

选项与参数 | 作用
:-: | :-:
`-name filename` | 按照名字filename查找，可以使用通配符（如“*”表示多个字符，“?”表示一个字符） 
`-perm mode` | 按权限mode（3位八进制数）查找
`-user account_name` | 按所有者为account_name查找（account_name处可以是账户号uid）
`-group group_name` | 按所属组为group_name查找（group_name处可以是组号gid）
`-mtime {-n|+n}` | 查找修改时间距现在n天之内(-)/之前(+)的文件
`-type {b|d|c|p|l|f}` | 查找某一类型的文件（b、d、c、p、l与f分别表示块设备、目录、字符设备、管道、符号链接、普通文件）
`-newer file` | 查找修改时间比某个文件新的文件
`! 某选项` | 在选项前加“!"表示取反，如`! -newer file`表示查找修改时间比file晚的文件

find命令的常用操作除了默认的`-print`以外，还有`“-exec 命令 {} \;”`，“{}”表示将找的文件名放到这个位置，执行`“命令 文件名”`。例如执行`find . -name "*.txt" -exec ls -l {} \;`（注意末尾有“\\;”），便会按`ls -l`命令输出当前目录里面（包括子目录里）所有后缀为.txt的文件。`-exec`可以换成`-ok`，使用`-ok`时每一次执行`“命令 文件名”`都要求用户进行确认，输入y确认执行，输入n取消执行。 更多的，还可以使用管道与xargs命令对查找到的命令进行操作，xargs主要用于给其他命令传递参数。例如，可用如下命令将当前目录里的所有.txt文件权限全改为700：`find . -name "*.txt" | xargs chmod 700`。

<div STYLE="page-break-after: always;"></div>
# Bash内置命令与环境简介
## 什么是Bash
Shell是Linux的外壳，是Linux的用户界面，可以为用户提供输入命令与参数，并库得到命令执行结果的环境，第一个主流的shell版本是Bourne shell，简称sh。Bash诞生于1987年，它是sh的超集，几乎完全兼容sh并拥有更多的特性和功能，Bash几乎是所有Linux操作系统默认的shell。
## 内置命令与外部命令
**内置命令**是Bash自带的命令，也叫内建(built-in)命令。直接运行`help`将会看到Bash内置命令与关键字的列表，可以看到cd、pwd、echo等常用命令就在其中，要查看某一内置命令的帮助，运行help加命令名字便可查看，实际上help也是一条内置命令:
```bash
$ help help
help: help [-dms] [pattern ...]
    Display information about builtin commands.
    
    Displays brief summaries of builtin commands.  If PATTERN is
    specified, gives detailed help on all commands matching PATTERN,
    otherwise the list of help topics is printed.
    
    Options:
      -d	output short description for each topic
      -m	display usage in pseudo-manpage format
      -s	output only a short usage synopsis for each topic matching
    		PATTERN
    
    Arguments:
      PATTERN	Pattern specifiying a help topic
    
    Exit Status:
    Returns success unless PATTERN is not found or an invalid option is given.
```
可以看到help命令有三种选项，`-d`显示简要功能介绍，`-m`显示手册页形式命令介绍，`-s`显示简要语法格式。

与内置命令相对的是**外部命令**，外部命令不是Bash自带的，而是安装在Linux的某个地方，例如ls命令就是一条外部命令，用`which ls`可以查看它安装在哪，当然了用`help ls`命令是无法看到该命令的帮助的。

我们已经知道echo是一条内置命令，而当我们运行`which echo`将会看到输出`/bin/echo`，说明它也是一条外部命令，其实有一些命令既是内置命令又是外部命令，这是因为Linux安装了一些与Bash内置命令同名的外部命令。因为内置命令的优先级高于外部命令，所以执行`echo "Hello world!"`时执行的是内置echo命令，如果想执行外部echo命令，则需要加上全路径：`/bin/echo "Hello world!"`。
## 认识Bash环境
### *命令行提示 PS1*
我们通常看到的命令行提示为`“账户名@计算机:当前路径$”`，其实用户是可以定制命令行提示的。**PS1**是Bash的内置变量，用于设置第一命令提示（主命令提示）。设置SP1时，Bash命令提示符内置变量设置如下表所示：

设置 | 描述 || 设置 | 描述
:-: | :-: | :-: | :-: | :-: 
`\H` | 完整的主机名称 || `\h` | 仅取主机的第一个名字
`\s` | shell的名称，即bash || `\d` | 日期，格式为weekday month date
`\t` | 显示24小时格式时间：HH:MM:SS || `\T` | 显示12小时格式时间：HH:MM:SS
`\A` | 显示24小时格式时间：HH:MM || `\@` | 显示AM/PM格式时间：HH:MM AM/PM
`\u` | 账户名称 || `\v` | Bash版本信息
`\V` | 带修正版本的Bash版本信息 || `\w` | 完整工作目录名称
`\W` | 只列出最后一级工作目录名称 || `\#` | 当前命令窗口下达的命令序号，从1开始计数
`\!` | 历史命令数目 || `\$` | 提示字符，root用户是#，普通用户是$

`PS1="\t(^_^)\u:\#>> "`命令就将命令行提示改为了“当前时间(^_^)用户名:命令序号>> ”的格式：（需要注意的是“=”前后并无空格）

```bash
vmware@ubuntu:~/learn_Linux$ PS1="\t(^_^)\u:\#>> "
00:49:34(^_^)vmware:9>> pwd
/home/vmware/learn_Linux
00:50:02(^_^)vmware:10>> echo "Hello"
Hello
```
如果想知道自己的Bash命令行提示是怎么设置的可以运行`echo $SP1`，我的虚拟机运行结果如下：
```bash
vmware@ubuntu:~$ echo $PS1
\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$
```
### *搜索路径 PATH*
我们在运行ls时，系统知道要运行/bin目录下的ls，运行vi时，系统知道需要运行/usr/bin目录下的vi，这一切是由Linux的搜索路径决定的。运行`echo $PATH`即可查看内置环境变量PATH，知道当前搜索路径，不同目录用冒号隔开，从输出便可知道/user/bin与/bin都在搜索路径之中。通过运行`“PATH=新搜索路径”`来改变搜索路径，如果需要在原来搜索路径的基础上添加目录，我们可以运行`“PATH="$PATH:新目录"”`，这里同样也需注意“=”前后不能有空格。如果不小心改坏了PATH，直接退出这个命令窗口新开一个就好了，当然如果你在操作前对PATH进行了备份，直接恢复也是可以的。
### *设置和取消别名命令 alias、unalias*
有时候我们用的有些命令有些长，不好记也容易输错，这时我们用内置命令 alias 来给命令起别名就比较方便了，用法是 `alias 别名='命令'`，然后要运行这条命令的时候输入别名就好了。如果想要查看当前所有的别名，执行命令 `alias` 或者 `alias -p` 便可查看，只查看某一别名的定义，则执行 `alias 别名`。删除别名用内置命令 unalias 加别名，而如果要一次性删除所有别名用命令 `unalias -a`。
### *修改.bashrc——设置自己的环境*
当你修改了命令行提示、搜索路径或是别名，当你再打开一个新的shell窗口的时候，你会发现你做的修改都没用了，这是因为在主目录下有一个名为 .bashrc 的隐藏文件，它其实是一个脚本，每次进入新的 Bash shell 时（具体来说是非登陆的shell），.bashrc都会运行。如果想要每次打开的shell窗口都做了你的修改，你可以在.bashrc文件的最后加上你做修改的命令。由于文件.bashrc的内容直接影响着用户环境，所以修改之前最好先做好备份。实际上，每个用户主目录下的.bashrc文件只影响自己的Bash环境，而/etc/environment、/etc/profile及/etc/bash.bashrc的修改会影响所有用户的环境（以Ubuntu系统为例）。
## 权限掩码命令 umask
分别用mkdir与touch命令新建目录与文件，看看其初始权限是什么，我们会发现目录的初始权限是755，而文件是644。这个初始值可用内置命令umask来设置，直接运行`umask`可以显示当前的掩码值，默认是0022。所谓掩码，就是从满权限中“拿走”的权限，目录的满权限为777，文件的满权限为666。注意这里的“拿走”是拿能拿的权限，我们可以通过命令`“umask 新掩码”`来设置新掩码，如果设掩码为033，那么会发现新建文件的权限是644，而非633，这是因为3=2+1，所以它要拿走的是2与1的权限，而6=4+2，里面没有1，只能拿走2而剩下4，所以新文件的权限为644。实际上，掩码在复制时也起作用，如果掩码为默认值022，将文件a.txt的权限设置为777：`chmod 777 a.txt`，然后在执行`cp a.txt b.txt`后，新文件b.txt权限为755，即拿去了022。
## source命令和点命令
source命令和点命令是用来运行脚本的，假如有一个脚本文件file，执行`source file`或`. file`便可以运行脚本，前面提到了.bashrc文件，修改了它便设置了自己的Bash运行环境，前面说要新打开一个窗口修改才生效，现在学了这个，你便可以直接运行`source .bashrc`或`. .bashrc`让配置马上生效。
## 编写并运行第一个脚本
脚本简单来说就是由一条一条命令组成的文本文件，脚本执行时，由解释器将其一条条地翻译成机器可以识别的指令，并按程序的逻辑顺序执行。
我们先用`vim file.sh`新建并打开一个文件，输入以下内容后保存退出，这就是一个简单的脚本。“#”号开头的是注释，其中第一行指明了脚本解释器的位置，第三、四行便是需要执行的命令。
```bash
#!/bin/bash
# This is the first bash script written by me
echo "Hello!"
echo $PATH
```
我们知道文件的初始权限是644，即rw-r--r--，是没有执行权限的，所以我们还需要执行`chmod +x file.sh`给该文件增加执行权限。做完这些以后我们执行`file.sh`发现还是不能执行，并给出提示无法找到该命令。
```bash
vmware@ubuntu:~/learn_Linux$ ls -l file.sh 
-rw-r--r-- 1 vmware vmware 83 Aug  6 00:02 file.sh
vmware@ubuntu:~/learn_Linux$ chmod +x file.sh
vmware@ubuntu:~/learn_Linux$ ls -l file.sh 
-rwxr-xr-x 1 vmware vmware 83 Aug  6 00:02 file.sh
vmware@ubuntu:~/learn_Linux$ file.sh
file.sh: command not found
```
这是因为file.sh所在目录不在搜索路径中，这时如果想要执行这个脚本文件，我们有三种方法：第一种就是前面提到的source命令或点命令，当前目录不在搜索路径中时它们也能执行，其实用这两种命令时即使脚本没有执行权限也可正常执行；
```bash
vmware@ubuntu:~/learn_Linux$ source file.sh 
Hello!
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```
第二种方法是执行`./file.sh`，因为“.”表示当前目录，这条命令就指明了file.txt的位置，所以可以执行；
```bash
vmware@ubuntu:~/learn_Linux$ ./file.sh 
Hello!
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```
第三种方法是将当前路径“.”加到搜索路径\$PATH中去，然后直接执行`file.sh`就好了。以后为了方便，我就将`PATH="$PATH:."`这条命令放到了.bashrc文件的最后一行。
```bash
vmware@ubuntu:~/learn_Linux$ PATH="$PATH:."
vmware@ubuntu:~/learn_Linux$ file.sh
Hello!
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:.
```
## 命令解释顺序与命令类型的查询
### *命令的解释顺序*
内置命令、关键字、外部命令（可执行程序和脚本）、别名、函数都是可以被调用执行的，可以相互重名（前面就提到了内置命令echo与外部命令/bin/echo重名），可是当他们重名时，我们怎么知道到底执行了哪一个呢？这就需要知道Bash的命令解释顺序：alias -> keyword -> function -> builtin -> $PATH，即运行命令时系统会按如下顺序查找：别名、关键字、函数、内置命令、收索路径，找到即停止，如果最终没有找到将会给出提示P：command not found。
### *改变命令解释顺序的三条内置命令 command、builtin、enable*
command命令将只按内置命令与外部命令来处理，用法是`“command 命令”`；builtin命令只会查找内置命令来执行，用法同是builtin加命令；enable命令禁用或使能内置命令，用选项`-n`禁用内置命令，比如运行`enable -n alias`后alias内置命令再无法使用，要重新使能alias命令，需执行`enable alias`。
### *命令类型查询的命令 type*
用`“type 命令名”`来查询，可以加上选项`-t`只显示类型的单词：alias(别名)、keyword(关键词)、function(函数)、builtin(内置命令)、file(外部命令)，如果没有任何输出表示未找到该命令。
## 命令的退出状态以及命令 true、false
在一个命令或程序执行结束之后，都有一个范围在0~255的退出状态返回，非0值皆表示执行失败，127表示未找到该命令，内置变量“\$?”存储了上一条命令的退出状态，运行命令`echo $?`即可看到。特别的是，Bash有两个特殊的内置命令`true`与`false`
## 管道
竖线“|”就是管道，通过管道可以将一条命令的输出穿给另一条命令。比如说如果用`ls -l`命令查看文件信息，如果目录下文件很多，查看便不太方便，这时我们可以用管道将`ls -l`的输出传给`more`分页查看：`ls -l | more`。如果想要统计目录下文件的个数，可以运行命令：`ls | wc -w`。
## 输入输出重定向
### *标准输入与标准输出*
执行一个shell命令会自动打开标准输入文件(stdin)与标准输出文件(stdout)，分别对应终端键盘与屏幕。运行`ls -l /dev/std*`命令将会看到如下输出，其中fd是文件描述符（File Description），是进程对其打开文件的索引，stdin与stdout对应的文件描述符为0与1。如果直接执行命令`wc`而不跟文件名，将会直接从stdin获得输入，按Ctrl+D结束输入，并获得统计结果。
```bash
$ ls -l /dev/std*
lrwxrwxrwx 1 root root 15 Aug  4 22:09 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx 1 root root 15 Aug  4 22:09 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx 1 root root 15 Aug  4 22:09 /dev/stdout -> /proc/self/fd/1
```
### *输入输出重定向与输出追加重定向*
  我们可以将标准输入与标准输出的结果重定向到普通文件中，输入重定向的格式为`“命令 < 文件名”`，输出重定向的格式为`“命令 < 文件名”`。如可用`cat a.txt > b.txt`或`cat < a.txt > b.txt`完成文件的复制，用`cat a.txt b.txt c.txt > all.txt`完成文件的合并，用`ls -la > filelist.txt`将目录中所有文件与子目录的信息放到filelist.txt中。

  输出追加重定向的格式为`“命令 >> 文件名”`，它将输出接在原来文件的结尾，而输出重定向“>”会将原来的输出冲掉。如：可用`cat b.txt >> a.txt`完成文件合并，`date >> date.txt`会将新的时间追加到原文件的结尾。
### *预防输出重定向覆盖旧文件*
  如果某条指令输出重定向到某个已存在的文件，原来的文件会被覆盖，我们可以通过设置Bash中的**noclobber**选项来预防这种情况的发生。运行`set -o`查看所有选项的开启情况，如果只想查看noclobber选项的情况，可以通过管道将输出传给grep命令筛选，即运行命令`set -o | grep noclobber`，默认情况下该选项是关闭的，可以通过`set -o noclobber`或`set -C`命令打开。如果存在一个名为file.txt的文件，在打开noclobber选项后，运行`echo "abcdef" > file.txt`将无法改变file.txt的内容并会得到提示：`bash: file.txt: cannot overwrite existing file`。要想再次关闭noclobber选项，用命令`set +o noclobber`或者`set +C`。
### *标准错误输出与“黑洞”*
  前面在讲标准输入stdin与标准输出stdout运行`ls -l /dev/std*`时，不知你是否发现还有一个stderr，stderr其实就是标准错误输出文件，其文件描述符为2，用来输出错误信息到屏幕上。假如文件a.txt是不存在的，运行`ls -l a.txt`将会在屏幕上看到错误信息：`ls: cannot access 'a.txt': No such file or directory`。我们再运行`ls -l a.txt > b.txt`，将发现错误信息仍输出到屏幕上，而b.txt是个空文件，这是因为这段输出不再是标准输出，而是标准错误输出。如果要对标准错误输出进行重定向，需要用`“命令 2> 文件名”`，同样地，标准错误输出追加重定向格式为`“命令 2>> 文件名”`。

  我们可以用如下格式将两种输出分别重定向到两个文件中：`“命令 1> 文件名1 2>文件名2”`，其中“1>”可以简写为“>”。如果想将两种输出都输出到同一个文件中，可以用“2>&1”将标准错误输出重定向到标准输出，也就是标准输出重定向到哪，标准错误输出就跟到哪，用`“命令 >文件名 2>&1”`便将标准输出与标准错误输出都输出到同一个文件中了。另，用`“命令 &> 文件名”`或`“命令 >& 文件名”`可直接将两种输出重定向到同一文件。Bash4新增了符号“&>>”，它即是将标准输出与标准错误输出都追加重定向到同一文件的符号。

  某些时候，我们只需要知道正确信息，而错误信息可以忽略，这时就可以将错误信息重定向到设备文件/dev/null，这是一个空文件，也叫“**黑洞**”，任何输出到它的文件将消失得一干二净。

### *同时把结果输出到标准输出与文件的命令 tee*
命令tee通常与管道一起使用，可以将命令结果既输出到屏幕又输出到文件，格式为`“命令 | tee 文件名”`（文件中只有标准输出的内容），这种格式会将文件中原来的内容冲掉，可以加选项`-a`或`--append`实现追加。用命令`“命令1 |& 命令2”`可以将命令1的标准输出与标准错误输出一起通过管道传给命令2，所以`“命令 |& tee 文件名”`可以将两种输出都输出到文件中。
## exec命令与文件描述符
Bash的内置命令exec常用格式如下：`exec [command [arguments …]]`，exec不启动新的shell，而用被执行的命令替换当前的shell进程，且原来的进程将会被清理掉，如果在shell命令窗口输入命令`exec ls`，你将会发现ls命令才刚输出完，shel窗口便关闭了，这是因为原来的shell进程已经被ls命令替换掉了。如果脚本a.sh内容如下所示：
```bash
#!/bin/bash
echo "Hi!"
exec date
exec ls -lt
echo "Bye!"
```
当我们执行这个脚本时，发现第3、4条命令并没有执行，这是因为在执行`exec date`的时候，执行a.sh的子shell已经被date命令替换掉了。
```bash
$ a.sh
Hi!
Wed Aug  7 21:39:13 PDT 2019
```
这说明在shel窗口或脚本里面exec命令是不能随便用的，不过当exec命令用来对文件描述符操作（用于重定向）时，不会替换当前shell，其与文件描述符相关的命令如下表所示：

命令 | 作用 
:-: | :-:
`exec < file` | 将file的内容作为exec的标准输入
`exec > file` | 将exec的输出重定向到文件
`exec m< file` | 将file读入到文件描述符m中
`command <& m` | 文件描述符m作为命令的标准输入
`exec m> file` | 将写入文件描述符m的内容重定向到文件file中
`command >& m` | 命令的标准输出重定向到文件描述符m
`exec n<& m` | 创建文件描述符m的副本：文件描述符n
`exec m<& -` | 关闭文件描述符m
`exec <& -` | 关闭标准输入
`exec >& -` | 关闭标准输出

### *exec < file*
该命令将file的内容作为exec的标准输入，file可以是个脚本，脚本执行完shell终端会自动退出。
### *exec m< file 与 command <& m*
`exec m< file`命令将file读入到文件描述符m中，`command <& m`命令将文件描述符m作为命令的标准输入。运行命令`exec 4< a.txt`后会新增文件描述符4，之后运行`cat <& 4`将会把文件描述符4中的内容输出到终端屏幕上（即文件a.txt的内容）。
```
vmware@ubuntu:~/learn_Linux$ cat a.txt 
Hello
vmware@ubuntu:~/learn_Linux$ exec 4< a.txt
vmware@ubuntu:~/learn_Linux$ ls -l /dev/fd/
total 0g
lrwx------ 1 vmware vmware 64 Aug  8 04:30 0 -> /dev/pts/0
lrwx------ 1 vmware vmware 64 Aug  8 04:30 1 -> /dev/pts/0
lrwx------ 1 vmware vmware 64 Aug  8 04:30 2 -> /dev/pts/0
lr-x------ 1 vmware vmware 64 Aug  8 04:30 3 -> /proc/4672/fd
lr-x------ 1 vmware vmware 64 Aug  8 04:30 4 -> /home/vmware/learn_Linux/a.txt
vmware@ubuntu:~/learn_Linux$ cat <& 4
Hello
```
### *exec n<& m 与 exec m<& -*
运行`exec n<& m`将创建文件描述符m的副本文件描述符n，无论文件描述符m是输入文件还是输出文件，都用该命令创建副本，之后文件描述符m与文件描述符n的使用效果一样，关闭他们中的任一个，另一个仍能正常使用，用命令`exec m<& -`便可关闭文件描述符m（特别的，关闭标准输入可以用`exec <& -`，关闭标准输出可以用`exec >& -`）。
### *exec > file*
该命令将标准输出重定向到文件file，运行完这条命令后，shell窗口将再看不到任何命令的结果（还能看到标准错误输出），标准输出的结果将存放到文件中，可是当你想运行`cat file`来查看file中的内容时会失败，这是因为file是该命令的输入文件，而输出文件也重定向到了文件file，所以`cat file`的输入输出其实是同一文件，故报错。要解决这个问题，我们可以新开一个shell窗口运行`cat file`命令，也可以在当前窗口运行`exec > /dev/tty`将标准输出恢复到屏幕上。
### *exec m> file 与 command >& m*
`exec m> file`将写入文件描述符m的内容重定向到文件file中,`command >& m` 将命令的标准输出重定向到文件描述符m。如执行命令`exec 5> b.txt`后，输入到文件描述符5的内容都会重定向到文件b.txt中， 在用`command >& 5`执行命令时，命令结果都会输出到文件b.txt中。
## 有关命令的进一步讨论
### *一行多命令*
可以用分号将不同命令隔开以实现一行里面输入多条命令。
### *将命令放到后台执行*
在命令后跟上“&”可以将命令放到后台执行，命令与“&”符号之间空格可加可不加。
### *命令行的续行*
用符号“\”用在行的末尾实现命令的续行，另换一行以后，系统会出“>”作为提示符，在提示符后继续完成命令的键入。实际上，内置变量PS2便是命令续行提示符，可用`“PS2=新命令续行提示符”`修改。
```bash
$ echo "I \
> am a girl"
I am a girl
$ PS2=continue\>\>
$ echo "How ar\
continue>>e you?"
How are you?
```
### *操作名字含空格的文件*
如果文件名字中含空格，需要用单/双引号将整个文件名包起来，或者文件名中的空格前加反斜杠“\”，对这种文件操作是不方便的，所以会尽量避免在文件名中使用空格。
```bash
$ echo "Hello!" > 'a new file.txt'
$ cat a new file.txt
cat: a: No such file or directory
cat: new: No such file or directory
cat: file.txt: No such file or directory
$ cat a\ new\ file.txt
Hello!
```
### *操作名字首字母为减号的文件*
如果文件以减号“-”开头，shell将会认为其为选项，从而由于无法找到该选项而报错。要对这种文件进行操作，一种方法是在文件名前加“-- ”，这是因为Bash规定，命令行中--后跟的不会被认为是选项；另一种方法是，将该文件名写成全路径或加“./”。
```bash
$ touch -a.txt
touch: invalid option -- '.'
Try 'touch --help' for more information.
$ touch -- -a.txt
$ echo "abcdefg" > ./-a.txt 
$ cat ~/learn_Linux/-a.txt 
abcdefg
```
### *Windows 与 Linux 文件格式的互转*
有时候，我们会从Windows复制脚本到Linux中，找不到任何语法错误，却发现脚本无法运行。这是因为Windows与Linux中文件格式不同，在Window下文件换行符是“\n\r”，而Linux下却是“\n”，这个差异导致了在Window下编辑的脚本复制到Linux下会发生错误。要解决这个问题，可以用dox2unix命令将来自Windows的文件转化成Linux的格式，用法为`“dox2unix 文件名”`，相对地，要将来自Linux的文件转化到Window的文件格式就用命令unix2dox。
### *大括号与小括号中的命令*
我们可以用小括号“()”或大括号“{}”将一条或多条命令括起来，“()”中的命令将在子shell中执行，“{}”中的命令将在当前shell执行，需要注意的是大括号“{”后必须有个空格，“}”前必须有个分号。
```bash
vmware@ubuntu:~$ pwd
/home/vmware
vmware@ubuntu:~$ (cd learn_Linux/)
vmware@ubuntu:~$ pwd
/home/vmware
vmware@ubuntu:~$ { cd learn_Linux/;}
vmware@ubuntu:~/learn_Linux$ pwd
/home/vmware/learn_Linux
```

<div STYLE="page-break-after: always;"></div>