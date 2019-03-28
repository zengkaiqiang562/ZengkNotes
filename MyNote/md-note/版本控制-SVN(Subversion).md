SVN 官网：https://subversion.apache.org/

Github SVN 源码：https://github.com/apache/subversion


Subversion(SVN) 是一个开源的版本控制系統
Subversion 管理着随时间改变的数据。这些数据放置在一个中央资料档案库(repository) 中。
这个档案库很像一个普通的文件服务器, 不过它会记住每一次文件的变动。
这样你就可以把档案恢复到旧的版本, 或是浏览文件的变动历史。


SVN 的一些概念

    repository（源代码库）:  源代码统一存放的地方

    Checkout（提取）:  当你手上没有源代码的时候，你需要从repository checkout一份

    Commit（提交）:  当你已经修改了代码，你就需要Commit到repository

    Update (更新):  当你已经Checkout了一份源代码， Update一下你就可以和Repository上的源代码同步，
						你手上的代码就会有最新的变更

日常开发过程其实就是这样的（假设你已经Checkout并且已经工作了几天）：

	Update(获得最新的代码) --> 作出自己的修改并调试成功 --> Commit(大家就可以看到你的修改了) 。

如果两个程序员同时修改了同一个文件呢, SVN可以合并这两个程序员的改动，
实际上SVN管理源代码是以行为单位的，就是说两个程序员只要不是修改了同一行程序，SVN都会自动合并两种修改。
如果是同一行，SVN会提示文件Confict, 冲突，需要手动确认。

## SVN 安装 ##
下载地址：https://sourceforge.net/projects/win32svn/
下载完成后，在相应的盘符中会有一个Setup-Subversion-1.8.16.msi的文件，目前最新的版本是1.8.16

把svn安装目录里的bin目录添加到path路径中，在命令行窗口中输入 svnserve --help ,查看安装正常与否


## 版本控制系统的生命周期 ##

### 创建版本库 ###
版本库相当于一个集中的空间，用于存放开发者所有的工作成果。
版本库不仅能存放文件，还包括了每次修改的历史，即每个文件的变动历史。

Create 操作是用来创建一个新的版本库。大多数情况下这个操作只会执行一次。
当你创建一个新的版本库的时候，你的版本控制系统会让你提供一些信息来标识版本库，
例如创建的位置和版本库的名字。

### 检出 Checkout ###
Checkout 操作是用来从版本库创建一个工作副本。
工作副本是开发者私人的工作空间，可以进行内容的修改，然后提交到版本库中。

### 更新 Update ###
update 操作是用来更新版本库的。这个操作将工作副本与版本库进行同步。
由于版本库是由整个团队共用的，当其他人提交了他们的改动之后，你的工作副本就会过期。

指令举例：

	svn update

如上，默认是更新到最新的版本
我们也可以指定更新到哪个版本：

	svn update -r6
	
### 执行变更 ###
当检出之后，你就可以做很多操作来执行变更。如修改文件，删除文件，添加文件，移动文件等变更操作。
Rename 操作可以更改文件/目录的名字。

这些变更操作不会立即执行，而是被添加到待变更列表。当执行Commit操作后，变更才会在版本库中生效

### 复查变化 —— status、diff ###
在Commit提交变更之前，最好复查下执行的变更。
Status 操作列出了工作副本中所进行的变更。

正如我们之前提到的，你对工作副本的任何改动都会成为待变更列表的一部分。
Status 操作就是用来查看这个待变更列表。

Status 操作只是提供了一个变动列表，但并不提供变动的详细信息。
你可以用 diff 操作来查看这些变动的详细信息。

指令举例：

	svn diff

	svn diff


### 修复错误 —— revert ###
我们来假设你对工作副本做了许多修改，但是现在你不想要这些修改了，这时候 revert 操作将会帮助你。

Revert 操作重置了对工作副本的修改。
它可以重置一个或多个文件/目录；
也可以重置整个工作副本，在这种情况下，revert 操作将会销毁待变更列表并将工作副本恢复到原始状态。

### 解决冲突 —— merge、resolve ###
合并的时候可能会发生冲突。

Merge 操作会自动处理可以安全合并的东西。其它的会被当做冲突。
例如，"hello.c" 文件在一个分支上被修改，在另一个分支上被删除了。这种情况就需要人为处理。

