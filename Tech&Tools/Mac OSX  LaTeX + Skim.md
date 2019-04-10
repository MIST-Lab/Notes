#Mac OSX  LaTeX + Skim

环境: Mac OSX Mojave 10.14.3 (18D109)



Step 1：下载MacTex  http://tug.org/mactex/

Step 2：下载Sublime Text 3 Beta http://www.sublimetext.com/3

Step 3：下载安装Skim http://skim-app.sourceforge.net

Step 4：在Sublime Text中安装Package Control https://sublime.wbond.net/installation 

Step 5：在Sublime Text中安装LatexTools

在Sublime Text里Command+Shift+P调出命令窗口，输入Install，之后选择LaTexTools

Step 7：Skim同步配置

Step 8: 修改编译和中文环境

点击Browse Package，然后

打开编辑~/Library/Application\ Support/Sublime\ Text/Packages/Users/LaTeX.sublime-settings，将其中”builder”条目改为： 
“builder”: “simple” 
接着打开编辑~/Library/Application\ Support/Sublime\ Text/Packages/LaTeXTools/builders/simpleBuilder.py 
找到其中大概第41行 
pdflatex = [“pdflatex”, “-interaction=nonstopmode”, “-synctex=1″] 
修改为 
pdflatex = [“xelatex”, “-interaction=nonstopmode”, “-synctex=1″] 
保存后关闭，重新编译一下，中文正常啦！

OK,经过上面的步骤，整个Latex环境就搭建完毕了，下面做个测试：

在Sublime Text 输入如下代码：

\documentclass{article}

\usepackage{fontspec, xunicode, xltxtra}

\setmainfont{Hiragino Sans GB}

\title{Title}

\author{}

\begin{document}

\maketitle{}

\section{Introduction}

This is where you will write your content. 在这里写上内容。

\end{document}

保存后按 Command+B 编译，Skim直接显示： 



![image-20190410233538348](/Users/junchengzhu/Library/Application Support/typora-user-images/image-20190410233538348.png)



来源：CSDN 
原文：https://blog.csdn.net/songrotek/article/details/46457571 
版权声明：本文为博主原创文章，转载请附上博文链接！