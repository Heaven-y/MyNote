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



文件状态

* Untracked
  * 默认情况下，文件没有添加到Git仓库管理中，我们需要通过add命令来操作
* tracked
  * staged：暂缓区中的文件状态
  * Unmodified：commit命令，可以将staged中文件提交到Git仓库
  * Modified：修改了某个文件后，会处于Modified状态



检测文件状态-git status























