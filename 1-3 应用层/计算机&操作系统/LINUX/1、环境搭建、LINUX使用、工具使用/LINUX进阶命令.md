## find:查找符合条件的文件(目录)

**格式：  find    目录名    选项    查找条件**

举例：

+ 1）find /work/001_linux_basic/dira/  -name "test1.txt"
  说明:
  	a)/work/001_linux_basic/dira/指明了查找的路径
  	b)-name表明以名字来查找文件
  	c)"test1.txt"，就指明查找名为test1.txt的文件
  同理：
  	find /work/001_linux_basic/dira/  -name "*.txt"	
  	查找指定目录下面所以以.txt结尾的文件，其中*是通配符。
  	find /work/001_linux_basic  -name "dira"	
  	查找指定目录下面是否存在dira这个目录，dira是目录名。

**注意：**

+ 如果没有指定查找目录，则为当前目录。
  	find . -name "*.txt"   其中.代表当前路径 
  	find  -name "*.txt"
  	都是一样的功能
  	
+ 2）find还有一些高级的用法，如查找最近几天(几个小时)之内(之前)有变动的文件
   find /home -mtime -2  查找/home目录下两天内有变动的文件	

## which和whereis

目的：查找命令或应用程序的所在位置
格式：which  命令名/应用程序名

在终端上执行pwd实际上是去执行了/bin/pwd
举例：
	which pwd 定位到/bin/pwd
	which gcc 定位到/usr/bin/gcc
	whereis  pwd查找到可执行程序的位置/bin/pwd和手册页的位置/usr/share/man/man1/pwd.1.gz

## 压缩（无损压缩）

单个文件的压缩(解压)使用**gzip 和bzip2** 、多个文件和目录使用**tar**。   

### gzip的常用选项
-l(list)	列出压缩文件的内容
-k(keep)	在压缩或解压时，**保留**输入文件。
-d(decompress)	将压缩文件进行解压缩

+ 1）查看
  	gzip  -l 压缩文件名
  	比如：gzip -l pwd.1.gz

+ 2）解压
  	gzip -kd  压缩文件名
  	比如：gzip -kd pwd.1.gz
  	该压缩文件是以.gz结尾的单个文件
+ 3）压缩
  	gzip -k  源文件名
  	比如：gzip -k mypwd.1
      得到了一个.gz结尾的压缩文件

**注意：**

1）如果gzip不加任何选项，此时为压缩，压缩完该文件会生成后缀为.gz的压缩文件，
并删除原有的文件，所以说，推荐使用gzip -k 来压缩源文件。

2）相同的文件内容，如果文件名不同，压缩后的大小也不同。

**3）gzip只能压缩单个文件，不能压缩目录。**



### bzip2来压缩单个文件
bzip2的常用选项
-k(keep)	在压缩或解压时，保留输入文件。
-d(decompress)	将压缩文件进行解压缩

1）压缩
	bzip2  -k  源文件名
	比如：bzip2 -k mypwd.1
	得到一个.bz2后缀的压缩文件
2）解压
	bzip2  -kd  压缩文件名
	bzip2 -kd mypwd.1.bz2	

注意：
1）如果bzip2不加任何选项，此时为压缩，压缩完该文件会生成后缀为.bz2的压缩文件，
并删除原有的文件，所以说，推荐使用bzip2 -k  来压缩源文件。
2）bzip2只能压缩单个文件，不能压缩目录。



单个文件的压缩使用gzip或bzip2，
压缩有两个参数：1）压缩时间  2）压缩比
**一般情况下，小文件使用gzip来压缩，大文件使用bzip2来压缩。**

+ mypwd.1源大小是1477字节，
  	gzip压缩后mypwd.1.gz是877字节，
  	bzip2压缩后mypwd.1.bz2是939字节。
+ myls.1源文件大小7664字节，
  	gzip压缩后myls.1.gz是3144字节，
  	bzip2压缩后myls.1.bz2是3070字节。	



### tar常用选项

**-c(create)**： 表示创建用来生成文件包
**-x：**表示提取，从文件包中提取文件
**-t:**    可以查看压缩的文件。
**-z:**    使用gzip方式进行处理，它与”c“结合就表示压缩，与”x“结合就表示解压缩。
**-j:** 	使用bzip2方式进行处理，它与”c“结合就表示压缩，与”x“结合就表示解压缩。
**-v：**  (verbose)详细报告tar处理的信息
**-f(file)：**表示文件，后面接着一个文件名。 
**-C：**  <指定目录>    解压到指定目录

#### 1.tar打包、gzip压缩

1）压缩
	tar -czvf   压缩文件名   目录名
	如：tar czvf dira.tar.gz  dira

注意：
tar  -czvf与tar  czvf是一样的效果，所以说，后面统一取消-。

2）查看
	tar tvf   压缩文件名
	如：tar tvf dira.tar.gz

3）解压
	tar xzvf 压缩文件名
	tar xzvf 压缩文件名  -C  指定目录
	如：tar xzvf dira.tar.gz   解压到当前目录
	如：tar xzvf dira.tar.gz   -C  /home/book   解压到/home/book
	
	

#### 2.tar打包、bzip2压缩

1）压缩
	tar c**j**vf   压缩文件名   目录名
	如：tar cjvf dira.tar.bz2  dira
	
2）查看
	tar tvf   压缩文件名
	如：tar tvf dira.tar.bz2

3）解压
	tar x**j**vf 压缩文件名
	tar x**j**vf 压缩文件名  -C  指定目录
	如：tar xjvf dira.tar.bz2   解压到当前目录
	如：tar xjvf dira.tar.bz2 -C  /home/book  解压到/home/book

