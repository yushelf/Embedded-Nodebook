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

