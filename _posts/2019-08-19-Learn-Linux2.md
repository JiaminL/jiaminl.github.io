# 变量与数组

[TOC]

## 变量的定义
### *一般变量的定义与清除方法*

Bash的变量无需事先申明类型便可直接赋值，实际上Bash的变量本质上都是字符串。变量名中只能有字母数组下划线，变量定义时用命令`“变量名=值”`，注意“=”前后不能有空格，引用时在变量名前加美元符号“$”。

```bash
$ name="Tom"
$ echo name
name
$ echo $name
Tom
```
对于没有定义的变量也可以直接引用，不过其值为空，如果定义时等号后面没有输入内容而直接回车（或输入的只有空格），其值也为空。不过定义了的能在变量表中查到其值，运行`set`便可以查看所有变量与函数的定义。可用命令`“unset 变量名”`来清除对变量的定义。
### *字符串定义及单双引号与大括号的使用*
在定义字符串变量的时候，如果字符串中含有空格，可以在空格前加反斜杠“\”，也可以用引号将有空格的地方包起来，其中双引号内的变量引用（“$”+“变量名”）会将变量值代入，而单引号中的变量引用不会代入。
```bash
$ echo Good morning
Good morning
$ a=Good morning
morning: command not found
$ a=Good\ morning; echo $a
Good morning
$ b="Hi! $a"; echo $b
Hi! Good morning
$ c='Hi! $a'; echo $c
Hi! $a
```
如果要将一个变量（如A）与一个以下划线开头的字符串（如“_bb”）连在一起，则需要将变量名用大括号括起来，否则系统会认为“A_bb”是变量名，而变量名“A_bb”没有被定义过，所以其值为空。要解决这个问题，可以用大括号{}将变量名括起来，也可以用引号将含下划线的部分包起来。
```bash
$ A=aa
$ echo "$A_bb cc"
 cc
$ echo "${A}_bb cc"
aa_bb cc
$ echo $A"_bb cc"
aa_bb cc
```
如果字符串中含有引号，则应该用另一种引号将字符串括起来，或者在该引号前加反斜杠。
```bash
$ echo "I'm Tom"
I'm Tom
$ echo I'm Tom
> '
Im Tom

$ echo "I'm Tom"
I'm Tom
$ echo I\'m Tom
I'm Tom
```
大括号除了作为变量名的定界符，还有扩展功能。使用该功能时将一些字符串以逗号间隔，放在大括号中间。左大括号前可以有其他字符串，叫做前导；同样右大括号后的字符串叫后继。其功能用如下例子说明：
```bash
$ echo a{b,c,d}e
abe ace ade
$ echo a{b,c,d{1,11,111}}e
abe ace ad1e ad11e ad111e
```
### *整形变量的定义*

可以用带`-i`(integer)选项的`declare`命令来使得变量带有整数性质。Bash的变量本质上都是字符串，所以当你尝试直接将45+2赋值给变量M以期望M得到计算结果47时，你会发现M的值只会是字符串45+2，后面我们会将学习用`$()`或`let`命令来进行整形运算，在这里将事先声明变量类型为整形，从而使变量可以直接运算。

```bash
$ M=45+2; echo $M
45+2
$ declare -i M; echo "before: $M"; M=45+2; echo "after: $M"
before: 45+2
after: 47
```

如果将字符串赋值给整形变量，若该字符串中没有空格其值为0，否则报错。

```bash
$ declare -i A="word"; echo $A
0
$ declare -i B="a word"; echo $B
bash: declare: a word: syntax error in expression (error token is "word")
```

可以用`“变量=基数#数值”`的形式以其他进制形式（二进制~六十四进制）给整形变量赋值，也可以用0开头的表示八进制，0x开头的表示十六进制。

```bash
$ declare -i D=2#1100; echo $D
12
$ D=0100; echo $D
64
$ D=0x100; echo $D
256
```
### *只读变量的定义与清除*

可以用内置命令`readonly`给变量设置只读属性，也可以通过带选项`-r`(readonle)的`declare`或`typeset`命令来定义只读变量。运行`readonly -p`或`declare -rp`可以输出所以的只读变量，只读变量不允许用`unset`命令来取消定义。

