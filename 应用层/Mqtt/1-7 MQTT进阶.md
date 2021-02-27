### 1. 主题基本形式
主题最基本形式就是一个字符串。
不区分大消息、可以使用空格（不建议）、尽量使用ASCII字符，不建议使用中文，大部分不支持

### 2. 主题分级
使用“/”可以对主题进行分级

### 3 主题通配符
单级通配符：**+**
可以替代一个主题级别
home/sensor/**+**/temperature可以替代下面两个主题

home/sensor/**kitchen**/temperature
home/sensor/**bedroom**/temperature

多级通配符：**#**

可以涵盖任意数量的主题级别，需要放在最后

**home/sensor/#** 可替代下面主题

home/sensor/kitchen/temperature
home/sensor/bedroom/brightness
home/sensor/data

### 4. 主题应用注意事项
+ 以$符号开头的主题是系统保留的特殊主题，我们不能随意订阅或者向其发布信息。
+ 不要用 “/” 作为主题开头,尽管允许，但是这样做无意义，会产生没用的主题级别
+ 主题尽量不要使用空格
+ 保持主题的简介明了，为了通信带宽
+ 尽量在主题中使用ASCII
+ 在主题中嵌入客户端ID