Resolve 操作就是用来帮助用户找出冲突并告诉版本库如何处理这些冲突。

### 提交更改 —— commit ###
Commit 操作是用来将更改从工作副本到版本库。
这个操作会修改版本库的内容，其它开发者可以通过更新（Update）他们的工作副本来查看这些修改。

在提交之前，你必须将文件/目录添加到待变更列表中。列表中记录了将会被提交的改动。

当提交的时候，我们通常会提供一个注释来说明为什么会进行这些改动。这个注释也会成为版本库历史记录的一部分。

Commit 是一个原子操作，也就是说要么完全提交成功，要么失败回滚。用户不会看到成功提交一半的情况。

指令举例：

	svn commit -m "change HelloWorld.html first"


## SVN 启动模式 ##
根据启动 SVN 时， 参数 "-r" 的配置方式不同， SVN的启动模式可分为 "单库 svnserve 方式" 和
"多库 svnserve 方式"

在SVN服务器端对SVN版本库的操作如下：

	mkdir /opt/svn    #创建版本库目录

	svnadmin create /opt/svn/runoob   #创建 版本库

	svnserve -d -r 路径 --listen-port 端口号    #启动 svn

-r: 配置方式决定了版本库访问方式。
--listen-port: 指定SVN监听端口，不加此参数，SVN默认监听3690

**单库svnserve方式**
对应的svnserve指令为：

	svnserve -d -r /opt/svn/runoob

即参数 "-r" 直接指定到版本库。在这种情况下，一个svnserve只能为一个版本库工作。

authz配置文件中对版本库权限的配置应这样写：

	[groups]
	admin=user1
	dev=user2
	[/]
	@admin=rw
	user2=r

使用类似这样的URL：svn://192.168.0.1/　即可访问runoob版本库

**多库svnserve方式**
对应的svnserve指令为：

	svnserve -d -r /opt/svn

即参数 "-r" 指定到版本库的上级目录。这种情况，一个svnserve可以为多个版本库工作 

authz配置文件中对版本库权限的配置应这样写：

	[groups]
	admin=user1
	dev=user2
	[runoob:/]
	@admin=rw
	user2=r
	
	[runoob01:/]
	@admin=rw
	user2=r

如果此时还用[/]，则表示所有库的根目录，
使用类似这样的URL：svn://192.168.0.1/runoob　即可访问runoob版本库。


## SVN 创建版本库 ##

	svnadmin create /opt/svn/runoob01

如上即创建了一个版本库 runoob01
runoob01版本库目录下会生成多个文件夹，其中 conf 目录保存了 svn 配置相关的文件，包括：
svnserve.conf、passwd、authz文件。

**svnserve.conf 配置文件**
该文件仅由一个[general]配置段组成

	[general]
	anon-access = none
	auth-access = write
	password-db = /home/svn/passwd
	authz-db = /home/svn/authz
	realm = tiku

anon-access: 控制非鉴权用户访问版本库的权限。取值范围为"write"、"read"和"none"。 
其中"write"为可读可写，"read"为只读，"none"表示无访问权限。 缺省值：read 

auth-access: 控制鉴权用户访问版本库的权限。取值范围为"write"、"read"和"none"。 
缺省值：write

authz-db: 指定权限配置文件的路径，除非指定绝对路径，否则文件位置为相对conf目录的相对路径。 
默认为 conf目录下的 authz文件 

password-db： 指定用户名口令文件的路径。默认为conf目录下的passwd文件。

realm: 指定版本库的认证域，即在登录时提示的认证域名称。
若两个版本库的 认证域相同，建议使用相同的用户名口令数据文件。
缺省值：一个UUID(Universal Unique IDentifier，全局唯一标示)。 


**passwd 用户名口令文件**
该文件仅由一个[users]配置段组成。

	[users]
	admin = admin
	thinker = 123456

即格式为： <用户名> = <口令>

**authz 权限配置文件**
该配置文件由一个[groups]配置段和若干个版本库路径权限段组成。

其中 [groups]配置段 中配置行格式如下：

	<用户组> = <用户列表>

版本库路径权限段 的段名格式如下：

	[<版本库名>:<路径>] 

