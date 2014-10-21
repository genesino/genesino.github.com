---
title: bash 字符串处理
author: 悟道
layout: post
categories:
  - bash
  - 转载
tags:
  - bash
---

[精华] bash 编程  
http://www.chinaunix.net 作者:jiupima 发表于：2005-07-22 16:29:17

Bash 编程

## <span style="text-decoration: underline;">一． Bash特殊字符</span>

1． 通配符：  
**：匹配任何字符串  
?：匹配任何单个字符*  
集合运算符：用一些单个字、一个连续范围或断续的字符集合作为通配符  
*[set]：用字符集合作通配符匹配单个字符，如：[aeiou]，[a-o]，[a-h, w-z]  
[!set]：除了集合外的所有字符组成的集合作通配符*  
2． 花括号展开式（可以嵌套）：  
格式：\[前导字符串]{字符串1[{嵌套字符串1…}\] \[, 字符传2…\]}[后继字符串]  
**如：c{a{r, t, n}, b{r, t, n}}s 就等于 cars cats cans cbrs cbts cbns**  
3． 其它特殊字符：  
( ：子shell开始，子shell继承父shell部分环境变量  
) ：子shell结束  
{ ：命令块开始，由当前shell执行，保留所有环境变量  
} ：命令块结束  
\ ：引用后面的单个字符  
‘ ：强引用字符串，不解释特殊字符  
“ ：弱引用字符串，解释所有特殊字符  
\` ：命令替换  
; ：命令分隔符（命令终止符），运行在一行里执行多条命令  
\# ：行注释  
$ ：变量表达式  
& ：在后台执行命令

## 二． Bash变量

### 1． 自定义变量

用户自定义的变量由字母、数字和下划线组成，并且变量名的第一个字符不能为数字，且变量名大小写敏感。  
varname=value **注意bash不能在等号两侧留空格**  
shell语言是非类型的解释型语言，给一个变量赋值实际上就是定义了变量，而且可以赋不同类型的值。引用变量有两种方式，$varname和${varname}，为防止变量在字符串中产生歧义建议使用第二种方式，引用未定义的变量其值为空。  
为一个变量赋值一个串，需要用到引号，注意\`、’、”的不同，&#8220;相当于$()  
为了使变量可以在其它进程中使用，需要将变量导出：export varname

### 2． 环境变量

可以用set命令给变量赋值或查看环境变量值，使用unset命令清除变量值，使用export导出变量将可以使其它进程访问到该环境变量。**可以把设置保存到.bashrc中，成为永久的环境变量**

### 3． 位置变量

### 位 置变量对应于命令行参数，其中$0为脚本名称，$1为第一个参数，依次类推，参数超过9个必须使用${}引用变量。shell保留这些变量，不允许用户以 另外的方式定义它们，传给脚本或函数的位置变量是局部和只读的，而其余变量为全局的（可以用local关键字声明为局部）。

### 4． 其它变量

$? ：保存前一个命令的返回码  
$- ：在Shell启动或使用set命令时提供选项  
$$ ：当前shell的进程号  
$! ：上一个子进程的进程号  
**$# ：传给脚本或函数的参数个数，即位置变量数减1，<span style="color: #ff0000;">不含脚本名称</span>**。  
**$* ：传给脚本或函数的参数组成的单个字符串，即除脚本名称后从第一个参数开始的字符串，每个参数以$IFS分隔（一般内部域分隔符$IFS为1空格）**。形同”…”  
<span style="color: #ff0000;">$</span><span style="color: #ff0000;">@ ：传给脚本或函数的参数列表，这些参数被表示为多个字符串</span>。形同”” “” “”…。

$*和$@之间的不同方便使用两种方法处理命令行参数，但是在打印时参数外观没有区别。  
如： #vi posparm.sh  
function cutparm  
{echo –e “inside cntparm: $# parms: $*\n”}  
cntparm “$*”  
cntparm “$@”  
#./posparm.sh abc bca cab  
inside cntparm: 1 parms: abc bca cab  
inside cntparm: 3 parms: abc bca cab

## 三． Bash操作符

### 1． 字符串操作符（替换操作符）

${var:-word} 如果var存在且不为空，返回它的值，否则返回word  
${var:=word} 如果var存在且不为空，返回它的值，否则将word赋给var， 返回它的值  
${var:+word} 如果var存在且不为空，返回word，否则返回空  
${var:?message} 如果var存在且不为空，返回它的值，  
否则显示“bash2:$var:$message”，然后退出当前命令或脚本  
${var:offset[]} 从offset位置开始返回var的一个长为length的子串，  
若没有length，则默认到var串末尾

### **2． 模式匹配操作符**

**${var#pattern} 从var头部开始，删除和pattern匹配的最短模式串，然后返回 剩余串  
${var##pattern} 从var头部开始，删除和pattern匹配的最长模式串，然后返回 剩余串，basename path＝${path##*/}  
${var%pattern} 从var尾部开始，删除和pattern匹配的最短模式串，然后返回 剩余串，dirname path＝${path%/*}  
${var%%pattern} 从var尾部开始，删除和pattern匹配的最长模式串，然后返回 剩余串  
${var/pattern/string} 用string替换var中和pattern匹配的最长模式串**

## 四． Shell中条件和test命令

Bash可以使用[ … ]结构或test命令测试复杂条件  
格式：[ expression ] 或 test expression  
返回一个代码，表明条件为真还是为假，返回0为真，否则为假。  
注：左括号后和右括号前空格是必须的语法要求

### 1． 文件测试操作符

-d file file存在并且是一个目录  
-e file file存在  
-f file file存在并且是一个普通文件  
-g file file存在并且是SGID(设置组ID)文件  
-r file 对file有读权限  
-s file file存在并且不为空  
-u file file存在并且是SUID(设置用户ID)文件  
-w file 对file有写权限  
-x file 对file有执行权限，如果是目录则有查找权限  
-O file 拥有file  
-G file 测试是否是file所属组的一个成员  
-L file file为符号链接  
<span style="color: #ff0000;"><span style="text-decoration: underline;">file1 –nt file2 file1比file2新<br /> file1 –ot file2 file1比file2旧</span></span>

### 2． 字符串操作符

str1=str2 str1和str2匹配  
str1!=str2 str1和str2不匹配  
str1>str2 str1大于str2  
<span style="text-decoration: underline;">-n str str的长度大于0（不为空）<br /> -z str str的长度为0（空串）</span>

### 3． 整数操作符

var1 –eq var2 var1等于var2  
var1 –ne var2 var1不等于var2  
var1 <span style="color: #ff0000;">–ge </span>var2 var1大于等于var2  
var1 <span style="color: #ff0000;">–gt </span>var2 var1大于var2  
var1 –le var2 var1小于等于var2  
var1 –lt var2 var1小于var2

### 4． 逻辑操作符

<span style="color: #ff0000;">!expr 对expr求反</span>  
expr1 && expr2 对expr1与expr2求逻辑与，当expr1为假时不再执行expr2  
expr1 || expr2 对expr1与expr2求逻辑或，当expr1为真时不再执行expr2  
注：另一种逻辑操作符 逻辑与expr1 –a expr2 逻辑或expr1 –o expr2

## 五． Shell流控制

### 1． 条件语句：if

if 条件 IFS=:  
then for dir in $PATH  
语句 do  
[elif 条件 if [ -O dir ]; then  
语句] echo –e “\tYou own $dir”  
[else else  
语句] echo –e “\tYou don’t own $dir”  
fi fi

### 2． 确定性循环：for done

for value in list for docfile in /etc/\* /usr/etc/\*  
do do  
statements using $value cp $docfile ${docfile%.doc}.txt  
done done  
注：for var;… 相当于for var in “$@”;…

### 3． 不确定性循环：while和until

while 条件 until 条件  
do do  
语句 语句  
done done

count＝1 count＝1  
while [ -n “$\*” ] until [ -z “$\*” ]  
do do  
echo &#8220;parameter $count&#8221; echo &#8220;parameter $count&#8221;  
shift shift  
count=&#8217;expr $count + 1&#8242; count=&#8217;expr $count + 1&#8242;  
done done  
条件为真执行循环体 条件为假执行循环体  
注：整数变量的定义与算法  
declare –i idx 定义整数变量 使用$(())无需定义  
idx=1  
while [ $idx!=150 ]  
do  
cp somefile somefile.$idx  
idx=$idx+1 整数算法 idx=$(( $idx+1 ))  
done  
<span style="color: #ff0000;">另一种算法 echo $(( 100/3 )) 将加减乘除表达式放入$(())中</span>

### 4． 选择结构：case和select

case 表达式 in 表达式和模式依次比较，执行第一个匹配的模式  
模式1) ;;使程序控制流跳到esac后执行，相当于break  
语句;; 允许表达式和含有通配符的模式进行匹配  
模式2)  
语句;;  
……  
[*)  
语句]  
esac

select value [ in list ] 按list列表自动生成菜单  
do 若没有list则默认为位置变量  
statements using $value  
done  
如：IFS=: 设置域分隔符为:号  
PS3=”choice>;” 改变select默认提示符  
clear  
select dir in $PATH  
do  
if [ $dir ]; then  
cnt=$(ls –Al $dir | wc -l)  
echo “$cnt files in $dir”  
else  
echo “No such choice !”  
fi  
echo –e “\npress ENTER to continue, CTRL-C to quit”  
read 使程序按回车继续，ctrl＋c退出  
clear  
done

### 5． 命令shift

<span style="color: #ff0000;">将存放在位置变量中的命令行参数依次向左传递<br /> shift n 命令行参数向左传递n个串</span>

## 六． Shell函数

定义： function fname fname ()  
{ {  
commands commands  
} }  
调用：fname [ parm1 parm2 parm3 ... ]  
说明： 函数在使用前定义，两种定义功能相同  
函数名和调用函数参数成为函数的位置变量  
函数中的变量应该使用local声明为局部变量

## 七． 输入输出

限于篇幅，这里不讨论所有输入输出操作符和功能  
1．I/O重定向  
< ：输入重定向 >; ：输出重定向(没有文件则创建，有则覆盖)  
>;>; ：输出重定向(没有则创建，有则追加到文件尾部)  
<< ：输入重定向(here文档)  
格式： command << label  
input…  
label  
说明： 使一个命令的输入为一段shell脚本(input…)，直到标号(label)结束  
如： cat < $HOME/.profile >; out  
echo “add to file end !” >;>; $HOME/.profile  
ftp：USER=anonymous  
PASS=YC@163.com  
ftp –i –n << END -i：非交互模式 -n：关闭自动登录  
open ftp.163.com  
user $USER $PASS  
cd /pub  
close  
END

END标记输入结束  
2．字符串I/O操作  
字符串输出：echo  
命令选项： -e：启动转义序列 -n：取消输出后换行  
转义序列：

\a：Alt/Ctrl+G(bell)

\b：退格Backspace/Ctrl+H

\c：取消输出后换行

\f：Formfeed/Ctrl+J

\r：Return/Ctrl+M

\v：Vertical tab

\n：八进制ASCII字符

\\：单个\字符

\t：Tab制表符  
<span style="color: #ff0000;">字符串输入：read<br /> 可以用于用户交互输入，也可以用来一次处理文本文件中的一行</span>

命令选项：

-a：将值读入数组，数组下标从0开始  
-e：使用GNU的readline库进行读入，允许bash的编辑功能  
-p：在执行读入前打印提示  
如： IFS=:  
read –p “start read from file . filename is : \c” filename  
while read name pass uid gid gecos home shell < filename do echo –e “name : $name\npass : $pass\n” done 说明：如果行的域数大于变量表的变量数，则后面的域全部追加给最后的变量

## 八． 命令行处理 命令行处理命令：

getopts 有两个参数，第一个为字母和冒号组成的选项列表字符串，第二个为一个变量名

选项列表字符串以冒号开头的选项字母排列组成，如果一选项需要一个参数则该选项字母后跟一个冒号

getopts分解第一参数，依次将选项摘取出来赋给第二个参数变量

如果某选项有参数，则读取参数到内置变量OPTARG中 内置变量OPTIND保存着将被处理的命令行参数（位置参数）的数值 选项列表处理完毕getopts返回1，否则返回0 如：

while getopts “:xy:z:” opt do case $opt in x) xopt=’-x set’ ;; y) yopt=”-y set and called with $OPTARG” ;; z) zopt=”-z set and called with $OPTARG” ;; \?) echo ‘USAGE: getopts.sh \[-x\] \[-y arg\] [-z arg] file…’ exit 1 esac done shift ($OPTING-1) 将处理过的命令行参数移去 echo ${xopt: -‘did not use –x‘} echo ${yopt: -‘did not use –y‘} echo ${zopt: -‘did not use –z‘} echo “Remaining command-line arguments are :” for f in “$@” do echo –e “\t$f\n” done

## 九． 进程和作业控制

信号处理命令：trap 格式：trap command sig1 sig2 … trap可以识别30多种信号，如中断(Ctrl+c)、挂起(Ctrl+z)等，

可以使用kill -l查看信号清单 当脚本接受到信号sig1、sig2等，

trap就执行命令command，command完成后脚本重新执行 信号可以通过名称或数字来标识

作业控制命令：bg、fg

bg：显示后台进程，即用Ctrl+z挂起或‘命令 &’执行的进程

fg：将后台进程转到前台执行

kill –9 %n：杀掉第n个后台进程

附录：

一． Bash支持的命令行参数

-a 将所有变量输出

-c &#8220;string&#8221;从string中读取命令

-e 使用非交互式模式

-f 禁止shell文件名产生

-h 定义

-i 交互式模式

-k 为命令的执行设置选项

-n 读取命令但不执行

-r 受限模式

-s 命令从标准输入读取

-t 执行一命令，然后退出shell

-u 在替换时，使用未设置的变量将会出错

-v 显示shell的输入行

-x 跟踪模式，显示执行的命令 许多模式可以组合起来用,使用set可以设置或取消shell的选项来改变shell环境。

<span style="color: #ff0000;">打开选项用&#8221;-&#8221;,关闭选项用&#8221;+&#8221;,</span>

若显示Shell中已经设置的选项，执行: $echo $-

二． .profile中shell的环境变量意思如下:

CDPATH 执行cd命令时使用的搜索路径

HOME 用户的home目录

IFS 内部的域分割符，一般为空格符、制表符、或换行符

MAIL 指定特定文件(信箱)的路径，有UNIX邮件系统使用 PATH 寻找命令的搜索路径(同dos的config.sys的 path)

PS1 主命令提示符，默认是&#8221;$&#8221; PS2 从命令提示符，默认是&#8221;>;&#8221;  
TERM 使用终端类型  
================================================================================  
from: http://unix-cd.com/unixcd12/article_view.asp?id=4440

**字符串长度**

**%x=&#8221;abcd&#8221;**  
** #方法一**  
** %expr length $x**  
** 4**  
** # 方法二**  
** %echo ${#x}**  
** 4**  
** # 方法三**  
** %expr &#8220;$x&#8221; : &#8220;.*&#8221;**  
4  
\# expr 的帮助  
\# STRING : REGEXP anchored pattern match of REGEXP in STRING

### 查找子串

<span style="color: #ff0000;">%expr index $x &#8220;b&#8221;</span>  
2  
<span style="color: #ff0000;">%expr index $x &#8220;a&#8221;</span>  
1  
<span style="color: #ff0000;">%expr index $x &#8220;b&#8221;</span>  
2  
%expr index $x &#8220;c&#8221;  
3  
%expr index $x &#8220;d&#8221;  
4  
得到子字符串

\# 方法一  
\# expr startpos length  
%expr substr &#8220;$x&#8221; 1 3  
abc  
%expr substr &#8220;$x&#8221; 1 5  
abcd  
%expr substr &#8220;$x&#8221; 2 5  
bcd  
<span style="color: #ff0000;"># 方法二<br /> # ${x:pos:lenght}<br /> %echo ${x:1}<br /> bcd<br /> %echo ${x:2}<br /> cd<br /> %echo ${x:0}</span>  
abcd  
%echo ${x:0:2}  
ab  
%pos=1  
%len=2  
%echo ${x:$pos:$len}  
bc  
匹配正则表达式

<span style="color: #ff0000;"># 打印匹配长度<br /> %expr match $x &#8220;.&#8221;<br /> 1<br /> %expr match $x &#8220;abc&#8221;<br /> 3<br /> %expr match $x &#8220;bc&#8221;<br /> 0</span>  
字符串的掐头去尾

%x=aabbaarealwwvvww  
%echo &#8220;${x%w*w}&#8221;  
aabbaarealwwvv  
%echo &#8220;${x%%w*w}&#8221;  
aabbaareal  
%echo &#8220;${x##a*a}&#8221;  
lwwvvww  
%echo &#8220;${x#a*a}&#8221;  
bbaarealwwvvww  
其中 , # 表示掐头， 因为键盘上 # 在 $ 的左面。  
其中 , % 表示%， 因为键盘上 % 在 $ 的右面。  
单个的表示最小匹配，双个表示最大匹配。  
也就是说，当匹配的有多种方案的时候，选择匹配的最大长度还是最小长度。

字符串的替换

**<span style="color: #ff0000;">%x=abcdabcd<br /> %echo ${x/a/b} # 只替换一个<br /> bbcdabcd<br /> %echo ${x//a/b} # 替换所有</span>  
<span style="color: #ff0000;">bbcdbbcd</span>**  
不可以使用 regexp ， 只能用 * ? 的文件扩展方式。

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

<center>
  </p> <h1>
    Bash String Processing
  </h1>
  
  <p>
    </center>
  </p>
  
  <h2>
    Introduction
  </h2>
  
  <p>
    Over the years, the <tt>bash</tt> shell has acquired lots of new bells and whistles. Some of these are very useful in shell scripts; but they don&#8217;t seem well known. This page mostly discusses the newer ones, especially those that modify strings. In some cases, they provide useful alternatives to such old standbys as <tt>tr</tt> and <tt>sed</tt>, with gains in speed. The general theme is avoiding pipelines.
  </p>
  
  <p>
    In the examples below, I&#8217;ll assume that <tt>string</tt> is a shell variable that contains some character string. It might be as short as a single character, or it might be the contents of a whole document.
  </p>
  
  <h2 id="case">
    Case Conversions
  </h2>
  
  <p>
    One of the obscure enhancements that can be discovered by reading the <tt>man</tt> page for <tt>bash</tt>is the case-conversion pair:
  </p>
  
  <pre>	newstring=${string^^}	# the string, converted to UPPER CASE
	newstring=${string,,}	# the string, converted to lower case</pre>
  
  <p>
    (You can also convert just the <em>first</em> letter by using a <em>single</em> <tt>^</tt> or <tt>,</tt> .) Notice that the original variable, <tt>string</tt>, is not changed.
  </p>
  
  <p>
    Normally, we think of doing this by using the <tt>tr</tt> command:
  </p>
  
  <pre>	newstring=`echo "$string" | tr '[a-z]' '[A-Z]'`
	newstring=`echo "$string" | tr '[A-Z]' '[a-z]'`</pre>
  
  <p>
    Of course, that involves spawning a new process. Actually, as the <tt>man</tt> page for <tt>tr</tt>tells you, this isn&#8217;t optimal; depending on your locale setting, you might get unexpected results. It&#8217;s safer to say
  </p>
  
  <pre>	newstring=`echo "$string" | tr '[:lower:]' '[:upper:]'`
	newstring=`echo "$string" | tr '[:upper:]' '[:lower:]'`</pre>
  
  <p>
    Using <tt>tr</tt>is certainly more readable; but it also takes a lot longer to type. How about execution time?
  </p>
  
  <h3>
    Timing tests
  </h3>
  
  <p>
    Here&#8217;s a code snippet that does nothing, a hundred thousand times:
  </p>
  
  <pre>	str1=X

	i=0
	time (
	while [ $i -lt 100000 ]
	do
		let i++
	done
	)</pre>
  
  <p>
    On my machine — currently, a 3 GHz (6000 bogomips) dual-core Pentium box — that takes about 1.57 seconds. That&#8217;s the <tt>bash</tt>overhead for running the useless loop. Nearly all of that is “user” time; the “sys” time is only a few dozen milliseconds.
  </p>
  
  <p>
    Now let&#8217;s add the line
  </p>
  
  <pre>	str2=${str1^^}</pre>
  
  <p>
    to the loop, just after the <tt>let</tt>statement. The execution time jumps to about 2.3 seconds; so executing the added line 100,000 times took about 0.7 second. That&#8217;s about 7 microseconds per execution.
  </p>
  
  <p>
    Now, let&#8217;s try putting the line
  </p>
  
  <pre>	str2=`echo "$str1" | tr '[:lower:]' '[:upper:]'`</pre>
  
  <p>
    inside the loop instead. The execution time is now a whopping 1<sup>m</sup> 33<sup>s</sup> of real time — but only 3 seconds of user and 7 sec of system time! Apparently, the system gives both <tt>bash</tt> and <tt>tr</tt>a thousand one-millisecond time-slices a second, and then takes a vacation until the next round millisecond comes up.
  </p>
  
  <p>
    If we try to even things up a bit by making the initial string longer, we find practically the same times for the version using <tt>tr</tt>, but about 0.2 second longer than before for the all-shell version, if the string to convert is <tt>"Hello, world!"</tt>. Clearly, we need a really big string to measure <tt>bash</tt>&#8216;s speed accurately.
  </p>
  
  <p>
    So let&#8217;s initialize the original string with the line
  </p>
  
  <pre>	str1=`cat /usr/share/dict/american-english`</pre>
  
  <p>
    which is a text file of 931708 characters. For this big file, a single cycle through the loop is enough: it takes <tt>bash</tt> about 45.7 seconds, all but a few milliseconds of which is “user” time. On the other hand, the <tt>tr</tt>version takes only 0.24 seconds to process the big text file.
  </p>
  
  <p>
    Clearly, there&#8217;s a trade-off here that depends on the size of the string to be converted. Evidently, the context switch required to invoke <tt>tr</tt> is the bottleneck when the string is short; but <tt>tr</tt> is so much more efficient than <tt>bash</tt> in converting big strings that it&#8217;s faster when the string exceeds a few thousand characters. I find my machine takes about 1.55 milliseconds to process a string about 4100 characters long, regardless of which method is used. (About a quarter of a millisecond is used by the system when <tt>tr</tt> is invoked; presumably, that&#8217;s the time required to set up the pipeline and make the context switch.)
  </p>
  
  <h2 id="sed">
    <tt>sed</tt>-like Substitutions
  </h2>
  
  <p>
    Likewise, you can often make <tt>bash</tt> act enough like <tt>sed</tt>to avoid using a pipeline. The syntax is
  </p>
  
  <pre>	newstring=${oldstring/pattern/replacement}</pre>
  
  <p>
    Notice that there is no trailing slash, as in <tt>sed</tt> or <tt>vi</tt>: the closing brace terminates the substitution string.
  </p>
  
  <p>
    The catch is that only shell-type patterns (like those used in pathname expansion) can be used, not the elaborate regular expressions recognized by <tt>sed</tt>. Also, only a single replacement normally occurs; but you can simulate a “global” replacement by using <em>two</em> slashes before the pattern:
  </p>
  
  <pre>	newstring=${oldstring//pattern/replacement}</pre>
  
  <p>
    A handy use for this trick is in sanitizing user input. For example, you might want to convert a filename into a form that&#8217;s safe to use as (part of) a shell-variable name: filenames can contain hyphens and other special characters that are not allowed in variable names, which can only be alphanumeric. So, to clean up a dirty string:
  </p>
  
  <pre>	clean=${dirty//[-+=.,]/_}</pre>
  
  <p>
    If we had set <tt>dirty='a,b.c=d-e+f'</tt>, the line above converts the dangerous characters to underscores, forming the clean string: <tt>a_b_c_d_e_f</tt>, which can be used safely in a shell script.
  </p>
  
  <p>
    And you can omit the replacement string, thereby deleting the offensive parts entirely. So, for example,
  </p>
  
  <pre>	cleaned=${dirty//[-+=.,]}</pre>
  
  <p>
    is equivalent to
  </p>
  
  <pre>	cleaned=`echo $dirty | sed -e 's/[-+=.,]//g'`</pre>
  
  <p>
    or
  </p>
  
  <pre>	cleaned=`echo $dirty | tr -d '+=.,-'`</pre>
  
  <p>
    where we have to put the hyphen last so <tt>tr</tt>won&#8217;t think it&#8217;s an option.
  </p>
  
  <p>
    Be careful: <tt>sed</tt> and <tt>tr</tt> allow the use of ranges like <tt>'A-Z'</tt> and <tt>'0-9'</tt> ; but <tt>bash</tt> requires you to either enumerate these, or to use character classes like <tt>[:upper:]</tt> or <tt>[:digit:]</tt> <em>within</em> the brackets that define the pattern list.
  </p>
  
  <p>
    You can even force the pattern to appear at the beginning or the end of the string being edited, by prefixing <tt>pattern</tt> with <tt>#</tt> (for the start) or <tt>%</tt> (for the end).
  </p>
  
  <h2 id="basename">
    Faking <tt>basename</tt> and <tt>dirname</tt>
  </h2>
  
  <p>
    This use of <tt>#</tt> to mark the beginning of an edited string, and <tt>%</tt> for the end, can also be used to simulate the <tt>basename</tt> and <tt>dirname</tt>commands in shell scripts:
  </p>
  
  <pre>	dirpath=${path%/*}</pre>
  
  <p>
    extracts the part of the <tt>path</tt> variable <em>before</em>the last slash; and
  </p>
  
  <pre>	base=${path##*/}</pre>
  
  <p>
    yields the part <em>after</em> the last slash. <strong>CAUTION</strong>: Notice that the asterisk goes <em>between</em> the slash and the <tt>##</tt>, but <em>after</em> the <tt>%</tt>.
  </p>
  
  <p>
    That&#8217;s because
  </p>
  
  <pre>	${varname#pattern}</pre>
  
  <p>
    trims the <strong>shortest prefix</strong> from the contents of the shell variable <tt>varname</tt> that matches the shell-pattern <tt>pattern</tt> ; and
  </p>
  
  <pre>	${varname##pattern}</pre>
  
  <p>
    trims the <strong>longest prefix</strong>that matches the pattern from the contents of the shell variable. Likewise,
  </p>
  
  <pre>	${varname%pattern}</pre>
  
  <p>
    trims the <strong>shortest suffix</strong> from the contents of the shell variable <tt>varname</tt> that matches the shell-pattern <tt>pattern</tt> ; and
  </p>
  
  <pre>	${varname%%pattern}</pre>
  
  <p>
    trims the <strong>longest suffix</strong> that matches the pattern from the contents of the shell variable. You can see that the general rule here is: a <em>single</em> <tt>#</tt> or <tt>%</tt> to match the <em>shortest</em> part; or a <em>double</em> <tt>##</tt> or <tt>%%</tt> to match the <em>longest</em>part.
  </p>
  
  <p>
    But be careful. If you just feed a bare filename instead of a pathname to <tt>dirname</tt>, you get just a dot <tt>[.]</tt>; but if there are no slashes in the variable you process with the hack <a href="http://mintaka.sdsu.edu/GF/bibliog/latex/debian/bash.html#basename">above</a>, you get the filename back, unaltered: because there were no slashes in it, nothing got removed. So this trick isn&#8217;t a complete replacement for <tt>dirname</tt>.
  </p>
  
  <p>
    Another use of <tt>basename</tt> is to remove a suffix from a filename. We often need to do this in shell scripts when we want to generate an output file with the same basename but a different extension from an input file. For example, to convert <tt>file.old</tt> to <tt>file.new</tt>, you could use
  </p>
  
  <pre>	newname=`basename $oldname .old`.new</pre>
  
  <p>
    so that, if you had set <tt>oldname</tt> to <tt>file.old</tt> , <tt>newname</tt> would be set to <tt>file.new</tt> . But it&#8217;s faster to say
  </p>
  
  <pre>	newname=${oldname%.old}.new</pre>
  
  <p>
    (Notice that we have to use the <tt>%</tt> operation here, even though the generic replacement for <tt>basename</tt> given above uses the <tt>##</tt>operation. That&#8217;s because we&#8217;re trimming off a suffix rather than a prefix, in this case.) If you didn&#8217;t know the old file extension, you could still replace it by saying
  </p>
  
  <pre>	newname=${oldname%.*}.new</pre>
  
  <p>
    This way of trimming off a prefix or a suffix is also useful for separating numbers that contain a decimal point into the integer and fractional parts. For example, if we set <tt>DECIMAL=123.4567</tt>, we can get the part before the decimal as
  </p>
  
  <pre>	INTEGER=${DECIMAL%.*}</pre>
  
  <p>
    and the digits of the fraction as
  </p>
  
  <pre>	FRACT=${DECIMAL#*.}</pre>
  
  <h2 id="num">
    Numerical operations
  </h2>
  
  <p>
    Speaking of digits, you can also perform simple integer arithmetic in <tt>bash</tt> without having to invoke another process, such as <tt>expr</tt>. Remember that the <tt>let</tt>operation automatically invokes arithmetic evaluation on its operands. So
  </p>
  
  <pre>	let sum=5+2</pre>
  
  <p>
    will store 7 in <tt>sum</tt>. Of course, the operands on the right side can just as well be shell variables; so, if <tt>x</tt> and <tt>y</tt>are numerical, you could
  </p>
  
  <pre>	let sum=x+y</pre>
  
  <p>
    which is both more compact and faster than
  </p>
  
  <pre>	sum=`expr $x + $y`</pre>
  
  <p>
    If you want to space the expression out for better readability, you can say
  </p>
  
  <pre>	let "sum = x + y"</pre>
  
  <p>
    and <tt>bash</tt> will do the right thing. (You have to use quotes so that <tt>let</tt>has just a single argument. If you don&#8217;t like the quotes, you can say
  </p>
  
  <pre>	sum=$(( x + y ))</pre>
  
  <p>
    but then you can&#8217;t have spaces around the <tt>=</tt>sign.)
  </p>
  
  <p>
    This way of doing arithmetic is a lot more readable than using <tt>expr</tt> — especially when you&#8217;re doing multiplications, because <tt>expr</tt> has to have its arguments separated by whitespace, so the asterisk <tt>[*]</tt> has to be quoted:
  </p>
  
  <pre>	product=`expr $x \* $y`</pre>
  
  <p>
    Yuck. Pretty ugly, compared to
  </p>
  
  <pre>	let "product = x * y"</pre>
  
  <p>
    Finally, when you need to increment a counter, you can say
  </p>
  
  <pre>	let i++</pre>
  
  <p>
    or
  </p>
  
  <pre>	let j+=2</pre>
  
  <p>
    which is cleaner, faster, and more readable than invoking <tt>expr</tt>.
  </p>
  
  <h2 id="sub">
    Sub-strings
  </h2>
  
  <p>
    In addition to truncating prefixes and suffixes, <tt>bash</tt>can extract sub-strings. To get the 2 characters that follow the first 5 in a string, you can say
  </p>
  
  <pre>	${string:5:2}</pre>
  
  <p>
    for example.
  </p>
  
  <p>
    This can save a lot of work when parsing replies to shell-script questions. If the shell script asks a yes/no question, you only need to check the first letter of the reply. Then
  </p>
  
  <pre>	init=${string:0:1}</pre>
  
  <p>
    is what you want to test. (This gives you 1 character, starting at position 0 — in other words, the first character of the string.)
  </p>
  
  <p>
    If the “offset” parameter is −1, the substring begins at the <em>last</em> character of the string; so
  </p>
  
  <pre>	last=${string: −1:1}</pre>
  
  <p>
    gives you just the last character. (Note the space that&#8217;s needed to separate the colon from the minus sign; this is required to avoid confusion with the colon-minus sequence used in specifying a default value.)
  </p>
  
  <p>
    To get the last 2 characters, you should specify
  </p>
  
  <pre>	last2=${string: −2:2} ;</pre>
  
  <p>
    note that
  </p>
  
  <pre>	penult=${string: −2:1}</pre>
  
  <p>
    gives you the <em>next</em>-to-last character.
  </p>
  
  <h2 id="wc">
    Replacing <tt>wc</tt>
  </h2>
  
  <p>
    Many invocations of <tt>wc</tt> can be avoided, especially when the object to be measured is small. Of course, you should avoid operating on a file directly with <tt>wc</tt>in constructions like
  </p>
  
  <pre>	size=`wc -c somefile`</pre>
  
  <p>
    because this captures the user-friendly repetition of the filename in the output. Instead, you want to re-direct the input to <tt>wc</tt>:
  </p>
  
  <pre>	size=`wc -c &lt; somefile`</pre>
  
  <p>
    But if the operand is already in a shell variable, you certainly don&#8217;t want to do this:
  </p>
  
  <pre>	size=`echo -n "$string" | wc -c`</pre>
  
  <p>
    — particularly if the string is short — because <tt>bash</tt>can do the job itself:
  </p>
  
  <pre>	size=${#string}</pre>
  
  <p>
    It&#8217;s even possible to make <tt>bash</tt> fake <tt>wc -w</tt>, if you don&#8217;t mind sacrificing the positional parameters:
  </p>
  
  <pre>	set $string
	nwords=$#</pre>
  
  <pre>http://mintaka.sdsu.edu/GF/bibliog/latex/debian/bash.html</pre>
