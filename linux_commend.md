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
