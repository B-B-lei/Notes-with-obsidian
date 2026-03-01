原本使用pip进行python依赖包下载与管理，但pip将所有包放在一起,管理混乱,有时会导致各种版本不一致冲突问题，况且涉及到科学计算、研究需要使用的jupyternotebook也需要在conda环境下使用;实际conda的作用≈pip+virtualenv , 故我们最后选择Anaconda服务。
 
Anaconda基本原理：  
conda的功能与原理可以类比git(目前水平这样想，后续可能修改)，两者同样用到了虚拟化的技术；Anaconda本身维护了一个pkg目录与env目录(这两个目录的path在配置文件condarc中定义的,一般不做修改了)，pkg存储使用conda install到的依赖包，env目录下是conda创建的不同env环境。  
env是conda的核心理念与功能，实际使用conda服务的时候，我们最好对于每个python项目使用一个独立的env环境；这里的env环境本质上包括：独立的python解释器版本(python.exe)以及该环境下(某个项目)需要使用到的packges依赖包，这主要是因为许多不同项目使用的python版本存在不同，使用的依赖包之间也存在不同，如果自行手动管理会十分混乱，并且依赖关系版本之间也可能存在矛盾（垃圾python版本管理），而conda提供的env机制能够很好的起到虚拟化与环境隔离的作用（某个项目使用的环境将特定python 版本与依赖包封装隔离出来），实际的项目或python脚本则运行在env之上(假装这个env就是本机的主python环境)； 而实际上每个env内部的依赖包与python版本并不是简单的copy一份，这会导致大量的重复与存储空间损耗（许多包需要被许多env下的项目使用），实际conda采用的是硬链接策略(可以类比为文件层面的C/C++指针引用)，实际指向数据是相同的，无需担心大量损耗。
   

Conda 常用指令：（我没有将Anaconda路径添加到win环境变量中，故只能通过Anaconda prompt终端运行这些命令）  
conda create -n[指定name] env_name[可自定义] python=[version,比如3.10]  
能够创建一个conda管理的新env,并且指定解释器版本，也能指定初始化env时要安装的依赖包这里没有介绍
 
conda env list  
展示当前所有conda管理的环境   conda activate [env_name]  
激活指定env，主要给终端使用(相当于当前终端也进入该env,使用对应的python解释器版本)
 
conda install [pkg_name]  
默认先从Anaconda下的pkg中寻找包，找到了添加硬链接到当前env,没找到从远程库（conda配置文件condarc中设置的）寻找指定packge下载到pkg,然后同样在env中添加硬链接。￼