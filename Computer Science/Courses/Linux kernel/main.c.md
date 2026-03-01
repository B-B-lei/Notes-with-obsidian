

这是kernel启动的入口函数


## 摘要
时刻注意kernel为了实现管理硬件功能,首先制定了一套逻辑,然后利用定义一系列kernel中使用的数据结构进行管理,分配; 进一步的,高层的调用,修改这些数据结构的操作就是高层api封装,乃至系统调用都属于高层的抽象封装;

## main.c分析

main()函数的流程顺序如下:

#### 1.首先进行mem_layout; 
mem_layout的过程包含:管理分配当前kernel所在的1MB ram以及扩展ram组成的mem space;  后者通过之前的写在0x90002中的系统信息获取(直接读该地址的数据);
Note: 这里的mem_layout内容都是基于physical mem进行的配置,即使已经启动了pages机制(因为初始为direct mapping)

具体layout内容:
1.第一个1MB作为存储kernel代码,data(各种重要全局变量,数据结构);
2.紧随其后的 1/3 MB(取决于总RAM大小,Linux的RAM是16MB,他的就是3MB大)作为数据缓冲区,用来加速与外设block_dev的交互;
3.接着分配剩余所有的mem, 通过调用mem_init()完成;
所谓分配mem以及mem_init的内容就是: 对于kernel global维护的数据结构mem_map进行初始化写操作;  已经确定了kernel上层基于pages管理mem的设计,接下来利用该数据结构抽象来管理所有pages,  初始mem_init()就是将所有的mem_map[]数组置为空闲;
每个mem_map element代表当前idx的page是否分配使用(busy/free);

最后mem_init结束后,前1MB(kernel)对应的pages(255个)和buffer的mem_map是USED;后面的pages用于user proc都是0(free)状态.

Note:后续针对其他kernel功能的 init func本质上也就是对上述数据结构进行初始化操作,并且注意这些数据结构实际的物理地址都位于kernel mem的data section中,也就是第一个1MB中;
#### 2.trap_init()

首先明确中断的启动,调用机制是由cpu硬件逻辑实现的;CPU会监听EFLAGS中的标记位,出现异常后分析得到异常号,之后根据该异常号去查询IDT的具体索引号;
Note: IDT在kernel中作为全局数据结构使用;


head.S 相关:
物理地址布局（前1MB）：
┌─────────────────────────────────────┐
│ 0x000000 - 0x000FFF (4KB)            │ <- 页目录 (pg_dir)
├─────────────────────────────────────┤
│ 0x001000 - 0x001FFF (4KB)            │ <- 页表0 (pg0)
├─────────────────────────────────────┤
│ 0x002000 - 0x002FFF (4KB)            │ <- 页表1 (pg1)
├─────────────────────────────────────┤
│ 0x003000 - 0x003FFF (4KB)            │ <- 页表2 (pg2)
├─────────────────────────────────────┤
│ 0x004000 - 0x004FFF (4KB)            │ <- 页表3 (pg3)
├─────────────────────────────────────┤
│ 0x005000 - 0x005FFF (4KB)            │ <- tmp_floppy_area
├─────────────────────────────────────┤
│ 0x006000 - 0x006FFF (4KB)            │ <- IDT (256×8=2KB)
├─────────────────────────────────────┤
│ 0x007000 - 0x007FFF (4KB)            │ <- GDT (256×8=2KB)
├─────────────────────────────────────┤
│ 0x008000 - 0x00FFFF (32KB)           │ <- 内核代码和数据
└─────────────────────────────────────┘
之前的head.S中设置了INT中断表的位置,在前1MB地址空间中,并且每个表项都没有实际有效值(临时占位);


IDT表项:
8字节IDT描述符：
┌─────────────────────────────────────┐
│ 偏移地址低16位 (0-15位)               │
├─────────────────────────────────────┤
│ 段选择子 (16-31位) ← 这里！           │
├─────────────────────────────────────┤
│ 属性 (32-47位)                       │
│   - P位(15): 存在位                 │
│   - DPL(13-14): 特权级               │
│   - Type(8-12): 门类型               │
├─────────────────────────────────────┤
│ 偏移地址高16位 (48-63位)              │
└─────────────────────────────────────┘

