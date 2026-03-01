
gcc调用编译程序GAS和链接程序LD,构成自动化生成可执行程序的脚手架工具

gcc将一个.c/cpp文件编译为可执行文件的过程包括：

1.gcc  -E   E代表Expand展开，用于替换源程序中的如#include, # define等宏；
生成.i文件

2.gcc  -S  S代表Stop after compilation,（do not assmbly）  编译后停止不进行汇编；这里的编译指的是生成实际二进制指令的助记符语言assmbly的过程；
生成.s 或.asm后缀文件；

3.gcc -c  c代表compille,(compile and assmbly)实际生成可执行文件的部分内容，
生成.o文件,但是此时未进行链接只是局部文件仍然无法运行


gcc -o o代表object,后面跟着的是指定生成的obj文件名（上面的比如.i,  .s,  .o文件都可以作为object）

直接运行gcc不指定上述参数时，gcc会按照上述步骤依次运行直到生成最终的可执行文件,可执行文件没有后缀名；
注意上述过程按照顺序执行并且工作内容也是流式的，编译器gcc/g++会自动识别当前文件编译进展并仅执行接下来的任务