举例：

	[groups]
	g_admin = admin,thinker
	
	[admintools:/]
	@g_admin = rw
	* =
	
	[test:/home/thinker]
	thinker = rw
	* = r


## SVN 检出操作（checkout）、查看检出的版本库信息 info ##

	svn checkout svn://192.168.0.1/runoob01 --username=user01

在客户端通过svn用户"user01" 对版本库"runoob01" 进行检出（checkout）操作。

检出成功后，在客户端的当前目录下生成版本库runoob01的副本目录

查看更多关于版本库的信息，执行 info 命令。


## SVN 解决冲突 —— commit、update、commit ##

	root@runoob:~/svn/runoob01/trunk# svn commit -m "change HelloWorld.html first"
	Sending        HelloWorld.html
	Transmitting file data .svn: E160028: Commit failed (details follow):
	svn: E160028: File '/trunk/HelloWorld.html' is out of date

如上提交修改时，表示提交失败，通常可能是出现了冲突，于是，需要先执行 svn update指令更新工作副本：

	root@runoob:~/svn/runoob01/trunk# svn update
	Updating '.':
	C    HelloWorld.html
	Updated to revision 6.
	Conflict discovered in file 'HelloWorld.html'.
	Select: (p) postpone, (df) show diff, (e) edit file, (m) merge,
	        (mc) my side of conflict, (tc) their side of conflict,
	        (s) show all options: mc
	Resolved conflicted state of 'HelloWorld.html'
	Summary of conflicts:
	  Text conflicts: 0 remaining (and 1 already resolved)

如上，因为本地工作副本中的HelloWorld.html已做了修改，当从版本库中更新时，本地的修改就会和版本库中的
更新产生冲突，于是，需要解决冲突。

如上的"mc" 表示以本地的修改替换掉版本库中有冲突的更新。

冲突解决后，再次执行 commit指令 提交本地修改：

	root@runoob:~/svn/runoob01/trunk# svn commit -m "change HelloWorld.html second"
	Sending        HelloWorld.html
	Transmitting file data .
	Committed revision 7.
	
如上，提交成功

## SVN 提交操作 —— status、add ##
在本地的工作副本中新建一个readme文件：

	root@runoob:~/svn/runoob01/trunk# cat readme 
	this is SVN tutorial.

通过 svn status 指令 查看工作副本中的变更列表

	root@runoob:~/svn/runoob01/trunk# svn status
	?       readme

如上， readme文件的状态为？，说明它还未加到版本控制中。

执行 add 指令将文件加入到版本控制（并加入到变更列表），等待提交到版本库

	root@runoob:~/svn/runoob01/trunk# svn add readme 
	A         readme

执行完 add 指令后再次查看变更列表：

	root@runoob:~/svn/runoob01/trunk# svn status     
	A       readme

此时 readme的状态为A，它意味着这个文件已经被成功地添加到了版本控制中。

最后，通过 commit 指令将变更列表中的修改提交到版本库中：

	root@runoob:~/svn/runoob01/trunk# svn commit -m "SVN readme."
	Adding         readme
	Transmitting file data .
	Committed revision 8.
	svn commit -m "SVN readme."

如上，如果忽略了 -m 选项， SVN会打开一个可以输入多行的文本编辑器来让你输入提交信息。

现在 readme 被成功地添加到了版本库中，并且修订版本号自动增加了1。
	

## SVN 版本回退 —— revert、merge -r ##
当我们想放弃对文件的修改，可以使用 svn revert 命令。
svn revert 指定只是回退本地工作副本中的修改。

举例：

	root@runoob:~/svn/runoob01/trunk# svn status
	M       readme

如上， readme文件的状态为 M ，即modify，表示readme文件被修改了。
如果要回退修改， 则执行 revert 指令：

	root@runoob:~/svn/runoob01/trunk# svn revert readme 
	Reverted 'readme'

如上只是将 readme文件中的修改回退，再次查看 readme文件的状态则为：

	root@runoob:~/svn/runoob01/trunk# svn status 
	root@runoob:~/svn/runoob01/trunk# 

即 readme文件没做任何修改。

revert 操作不仅可以使单个文件恢复原状， 还可以使整个目录恢复原状。恢复目录用 -R 命令：

	svn revert -R trunk

