---
layout: post
title: 在MAC中用命令行快速切换目录
category: 乱语
tags: mac 命令行
---
{{ page.title }}
================
<p class="meta">2013年9月11日 - 郑州</p>
感谢[伯乐在线](http://blog.jobbole.com/46258/)翻译的文章[Quickly navigate your filesystem from the command-line](http://jeroenjanssens.com/2013/08/16/quickly-navigate-your-filesystem-from-the-command-line.html)。


因为最近在学习程序语言，在尝试使用github等工具，少不了经常使用命令行工具。一遍遍输入`cd ~/users/someone/...`每次真的很麻烦。看了伯乐在线分享的翻译教程，使用符号链接跳转很easy地解决了这个问题。

该方法需要先cd到目标目录，然后执行`mark`命令建立符号链接。

	$ cd ~/desktop/fjy/desktop
	$ mark d

然后在文件系统的任何地方输入以下命令都会跳转到我的桌面目录。

	$ jump d

输入`unmark`命令移除符号链接。

	$ unmark d

输入`marks`命令浏览所有的标签。

	$ marks
	d -> /users/fjy/desktop

MAC版本复制了评论中smarty_kiki的代码：


	# mark
	export MARKPATH=$HOME/.marks
	export MARKDEFAULT=kiki
	 
	function jump {
	    local m=$1
	    if [ "$m" = "" ]; then m=$MARKDEFAULT; fi
	    cd -P "$MARKPATH/$m" 2>/dev/null || echo "No such mark: $m"
	}
	function mark {
	    mkdir -p "$MARKPATH"
	    local m=$1
	    if [ "$m" = "" ]; then m=$MARKDEFAULT; fi
	    rm -f "$MARKPATH/$m"
	    ln -s "$(pwd)" "$MARKPATH/$m"
	}
	function unmark {
	    local m=$1
	    if [ "$m" = "" ]; then m=$MARKDEFAULT; fi
	    rm -i "$MARKPATH/$m"
	}
	function marks {
	    ls -l "$MARKPATH" | grep ^l | cut -d ' ' -f 13-
	}
	 
	_completemarks() {
	    local curw=${COMP_WORDS[COMP_CWORD]}
	    local wordlist=$(ls -l "$MARKPATH" | grep ^l | cut -d ' ' -f 13)
	    COMPREPLY=($(compgen -W '${wordlist[@]}' -- "$curw"))
	    return 0
	}
	1
	complete -F _completemarks jump unmark

使用步骤如下：

建立marks目录储存符号链接

	$ cd ~
	$ mkdir .marks

打开root目录下的隐藏文件.bashec

	$ sudo subl .bashec

将上面的代码复制到这个文件中。保存关闭。

这个时候命令并不能立刻生效。需要重新加载`.bashrc`修改后的设置。

>.bashrc主要保存个人的一些个性化设置，如命令别名、路径等。

一般会在`.bash_profile`中显示的调用`.banshrc`,重新加载设置的命令是：

	$ source ~/.bash_profile

重新加载后就可以享受便捷的乐趣了。

WINDOWS及MAC OS X的详细解释请参考[原文](http://blog.jobbole.com/46258/)。
