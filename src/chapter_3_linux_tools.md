# Chapter 3 Linux Tools
## 3.1 grep
- 全面搜索正则表达式并把行打印出来）是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。用于过滤/搜索的特定字符。
### 3.1.1 基本命令选项
```shell
-a --text  # 不要忽略二进制数据。
-A <显示行数>   --after-context=<显示行数>   # 除了显示符合范本样式的那一行之外，并显示该行之后的内容。
-b --byte-offset                           # 在显示符合范本样式的那一行之外，并显示该行之前的内容。
-B<显示行数>   --before-context=<显示行数>   # 除了显示符合样式的那一行之外，并显示该行之前的内容。
-c --count    # 计算符合范本样式的列数。
-C<显示行数> --context=<显示行数>或-<显示行数> # 除了显示符合范本样式的那一列之外，并显示该列之前后的内容。
-d<进行动作> --directories=<动作>  # 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep命令将回报信息并停止动作。
-e<范本样式> --regexp=<范本样式>   # 指定字符串作为查找文件内容的范本样式。
-E --extended-regexp             # 将范本样式为延伸的普通表示法来使用，意味着使用能使用扩展正则表达式。
-f<范本文件> --file=<规则文件>     # 指定范本文件，其内容有一个或多个范本样式，让grep查找符合范本条件的文件内容，格式为每一列的范本样式。
-F --fixed-regexp   # 将范本样式视为固定字符串的列表。
-G --basic-regexp   # 将范本样式视为普通的表示法来使用。
-h --no-filename    # 在显示符合范本样式的那一列之前，不标示该列所属的文件名称。
-H --with-filename  # 在显示符合范本样式的那一列之前，标示该列的文件名称。
-i --ignore-case    # 忽略字符大小写的差别。
-l --file-with-matches   # 列出文件内容符合指定的范本样式的文件名称。
-L --files-without-match # 列出文件内容不符合指定的范本样式的文件名称。
-n --line-number         # 在显示符合范本样式的那一列之前，标示出该列的编号。
-P --perl-regexp         # PATTERN 是一个 Perl 正则表达式
-q --quiet或--silent     # 不显示任何信息。
-R/-r  --recursive       # 此参数的效果和指定“-d recurse”参数相同。
-s --no-messages  # 不显示错误信息。
-v --revert-match # 反转查找。
-V --version      # 显示版本信息。   
-w --word-regexp  # 只显示全字符合的列。
-x --line-regexp  # 只显示全列符合的列。
-y # 此参数效果跟“-i”相同。
-o # 只输出文件中匹配到的部分。
-m <num> --max-count=<num> # 找到num行结果后停止查找，用来限制匹配行数

```
### 3.1.2 示例
```shell
grep -E "[1-9]+"
# 只输出文件中匹配到的部分 -o 选项
echo this is a test line. | grep -o -E "[a-z]+\."
line.
# 统计文件或者文本中包含匹配字符串的行数 -c 选项：
grep -c "text" file_name
# 输出包含匹配字符串的行数 -n 选项
grep "text" -n file_1 file_2
# 打印样式匹配所位于的字符或字节偏移
#一行中字符串的字符偏移是从该行的第一个字符开始计算，起始值为0。选项  **-b -o**  一般总是配合使用。
echo gun is not unix | grep -b -o "not"
7:not
# 多级目录下递归搜索
grep "text" . -r -n
# -e 匹配多个样式
echo this is a text line | grep -e "is" -e "line" -o
is
is
line
# 只在目录中所有的.php和.html文件中递归搜索字符"main()"
grep "main()" . -r --include *.{php,html}

# 在搜索结果中排除所有README文件
grep "main()" . -r --exclude "README"

# 在搜索结果中排除filelist文件列表里的文件
grep "main()" . -r --exclude-from filelist


```
## 3.2 sed
### 3.2.1 正则表达式
#### 基本正则表达式
- .，表示匹配任意一个字符，除了换行符，类似 Shell 通配符中的 ?；

- *，表示前边字符有 0 个或多个；

- .*，表示任意一个字符有 0 个或多个，也就是能匹配任意的字符；

- ^，表示行首，也就是每一行的开始位置，^abc 匹配以 abc 开头的字符串；

- $，表示行尾，也就是每一行的结尾位置，}$ 匹配以大括号结尾的字符串；

- {}，表示前边字符的数量范围，{2}，表示重复 2 次，{2,}重复至少 2 次，{2,4} 重复 2-4 次；

- []，括号中可以包含表示字符集的表达式


#### （二）扩展正则表达式

扩展正则表达式使用频率上没有基本表达式那么高，但依然很重要，很多情况下没有扩展正则是搞不定的，sed 命令使用扩展正则时需要加上选项 -r。

- ?：表示前置字符有 0 个或 1 个；

- +：表示前置字符有 1 个或多个；

- |：表示匹配其中的一项即可；

- ()：表示分组，(a|b)b 表示可以匹配 ab 或 bb 子串，且命令表达式中可以通过 \1、\2 来表示匹配的变量

- {}：和基本正则中的大括号中意义相同，只不过使用时不用加转义符号；

### 3.2.2 语法
```shell
sed [option] 'command' filename
```
#### command
command 子命令格式：