```bash
$ readonle a=20
readonle: command not found
$ a=10
$ readonly a=20
$ a=10
bash: a: readonly variable
$ unset a
bash: unset: a: cannot unset: readonly variable
$ declare b=2
$ b=50
$ declare -r b=2
$ b=50
bash: b: readonly variable
```

如要对删除只读变量，可运行如下命令删除前面定义的只读变量a（在网上找的，笔者并不太清楚含义）。

```bash
$ cat << EOF | gdb
> attach $$
> print (int)unbind_variable("a")
> detach
> EOF
```
### *数组的定义与清除*

Bash只支持一维数组，不支持多维数组。数组定义可以用`“变量名=(以空格分隔的数组元素的值)”`来定义（其下标默认从0开始）；也可通过`“变量名=([下标1]=值1 )”`用`“declare -a 数组名”`来定义数组，然后再通过`“数组名[下标]=数组元素的值”`来对每个元素进行定义（下标可以不从0开始）。我们引用数组元素时可以用`“${数组名[下标]}”`(用`“${数组名[*]}”`或`“${数组名[@]}”`将得到数组的所有元素)，需要注意的是大括号不能省，否则匹配到的是`“$数组名”`而得到数组的第0号元素的值。可以通过`“${#数组名[*]}”`（或`“${#数组名[@]}”`）获得数组元素个数，通过`“${!数组名[*]}”`（或`“${!数组名[@]}”`）获得所有元素的下标。（带“*”号命令是将所有内容作为一个字符串进行输出，而带“@”号的输出是一系列以空白字符分隔的独立字符串）

```bash
$ a=(10 20 30 40)
$ echo ${a[0]}, ${a[1]}, ${a[2]}, ${a[3]}
10, 20, 30, 40
$ echo $a[0], $a[1], $a[2], $a[3]
10[0], 10[1], 10[2], 10[3]
$ declare -a b
$ b[3]=20
$ b[2]=98
$ echo ${!b[*]}; echo ${b[*]}
2 3
98 20
```

要想添加元素，可以直接定义：`“数组名[下标]=值”`。可用`“unset 数值名[下标]”`来删除某一数组元素，如果想要删除整个数组，则运行`“unset 数组名”`。可用`“readonly 数组名”`或`“declare -ar 数组名”`来定义只读数组，同样，只读数组也不可以用`unset`命令来取消定义。用命令`“declare -ap 数组名”`可以显示所有数组

可用带`-p`选项的`declare`命令来显示变量属性与值。因为Bash中所有变量本质为字符串，所以其输出都带有双引号。

```bash
$ declare -p a
declare -a a=([1]="34" [4]="43" [5]="333")
```

### *关联数组的定义*

 前面我们数组的下标是整数，实际上Bash4以上的版本数组下标可以不是整数了，这样的数组称为关联数组，也称为哈希或者映射，而前面所介绍的数组其实称为索引数组。用带`-A`选项的`declare`命令可以定义关联数组，不过其方括号中的内容不再称为下标，而称为键(key)，关联数组的使用与索引数组的使用方法类似。

```bash
$ declare -A age=([Tom]=10 [Jake]=23 [Amy]=3 [Mary]=56)
$ declare -p age
declare -A age=([Jake]="23" [Tom]="10" [Mary]="56" [Amy]="3" )
$ echo ${!age[@]}
Jake Tom Mary Amy
```

## 键盘输入变量值——内置命令read

运行命令`“read 变量名”`可以通过键盘输入对变量进行赋值，read命令可一次读取多个变量的值：`“read 变量名1 变量名2 ……”`。如果read后面没有跟变量名，默认存到内置变量REPLY中。read命令可用`-a`选项创建数组并通过键盘输入获得数组元素的值，变量用空格隔开；可用`-p`选项来在键盘输入前给用户提示；选项`-t`跟数字表示进行计时输入，如用`-t 5`表示5秒后若没有键盘输入便超时退出；选项`-n`加整数用于设定能输入的最大字符数；选项`-s`将使得read命令进入silent模式，键盘输入不显示在屏幕上，常用于密码的输入。

