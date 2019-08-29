# Git 入门

## 初始化
#### 创建本地仓库：
```
mkdir first
cd first
git init
```
#### 设置签名：
    项目级别：
        git config user.name [YourName]
        git config user.email [user@simple.com]
        cat .git/config 可查看信息
    项目级别：
        git config --global user.name [your name]
        git config --global user.email [user@simple.com]
        配置信息在系统目录 /.gitconfig
        项目级别和项目级别按就近原则；
#### 查看签名：
	上面的命令去掉 [] 和里面的内容即可


## 文件操作
**概念：工作区、暂存区、本地库、远程库**

    查看状态（工作区 暂存区）：
        git status
    
    编辑工作区文件：
    	vim or 其他编辑器
    
    添加/修改工作区文件到暂存区：
    	git add [file name]
    		新建文件需要先 add（被追踪） 才能 commit；已经 add 过的文件可以直接 commit
    
    文件从暂存区撤回：
    	git rm --cached [file name]
    
    提交到本地仓库：
    	1. git commit [file name]
    	2. 提示需要填写提交注释
    	两步合并一步：
    		git commit -m "注释..." [file name]
    
    版本前进和后退：
    	历史记录查看：
    		完整显示：git log
    		hash详细显示：git log --pretty=oneline
    		hash缩减显示：git log --oneline
    		显示历史记录步数：git reflog
    
    	HEAD指针与索引值（hash）
    
    	方式：
    		基于索引值后退/前进（推荐）：
    			1. 显示局部索引值：git reflog
    			2. 根据索引值回退/前进：git reset --hard [hash值]
    		使用符号^（只能后退）：
    			^ 的个数表示回退几步：git reset -hard HEAD^^^
    		使用符号~（只能后退）：
    			~ 后的数字表示后退几步：git reset -hard HEAD~3
    
    	查看文档说明：
    		git help [命令名称]
    
    	查看reset 的 3 个参数：git help reset
    		--soft：移动工作区 HEAD 指针
    		--mixed：移动工作区 HEAD 指针，暂存区刷新为与工作区同步
    		--hard：移动工作区 HEAD 指针，暂存区和本地库刷新为与工作区同步
    
    删除文件及找回：
    	删除：
    		工作区删除：rm [filename]
    		提交暂存区：git add [filename]
    		提交本地库：git commit -m "..." [filename]
    	找回：
    		通过 reset 命令，前提是删除前提交到暂存区或本地库
    
    比较文件差异：
    	工作区文件与暂存区比较：
    		git diff [filename]
    	工作区文件与本地库某一历史记录比较：
    		git diff [历史版本] [filename]
    		git diff HEAD^ [filename]：与上一版本比较
    	同时比较多个文件：
    		git diff


## 项目管理
**概念：分支**

    分支的应用场景：
    	避免 master 污染；
    	项目改动较大，几乎推倒重来；
    	开辟多个feature，并行推进各个小组工作，多个功能同时开发；
    	开辟新分支给缺乏经验的员工试错；
    	实验性开发，做样本或参考，万一 feature 分支开发失败，不会影响 master 主分支；
    	master 分支有bug，为避免停机开辟 hot_fix 分支进行 bug 修复，修复完再合并到 master；
    
    举例：
    	master分支、feature分支、hot_fix分支等
    
    查看分支：
    	git branch -v
    
    创建分支：
    	git branch [分支名]
    
    切换分支：
    	git checkout [分支名]
    
    合并分支：
    	举例：hot_fix 合并到 master
    		1. 切换到被合并的分支上：git checkout master
    		2. merge 命令选择分支：git merge hot_fix
    冲突解决：
    	手动修改冲突部分，然后按提示操作：
    		git add [filename]
    		git commit -m "..."（注意这里后面不用加文件名）
    
    删除分支：

## Git 原理
	hash 算法：
		值长度相等;
		不可逆;
		源数据发生微小变化也会引起 hash 值发生很大变化;
	
	git 与 svn 对比：
		svn：集中式服务；增量式保存，节省磁盘空间;
		git：分布式服务；快照式保存，未发生修改的文件只创建指针指向上一版本;
		两者分支原理区别：
			git 基于文件快照，分支切换实际上仅是分支指针的移动；
			svn 创建分支需要完整复制一份项目文件
			举个例子：
				git 创建分支相当于 String a1 = "a"; String a2 = "a";
				svn 创建分支相当于 String b1 = new String("b"); String b2 = new String("b");

## 远程仓库
	推送到远程仓库
		1. 创建远程仓库：Chain
		2. 复制仓库地址：https://github.com/island404/Chain.git
		3. 起别名：
			git remote add [别名] [仓库地址] （别名可以与仓库名不同，但是一般设置相同）
			git remote add chain https://github.com/island404/Chain.git
		4. 查看别名以及地址：
			git remote -v
		5. 推送：
			git push [别名] [分支名]
			git push chain master
	
	远程仓库克隆
		1. 切换到某一文件夹，打开 git bash
		2. 输入 git clone [仓库地址]
		clone 命令的效果：
			下载完整的整个远程库；
			自动创建别名origin；
			自动初始化本地仓库（不需要手动初始化）。