[地址1, 地址2] [函数] [参数(标记)]
#### 基本子命令
1. 替换子命令 s
   ```shell
   # 将每行的hello替换为HELLO，只替换匹配到的第一个
   $ sed 's/hello/HELLO/' file.txt

   # 将匹配到的hello全部替换为HELLO，g表示替换一行所有匹配到的
   $ sed 's/hello/HELLO/g' file.txt

   # 将第2次匹配到的hello替换
   $ sed 's/hello/A/2' file.txt

   # 将第2次后匹配到的所有都替换
   $ sed 's/hello/A/2g' file.txt

   # 在行首加#号
   $ sed 's/^/#/g' file.txt

   # 在行尾加东西
   $ sed 's/$/xxx/g' file.txt
   ```
      - 多个匹配
      ```shell
      # 将1-3行的my替换为your，且3行以后的This替换为That
      $ sed '1,3s/my/your/g; 3,$s/This/That/g' my.txt

      # 等价于
      $ sed -e '1,3s/my/your/g' -e '3,$s/This/That/g' my.txt
      ```

2. 追加子命令 a
   ```shell
    # 将所有行下边都添加一行内容A
    $ sed 'a A' file.txt

    # 将文件中1-2行下边都添加一行内容A
    $ sed '1,2a A' file.txt
   ```
3. 插入子命令 i
4. 替换子命令 c
5. 删除子命令 d

#### option

- -n，表示安静模式。默认 sed 会把每行内容处理完毕后打印到屏幕上，加上选项后就不会输出到屏幕上。

- -e，如果需要用 sed 对文本内容进行多种操作，则需要执行多条子命令来进行操作；

- -i，默认 sed 只会处理模式空间的副本内容，不会直接修改文件，如果需要修改文件，就要指定 -i 选项；

- -f，如果命令操作比较多时，用 -e 会有点力不从心，这时需要把多个子命令写入脚本文件，使用 -f 选项指定执行该脚本；

- -r：如果需要支持扩展正则表达式，那么需要添加 -r 选项；
### 3.2.3 数字定址和正则定址

#### 数字定址
```shell
# 只将第4行中hello替换为A
$ sed '4s/hello/A/g' file.txt
# 将第2-4行中hello替换为A
$ sed '2,4s/hello/A/g' file.txt
# 从第2行开始，往下数4行，也就是2-6行
$ sed '2,+4s/hello/A/g' file.txt
# 将最后1行中hello替换为A
$ sed '$s/hello/A/g' file.txt
# 除了第1行，其它行将hello替换为A
$ sed '1!s/hello/A/g' file.txt
```
#### 正则定址
```shell
# 将匹配到hello的行执行删除操作，d 表示删除
$ sed '/hello/d' file.txt
# 删除空行，"^$" 表示空行
$ sed '/^$/d' file.txt
# 将匹配到以ts开头的行到以te开头的行之间所有行进行删除
$ sed '/^ts/,/^te/d' file.txt
```
## 3.3 awk
- awk会根据空格和制表符，将每一行分成若干字段，依次用$1、$2、$3代表第一个字段、第二个字段、第三个字段等等
- print命令里面，如果原样输出字符，要放在双引号里面。
### 3.3.1 基本用法
```shell
awk action filename
awk '{print $0}' demo.txt
```
### 3.3.2 内置函数
变量NF表示当前行有多少个字段，因此$NF就代表最后一个字段  
变量NR表示当前处理的是第几行  
toupper()用于将字符转为大写  
tolower()：字符转为小写。  
length()：返回字符串长度。  
substr()：返回子字符串。  
sin()：正弦。  
cos()：余弦。  
sqrt()：平方根。  
rand()：随机数。  
### 3.3.3 示例
```shell
# 输出奇数行
$ awk -F ':' 'NR % 2 == 1 {print $1}' demo.txt
root
bin
sync

# 输出第三行以后的行
$ awk -F ':' 'NR >3 {print $1}' demo.txt
sys
sync

$ awk -F ':' '$1 == "root" {print $1}' demo.txt
root

$ awk -F ':' '$1 == "root" || $1 == "bin" {print $1}' demo.txt
root
bin
```
## 3.4 find
- 从每个指定的起始点 (目录) 开始，搜索以该点为根的目录树，并按照运算符优先级规则从左至右评估给定的表达式，直到结果确定，此时find会继续处理下一个文件名。

### 3.4.1 语法
```shell
find [-H] [-L] [-P] [-D debugopts] [-Olevel] [起始点...] [表达式]
```
- -name pattern：按文件名查找，支持使用通配符 * 和 ?。
- -type type：按文件类型查找，可以是 f（普通文件）、d（目录）、l（符号链接）等。
  > f 普通文件  
l 符号连接  
d 目录  
c 字符设备  
b 块设备  
s 套接字  
p Fifo  
- -size [+-]size[cwbkMG]：按文件大小查找，支持使用 + 或 - 表示大于或小于指定大小，单位可以是 c（字节）、w（字数）、b（块数）、k（KB）、M（MB）或 G（GB）。
- -mtime days：按修改时间查找，支持使用 + 或 - 表示在指定天数前或后，days 是一个整数表示天数。
- -user username：按文件所有者查找。
- -group groupname：按文件所属组查找
- -depth: 让 find 以深度优先的方式遍历目录树，默认情况下 find 以广度优先方式处理目录树
### 3.4.2 示例
```shell
# 当前目录搜索所有文件，且文件内容包含 “140.206.111.111”
find . -type f -name "*" | xargs grep "140.206.111.111"
# 在/home目录下查找以.txt结尾的文件名，忽略大小写
find /home -iname "*.txt"
# 当前目录及子目录下查找所有以.txt和.pdf结尾的文件
find . -name "*.txt" -o -name "*.pdf"
# 基于正则表达式匹配文件路径
find . -regex ".*\(\.txt\|\.pdf\)$"
# 向下最大深度限制为3
find . -maxdepth 3 -type f
```