```bash
$ read a b c
123 Hello world !!
$ echo $a, $b, $c
123, Hello, world !!
$ read -a arr
23 43 123 65 53
$ echo ${!arr[*]}; echo ${arr[@]}
0 1 2 3 4
23 43 123 65 53
$ read -p "please input your name: " name
please input your name: Amy 
$ echo $name
Amy
$ read -s -p "please input password: " password
please input password: $ echo $password
kldasfha187f
```
## 引用命令执行结果的反引号与$()
用``“`命令`”``或`“$(命令)”`来引用命令的执行结果，可以将其赋值给变量也可将其放到另一条命令中去。反引号与\$()的作用相同，但是\$()容易嵌套，如命令`time=$(echo$(date))`。可以将反引号或​\$()放在单双引号之间，在单引号中时，反引号或\$()中的命令不执行，而在双引号中会被执行。
```bash
$ echo Now,time is `date`
Now,time is Tue Aug 13 19:51:33 PDT 2019
$ time=$(echo $(date))
$ echo $time
Tue Aug 13 19:52:06 PDT 2019
$ echo '$(date)'
$(date)
$ echo "$(date)"
Tue Aug 13 20:21:40 PDT 2019
```
## 数值运算
### *整形运算*
整形的运算应该放在双括号之间或内置命令let之后。因为Bash中只有整形，没有实型数，所以除法运算结果的小数部分会被直接丢弃。

```bash
$ a=30 b=50
$ w=a+b; echo $w
a+b
$ echo $a+$b
30+50
$ ((w=a+b)); echo $w
80
$ let m=b/a; echo $m
1
$ let m=100/13; echo $m
7
$ n=((100+7))
bash: syntax error near unexpected token `('
$ n=$((100+7)); echo $n
107
```

Bash支持的运算符如下表所示：

| 运算符                      | 意义 |
| :-------------------------: | :--: |
| +    -    *    /    %    ** | 加、减、乘、除、取余、幂 |
|!    ~|逻辑取反、位取反|
|++    --|自增、自减|
|<    <=    >    >=|大小比较|
|&    \|    ^|位与、位或、位异或|
|<<    >>|左移、右移|
|&&    \|\||逻辑与、逻辑或|
|?:|条件运算符|
|+=    -=    *=    /=    %=    &=    ^=    <<=    >>=    \|=|赋值运算符|

### *浮点运算*

如要在shell中实现浮点运算，可以使用工具bc。可以通过管道将式子传给bc，也可直接运行`bc`进入bc直接输入要运算的式子。

```bash
$ echo "scale=8; 17/3" | bc
5.66666666
$ bc
bc 1.07.1
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'. 
scale=4
9/7
1.2857
sqrt(3)
1.7320
```

## 导出变量命令 export、declare -x

在当前shell下定义一个变量，可是当我们运行`bash`进入子shell中并尝试使用该变量时，发现变量没有定义。这里要对下面的最后一条命令`(echo $a)`做一下解释，我们前面说过在小括号中的命令是在子shell中运行的，可是我们发现，命令`(echo $a)`还是将父shell中定义的值输出了，这是为什么呢？实际上，父shell在对`(echo $a)`命令进行解析的时候，便把$a给替换成"123"了，于是让子shell执行的命令实际上是`echo 123`。

```bash
$ a=123
$ bash
$ echo $a

$ exit
exit
$ echo $a
123
$ (echo $a)
123
```

我们可以使用内置命令`export`来让子shell继承父shell的变量定义，即导出该变量，其格式为：`“export 变量名”`或`“export 变量名=值”`，在shell终端中运行脚本时，脚本中的命令其实也是在一个子shell中运行的，用如下例子说明export命令的使用。命令`declare -x`的作用与`export`的一样，所以在这不多做阐述。需要知道的是，变量导出后其作用域可不止当前shell与其子shell，还包括子shell的子shell，子shell的子shell的子shell，……，”层层穿透“。

```bash
$ cat test.sh
#! /bin/bash
echo name=$name
$ test.sh
name=
$ name=Tom; test.sh
name=
$ export name; test.sh
name=Tom
```

要取消变量的导出，用命令`”export -n 命令名“`。用命令`export -p`或`declare -xp`可以显示当前shell的所有已经导出的变量。

## 环境变量

前面提到的PS1、PS2、PATH都是环境变量，运行`env`或者`printenv`命令可以显示环境变量