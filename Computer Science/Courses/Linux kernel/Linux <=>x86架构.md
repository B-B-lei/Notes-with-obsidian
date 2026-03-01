\> [!caution] This page contained a drawing which was not converted.   
 
操作系统的kernel 可以看作一系列特权操作函数的集合，从用户态访问OS kernel的角度看。  
User mode下program可以是不确定的，但kernel中的function基本上都是确定性的

80x86架构下，中断压栈寄存器(硬件压栈，由硬件实现)：  



实际入栈顺序:
ss   esp   eflags  cs  eip



GDT/LDT都是段描述符表寄存器；
系统通电后先分段再分页；
FLAGS,  CRs是控制寄存器；

CR3寄存器记录当前根分页目录表地址

jmpi 0 8 的8 -> 展开最低位pl是哪个pl
cpl, dpl, rpl

LDT的NULL设计

# C函数调用的stack原理:
### 1. 接力赛：谁负责保存现场？


![[Pasted image 20251218152433.png]]

正如所观察到的，现场的保存是分工明确的：

- **父函数（Caller）负责保存“进度”**： 它只管把参数（Params）扔进栈(传参顺序的相反顺序push)，然后执行 `CALL`。`CALL` 指令的本质是两件事：
    
    1. `PUSH EIP_next`（把父函数当前现场位置保存）。
        
    2. `JMP <func_addr>`（改变程序计数器，跳到子函数地址）。
        
    
    > **本质：** `func_name` 在汇编层面就是一个**符号地址**，最终会被链接器替换为 `Text Section`（代码段）中的内存地址。
    
- **子函数（Callee）负责保存“地基”**： 子函数开头的 `PUSH EBP` 和 `MOV EBP, ESP` 就是你提到的**函数序言（Prologue）**。
    
    1. `PUSH EBP`：保存父函数的“栈底指针”。
        
    2. `MOV EBP, ESP`：将父函数的“栈顶”变成子函数的“栈底”。此时，`EBP` 就像一根钉子，把父子两个栈帧紧紧锁在一起。(相当于将父函数的"栈顶指针"用子函数执行下不变的BP保存起来)
    
    Note: BP是栈底指针;  也就是说:整个父函数的stack都是子函数的"函数序言"保存的;
    后续子函数获取之前传入的params只需要根据当前BP的偏移量来获取实现即可.

### 函数尾声 (Epilogue)
这是结束func call之后的操作:
- **返回值处理**：先将结果存入 `EAX` 寄存器（C 语言约定 `EAX` 存放返回值）。
    
- **回收局部变量空间**：`mov esp, ebp` 
    当函数执行到结尾时，栈顶指针 `ESP` 可能因为局部变量或临时压栈操作，停留在距离 `EBP` 很远的地方。我们必须把栈恢复到“父函数刚刚调用完”的状态
    - **作用**：不管子函数在里面开了多少局部变量，这一步直接把 `ESP` 拉回到 `EBP` 指向的位置。
        
- **恢复父函数基址**：`pop ebp`
    
    - **作用**：此时栈顶正好是之前存入的 `Old EBP`。这一步弹出它，**父函数的 `EBP` 瞬间复活**。

 接下来执行RET指令：到底干了什么？

当 `leave` 执行完后，你会发现：现在的栈顶（ESP）正指着当初 `CALL` 压入的那个 `Return IP`（返回地址）！(leave就是上面 回收栈空间,恢复父函数基地址bp的打包指令)

此时，`RET` 指令登场了。它的作用极度单纯，只做一件事：

- **`pop eip`**（或者在 64 位下是 `pop rip`）
    
- **语义**：从栈顶弹出一个值，并强行塞给 CPU 的**程序计数器（EIP/RIP）**。
  
一旦 `EIP` 被修改，CPU 的下一条指令就会立刻跳转到父函数 `CALL` 指令的下一行。至此，控制权正式交还。Over!

Finally: 我们已经从call指令返回了(call是 push ip + jmp func;  然后在func中最后的ret会pop ip,此时的ip恢复到了原本caller的 call执行下一行,我们该执行caller了);
But,别忘了我们之前push(params)的部分! 它们还没有清空,如果不管的话,多层函数call的嵌套很快会让stack overflow!
So C的编译器实际上每次会在caller的 call下面额外添加 addl $20 esp 这样的指令,也就是在caller中进行一次params回收(stack清空)操作; 至此我们才完整的进行了一次C语法层面的函数调用!!!

### Summary:
上述所有调用函数的流程,包括:父函数保存现场, 子函数函数序言, 结尾的函数尾声,共同的核心任务是:
构造函数栈帧;
进行功能拆分:
C语法层面的函数调用: func(params); 被编译器翻译为: push params; call func;
C语法层面的函数定义: type func(params){body}; 被编译器翻译为:  func: 函数序言;body;函数尾声(ret收尾), 

so整个函数栈帧的build是编译器+call指令结合的产物,并且编译器还生成了call指令;call指令的本质就两条简单指令的组合: push ip; jmp func;






## 硬件驱动+同步


### Driver I
1.首先驱动程序分两部分: 第一部分是程序主动请求对外设的I/O操作,这部分一般封装成syscall api,
最典型的是结合了file system的read/write,这是因为Linux将设备抽象为了dev files来管理,如dev/tty等...... 
对于read/write,本质上两者是"高级函数",内部会调用对应设备的驱动程序的第一部分;我们以块设备为例,一般这部分会基于Linux kernel对于blk_dev的相关数据结构实现进行复杂的"官僚作风"的代码处理,比如申请"资源"(一般就是getblk, 获取一个bh,然后通过ll_rw_block函数基于输入的参数进行实际的I/O指令的配置来操作硬件, 最后将当前task挂起,就是schedule);

Note: 实际实现这部分逻辑的时候,譬如disk blocks资源, kernel在编写driver I部分会以"异步编程"的方式进行控制,关键部分如: wait_on_buffer会调用sleep_on,它实现的功能是:每个bh的waiting指针绑定的都是最近的请求该bh的task,每个task会记录先前的waiting保存到自己的kernel stack上,在自己被唤醒并且schedule调度到后,exit sleep_on之前会唤醒kernel stack上的task,形成: reverse list的结构;

2.

