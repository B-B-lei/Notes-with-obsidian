配置文件: ~/.gitconfig
本地配置一般在项目root目录下的.git/config文件中进行配置

1.git log   查看当前分支branch的历史   (默认从当前HEAD开始向父commit回溯)

git log --all --graph --decorate (--oneline)   查看整个git仓库(项目) 的图状历史(both local and remote)  oneline参数能够减小显示的log信息规模

太长了,这个命令,简短操作是:git config --global alias.hist "log --graph --pretty=format:'%C(bold yellow)%h%C(reset) -%C(bold green)%d%C(reset) %s %C(bold blue)<%an>%C(reset)' --abbrev-commit --date=relative --all"

设置git command别名, 现在只需要运行git hist 就能得到oneline的缩小graph all


git项目管理时一些中间文件比如: C语言的.o文件,  .s文件,以及二进制文件,这些不是我们想要进行版本管理的;我们需要的只是源文件,配置信息等文件;
可以通过当前root proj 目录下创建 .gitignore文件,写入想要git add忽略不保存track的文件name;
上述项目下创建都是local本地配置,想要全局global配置在~/.gitignore下进行配置,但是需要在~/.gitconfig中进行 git config --global进行配置声明该文件;

## Command

#### 1.local operation
	git init  将项目托管给git
	git add <file>  将file添加到暂存区
	git commit -m <"注释"> 添加commit记录(将add到的暂存区记录更新为commit内容),并添加注释.  

#### 2.branch operation
	git branch <branch_name> 基于当前HEAD的位置创建新branch
	git checkout <name | hashkey> 切换HEAD到指定commit node(会dispatch)
	git merge <branchname> 将指定branch与当前branch进行合并,可能产生conflict可以自行解决后commit


#### 3.log
	git status
	git log
	git reflog
	git hist(自定义的<==>git --all --graph --decorate --)
#### 4.remote
	git remote
	git fetch(仅获取新的与local不同的obj等信息不立刻改变local git branches布局)
	git pull(fetch+merge)
	git push
	git clone(创建git版本管领并且同步为remote的形状)


## 深入阅读 Pro GIt
refs是类似别名的数据结构,指向实际的commit object; 
object是特殊数据结构,file形式实现,filename是hash_key, filecontent是zilib压缩的content; 
commit/tree/blob都由对应的objects实现. 