**恢复一个已经提交的版本 —— merge -r**
为了消除一个旧版本，我们必须撤销旧版本里的所有更改然后提交一个新版本。这种操作叫做 reverse merge。

首先，找到仓库的当前版本，现在是版本 22，我们要撤销回之前的版本，比如版本 21。

	svn merge -r 22:21 readme 


## SVN 查看历史信息 —— log、diff、cat、list ##

svn log: 用来展示svn 的版本作者、日期、路径等等。

svn diff: 用来显示特定修改的行级详细信息。

svn cat: 取得在特定版本的某文件显示在当前屏幕。

svn list: 显示一个目录或某一版本存在的文件。

### svn log ###
svn log 可以显示所有的信息

**svn log -r begin:end**
如果只希望查看特定的某两个版本之间的信息，可以使用参数 "svn log -r begin:end"

	root@runoob:~/svn/runoob01/trunk# svn log -r 6:8
	------------------------------------------------------------------------
	r6 | user02 | 2016-11-07 02:01:26 +0800 (Mon, 07 Nov 2016) | 1 line
	
	change HelloWorld.html first.
	------------------------------------------------------------------------
	r7 | user01 | 2016-11-07 02:23:26 +0800 (Mon, 07 Nov 2016) | 1 line
	
	change HelloWorld.html second
	------------------------------------------------------------------------
	r8 | user01 | 2016-11-07 02:53:13 +0800 (Mon, 07 Nov 2016) | 1 line
	
	SVN readme.
	------------------------------------------------------------------------

**svn log 文件路径**
如果只想查看某一个文件的版本修改信息，可以使用 "svn log 文件路径"

	root@runoob:~/svn/runoob01# svn log trunk/HelloWorld.html 
	------------------------------------------------------------------------
	r7 | user01 | 2016-11-07 02:23:26 +0800 (Mon, 07 Nov 2016) | 1 line
	
	change HelloWorld.html second
	------------------------------------------------------------------------
	r6 | user02 | 2016-11-07 02:01:26 +0800 (Mon, 07 Nov 2016) | 1 line
	
	change HelloWorld.html first.
	------------------------------------------------------------------------
	r5 | user01 | 2016-11-07 01:50:03 +0800 (Mon, 07 Nov 2016) | 1 line
	
	
	------------------------------------------------------------------------
	r4 | user01 | 2016-11-07 01:45:43 +0800 (Mon, 07 Nov 2016) | 1 line
	
	Add function to accept input and to display array contents
	------------------------------------------------------------------------
	r3 | user01 | 2016-11-07 01:42:35 +0800 (Mon, 07 Nov 2016) | 1 line
	
	
	------------------------------------------------------------------------
	r2 | user01 | 2016-08-23 17:29:02 +0800 (Tue, 23 Aug 2016) | 1 line
	
	first file
	------------------------------------------------------------------------

**svn log -l N -v**
如果希望得到目录的信息要加 -v。
如果希望显示限定N条记录的目录信息，使用 "svn log -l N -v" ，其中 -l 表示 limit

	root@runoob:~/svn/runoob01/trunk# svn log -l 5 -v 
	------------------------------------------------------------------------
	r6 | user02 | 2016-11-07 02:01:26 +0800 (Mon, 07 Nov 2016) | 1 line
	Changed paths:
	   M /trunk/HelloWorld.html
	
	change HelloWorld.html first.
	------------------------------------------------------------------------
	r5 | user01 | 2016-11-07 01:50:03 +0800 (Mon, 07 Nov 2016) | 1 line
	Changed paths:
	   M /trunk/HelloWorld.html
	
	
	------------------------------------------------------------------------
	r4 | user01 | 2016-11-07 01:45:43 +0800 (Mon, 07 Nov 2016) | 1 line
	Changed paths:
	   M /trunk/HelloWorld.html
	
	Add function to accept input and to display array contents
	------------------------------------------------------------------------
	r3 | user01 | 2016-11-07 01:42:35 +0800 (Mon, 07 Nov 2016) | 1 line
	Changed paths:
	   A /trunk/HelloWorld.html (from /trunk/helloworld.html:2)
	   D /trunk/helloworld.html
	
	
	------------------------------------------------------------------------
	r2 | user01 | 2016-08-23 17:29:02 +0800 (Tue, 23 Aug 2016) | 1 line
	Changed paths:
	   A /trunk/helloworld.html
	
	first file
	------------------------------------------------------------------------

