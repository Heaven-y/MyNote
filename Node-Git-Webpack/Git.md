vscode配置

Terminal › Integrated › Profiles: Windows

"Git-Bash": {"path": "D:\\Git\\Git\\bin\\bash.exe" }



三者区别

* Bash
  * Unix shell 的一种，Linux 与 Mac OS X 都将它作为默认 shell
  * Git Bash基于 CMD 的，在 CMD 的基础上增添一些新的命令与功能
* CMD
  * 命令行提示符（CMD）是 Windows 操作系统上的命令行解释程序
* GUI
  * 提供了一个图形用户界面来运行 git 命令



SSH

公钥放入远程服务器

* ssh-keygen -t ed25519(加密类型) -C "your email"
* ssh-keygen -t rsa -b 2048 -C  "your email"



![image-20221104212718113](img/image-20221104212718113.png)

检测文件状态-git status  (-s)

* Untracked
  * 默认情况下，文件没有添加到Git仓库管理中，我们需要通过add命令来操作
* tracked
  * staged：暂缓区中的文件状态
  * Unmodified：使用commit命令，可以将staged中文件提交到Git仓库
  * Modified：修改了某个文件后，会处于Modified状态



打印记录信息

* git log(按q退出  space往下查找)
  * 按时间顺序 新->旧
  * --pretty=oneline  (--graph(图结构))
* git reflog
  * 也保存了reset后的信息



版本回退 git reset --hard

* HEAD 当前分支的指针，记录该分支上最后一次提交
  * 上个版本HEAD^，上上个版本HEAD^^，上num版本HEAD~num
* 也可也指定commit id



git clone后本地的master分支默认跟踪了远程的origin/master



git commit -a -m "description"



git remote (-verbose)  查看远程地址

git remote add <shotrname\> <url\> 添加远程地址

* (通过http添加)直接git pull 报错
  * 只是连接了远程仓库，未指定分支间的连接
  * git branch --set-upstream-to=origin/master 给本地的分支设置上游分支(跟踪分支) --解决git fetch
  * git merge --allow-unrelated-histories



git pull = git fetch + git merge(rebase)