trap_init()的作用就是将实际实现的interrupt function(写在别的地方,但是是全局函数,trap_init()可以识别到并且得到地址)的地址按照IDT表的顶层设计(拆分开的格式,出于历史兼容的原因,),初始化对应的IDT表项, 包括: IDT idx对应的服务程序的入口地址(&idt_func),DPL(访问该func的需要的权限 3/0-> user/kernel)等信息;

有关CPL,DPL,RPL: C代表current, cs开启保护模式后,低16位作为段选择子,而段选择子的低2位组成RPL, IDT表项中的段选择子也包含RPL;  而当前cs中的RPL作为CPL,用来和GDT的DPL判断是否允许当前command访问/跳转对应段选择子制定的段地址;

Notice: trap_init()主要用于初始化IDT中断向量表表项内容,让每个表项对应idx(也就是中断号)的地址字段指向实际的global int_func地址并且设置访问权限DPL


#### 3.sched_init()

1.gdt  全局表,每个表项记录一个段描述符(8 Bytes),用来描述一个段的信息:段基址,限长,访问权限

GDT[0]: NULL描述符
GDT[1]: 内核代码段 (0x08)
GDT[2]: 内核数据段 (0x10)
GDT[3]: 临时描述符
GDT[4]: TSS0描述符 ← sched_init()添加
GDT[5]: LDT0描述符 ← sched_init()添加
GDT[6]: TSS1描述符
GDT[7]: LDT1描述符
...
GDT[132]: TSS63描述符
GDT[133]: LDT63描述符

ldt与tss是相对每个进程来说的(tast_struct结构体中包含这两个member,其中ldt[3]以数组存在,tss一个),也就是说每个进程都有独自的ldt与tss;

2.ldt

LDT[0]: NULL描述符
LDT[1]: 用户代码段 (0x0f)
LDT[2]: 用户数据段 (0x17)
16位段选择子：
┌─────────────────────────────────────┐
│ 索引 (3-15位) | TI(2位) | RPL(0-1位) │
└─────────────────────────────────────┘

3.tss task state segment 记录任务的上下文; tss的具体数据结构是一个包含了许多寄存器(任务上下文)的结构体;这里不一一枚举了;

功能:
对于tasks(64个最多),第一个initial_tast(task0),进行数据硬编码初始化,然后把task0的tss,ldt写入gdt[4]  [ 5]的对应tss,ldt记录中;然后初始化剩余的task[]数组(没错是数组形式管理),然后每个task[i]对应的gdt表项(分别保护ldt,tss)都初始化为0;
然后加载task0的tss,ldt到tss,ldt r(分别的寄存器中)
需要注意的是,task0是一个union(task_struct,stack),并且总的大小是stack的大小pagesize限制,也就是4KB; 并且无需担心两个element彼此覆盖的情况,因为: task_struct部分的地址是从:&init_task(就是union体)的地址开始的,而对应的init_task中结构体部分是通过INIT_TASK这个宏定义进行硬编码的,其中包含了tss的字段(每个task_struct都包含这个字段),而它的tss中定义了sp栈指针指向的内容为:&init_task+PAGESIZE !!注意这是重点,因为stack操作sp指针都是从:大地址向小地址变化的,而这里相当于union占据了两头,并且不会产生覆写的情况(stack不会那么大);task_struct大概956B
另外,从这里分析得出:union中定义的 char stack[ PAGESIZE ]虽然是数组但不是用来idx随即存取访问的,仅仅是起到:分配,声明,占用空间的功能; 实际的使用仍然是通过:sp寄存器读取tss中的sp字段,然后进行push,pop指令操作sp寄存器值来实现的

读fork产生的 get_free_page inline assmbly






unlocker_buffer 被endrequest调用,  endrequest表示对应bh的请求可以满足(bh指向buffer写完数据可被process访问) ,  unlocker_buffer解锁 bh->wait(链表的head node, 后来的process先解锁使用buffer),