### svn diff ###
用来检查历史修改的详情。

    检查本地修改

    比较工作副本与版本库

    比较版本库与版本库

**如果用 svn diff，不带任何参数，它将会比较你的工作文件与缓存在 .svn 的"原始"拷贝。**

	root@runoob:~/svn/runoob01/trunk# svn diff
	Index: rules.txt
	===================================================================
	--- rules.txt (revision 3)
	+++ rules.txt (working copy)
	@@ -1,4 +1,5 @@
	Be kind to others
	Freedom = Responsibility
	Everything in moderation
	-Chew with your mouth open

**比较工作拷贝和版本库**
比较你的工作拷贝和版本库中版本号为 3 的文件 rule.txt。

	比较你的工作拷贝和版本库中版本号为 3 的文件 rule.txt。

**比较版本库与版本库**
通过 -r(revision) 传递两个通过冒号分开的版本号，这两个版本会进行比较。

比较 svn 工作版本中版本号2和3的这个文件的变化：

	svn diff -r 2:3 rule.txt

### svn cat ###
如果只是希望检查一个过去版本，不希望查看他们的区别，可使用svn cat

	svn cat -r 版本号 rule.txt

这个命令会显示在该版本号下的该文件内容

### svn list ###
svn list 可以在不下载文件到本地目录的情况下来察看目录中的文件：

	$ svn list http://192.168.0.1/runoob01
	README
	branches/
	clients/
	tags/


## SVN 分支 —— copy ##
Branch 选项会给开发者创建出版本库的另外一个分支。

举例： 
项目 demo 下有两个小组，svn 下有一个 trunk 版。
由于客户需求突然变化，导致项目需要做较大改动，此时项目组决定由小组 1 继续完成原来正进行到一半的工作
（某个模块），小组 2 进行新需求的开发。

那么此时，我们就可以为小组2建立一个分支，
分支其实就是 trunk 版（主干线）的一个copy版，不过分支也是具有版本控制功能的，而且是和主干线相互独立的，
当然，到最后我们可以通过（合并）功能，将分支合并到 trunk 上来，从而最后合并为一个项目。

我们在本地副本中创建一个 my_branch 分支：

	root@runoob:~/svn/runoob01# ls
	branches  tags  trunk
	root@runoob:~/svn/runoob01# svn copy trunk/ branches/my_branch
	A         branches/my_branch
	root@runoob:~/svn/runoob01# 

查看状态：

	root@runoob:~/svn/runoob01# svn status
	A  +    branches/my_branch
	A  +    branches/my_branch/HelloWorld.html
	A  +    branches/my_branch/readme

提交新增的分支到版本库： 

	root@runoob:~/svn/runoob01# svn commit -m "add my_branch" 
	Adding         branches/my_branch
	Replacing      branches/my_branch/HelloWorld.html
	Adding         branches/my_branch/readme
	
	Committed revision 9.

接着我们就到 my_branch 分支进行开发：

	root@runoob:~/svn/runoob01# cd branches/my_branch/

如上，切换到my_branch 分支后，创建 index.html 文件：

	root@runoob:~/svn/runoob01/branches/my_branch# svn status
	?       index.html

	root@runoob:~/svn/runoob01/branches/my_branch# svn add index.html 
	A         index.html

	root@runoob:~/svn/runoob01/branches/my_branch# svn commit -m "add index.html"
	Adding         index.html
	Transmitting file data .
	Committed revision 10.

如上，就可以把新建的index.html文件提交到my_branch分支的版本库中

**将 my_branch 分支 合并到 trunk 分支**
首先切换到 trunk，执行 svn update，
然后将 my_branch 分支合并到 trunk 中：

	root@runoob:~/svn/runoob01/trunk# svn merge ../branches/my_branch/
	--- Merging r10 into '.':
	A    index.html
	--- Recording mergeinfo for merge of r10 into '.':
	 G   

将合并好的 trunk 提交到版本库中：

	root@runoob:~/svn/runoob01/trunk# svn commit -m "add index.html"
	Adding         index.html
	Transmitting file data .
	Committed revision 11.