
1.grep   在制定文件中正则匹配，查阅关键字

grep [OPTIONS]   PATTERN   FILE...
![[Pasted image 20251012094843.png]]

    可以与 | 管道符搭配使用，| 可用于连接两个程序的标准输出与标准输入流，如：
curl --head --silent google.com | grep -i content-length。
前面的curl指令类似发送http请求数据包的头部协议数据，作为第二个参数通过管道传递给grep查找包含 content-length关键字的rows

    这里实际上涉及到了grep指令的命令行参数与标准输入的混用；grep程序的逻辑是：首先查询命令行参数是否存在文件信息，不存在则直接读取stdin 标准输入流信息。


2.chmod 修改文件属性:

chmod $\text{[ 三位数字 ] [ 文件名 ]}$
三个组：u(User),g(Group),o(Others)
权限：r w x <==>4 2 1
计算方式：需要什么权限+, 最大4+2+1=7
三组需要三个sum数字，如：
chmod 755 script.sh    <=> rwx r-x r-x

by th way: 目录文件的x表示允许进入访问


3.!!
!!返回历史中上一条记录的完整命令，通常可以与：由于非sudo权限运行失败，输入：sudo !!可以重新运行的作用。

4.lsof -i:port_nr
查找占用port_nr的进程
