- wget 下载<br>

  -c 断点续传<br>
  o 指定存放位置<br>
  killall -SIGTSTP wget (相当于 ^z)<br>
  恢复 killall -SIGCONT wget

- ubuntu下打开图形界面的文件浏览

  nautilus

- curl 下载单个文件, 默认将输出打印到标准输出中 可以用 > 对输出进行转向输出

- tar 压缩解压

  - 以下几个,压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个<br>
    -c, --create 创建压缩文件<br>
    -x, --extract 解压 -t, --list 查看内容<br>
    -r, --append 向压缩包末尾增加文件<br>
    -u, --update 更新原压缩包的文件

  - -f 最后一个参数, 后接归档文件名

  - 以下可选<br>
    -a, --auto-compress 使用归档后缀名来决定压缩程序<br>
    -j, --bzip2 通过 bzip2 过滤归档<br>
    -z, --gzip, --gunzip, --ungzip 通过 gzip 过滤归档<br>
    -Z, --compress, --uncompress 通过 compress 过滤归档

    > 1. *.tar 用 tar –xvf 解压
    > 2. *.gz 用 gzip -d或者gunzip 解压
    > 3. *.tar.gz和_.tgz 用 tar –xzf 解压
    > 4. *.bz2 用 bzip2 -d或者用bunzip2 解压
    > 5. *.tar.bz2用tar –xjf 解压
    > 6. *.Z 用 uncompress 解压
    > 7. *.tar.Z 用tar –xZf 解压

- ps查看进程

```
ps -ef  
ps -aux
```

- chown 修改文件所属用户和用户组

  基本语法：<br>
  chown [-R] 账号名称 文件或目录<br>
  chown [-R] 账号名称:用户组名称 文件或目录<br>
  参数： -R : 进行递归( recursive )的持续更改，即连同子目录下的所有文件、目录 都更新成为这个用户组。常常用在更改某一目录的情况。

- unzip 解压 -d 指定解压目录


### awk


### sed
sed 一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有 改变，除非你使用重定向存储输出

    [root@www ~]# sed [-nefr] [动作]
    选项与参数：
    -n ：使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN 的数据一般都会被列出到终端上。但如果加上 -n 参数后，则只有经过sed 特殊处理的那一行(或者动作)才会被列出来。
    -e ：直接在命令列模式上进行 sed 的动作编辑；
    -f ：直接将 sed 的动作写在一个文件内， -f filename 则可以运行 filename 内的 sed 动作；
    -r ：sed 的动作支持的是延伸型正规表示法的语法。(默认是基础正规表示法语法)
    -i ：直接修改读取的文件内容，而不是输出到终端。

    动作说明： [n1[,n2]]function
    n1, n2 ：不见得会存在，一般代表**选择进行动作的行数**，举例来说，如果我的动作是需要在 10 到 20 行之间进行的，则『 10,20[动作行为] 』

    function：
    a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
    c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
    d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
    i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
    p ：列印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～
    s ：替换，通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是啦！
