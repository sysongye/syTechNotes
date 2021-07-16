# <font color=#69D600>Linux</font>

[TOC]

### Linux	[content](00_Linux.md)



### 简介

#### Linux 之父

​		**林纳斯·班奈狄克·托瓦兹	Linus Benedict Torvalds**

​		1969 年 12 月 28 日 生于芬兰赫尔辛基市，拥有美国国籍

​		Linux 内核的最早作者，随后发起了这个开源项目，担任 Linux 内核的首要架构师与项目协调者，是当今世界最著名的电脑程序员、黑客之一。

##### Linux

​		一种自由和开放源码的 **类 UNIX** 操作系统。该操作系统的内核由 Linus Torvalds 在 1991 年 10 月 5 日首次发布，在加上用户空间的应用程序之后，成为 Linux 操作系统。主要受到 Minix 和 Unix 思想的启发，是一个基于 POSIX 的多用户、多任务、支持多线程和多 CPU 的操作系统。

​		Linux 的诞生显得充满了偶然。Linus 经常要用他的终端仿真器（ Terminal Emulator ）去访问大学主机上的新闻组和邮件，为了方便读写和下载文件，他自己编写了磁盘驱动程序和文件系统，这些在后来成为了 Linux 第一个内核的雏形。当时，他年仅21岁。 

​		在自由软件之父理查德·斯托曼（ Richard Stallman ）某些精神的感召下，Linus 很快以 Linux 的名字把这款类 Unix 的操作系统加入到了自由软件基金（FSF）的 GNU 计划中，并通过 GPL 的通用性授权，允许用户销售、拷贝并且改动程序，但你必须将同样的自由传递下去，而且必须免费公开你修改后的代码。这说明，Linux 并不是被刻意创造的，它完全是日积月累的结果，是经验、创意和一小段一小段代码的集合体。

​		Linus Torvalds 最早发布到 comp.os.minix 的小民意调查：

```
// Just for fun
(德文版抄录)
From: torvalds@kruuna.Helsinki.Fi(Linus Benedict Torvalds)
Newsgroup: comp.os.Minix
Subject: Welche Eigenschaften würdest du am liebsten in Minix realisiert sehen? （你在 Minix 中最想看到什么？）
Summary: Kleine Meinungsumfrage für mein neues （关于我新操作系统的小民意调查）
Betriebssystem Message-ID: <1991Aug25.205708.9541@klaava.Helsinki.Fi>

(英文版抄录)
Hello everybody out there using minix – 

I’m doing a（free）operating system（just a hobby，won’t be big and professional like gnu）for 386（486）AT clones. This has been brewing since April，and is starting to get ready. I’d like any feedback on things people like /dislike in minix，as my OS resembles practical reasons among other things.

I’ve currently ported bash（1.08）and gcc（1.40），and things seem to work. This implies that I’ll get something practical within a few months，and I’d like to know what feathers most people would want. Any suggestions are welcome，but I won’t promise I’ll implement them：-)

Linus（torvalds@kruuna.helsinki.fi）

Ps. Yes – it’s free of any minix code ，and it has a multi-threaded fs. It is NOT portable（uses 386 task switching etc）. and it probably never will support anything other than AT-hard-disks，as that’s all I have：-(
```



##### Git

​		2005年，Samba 开发者 Andrew Tridgell 写了一个简单程序，可以连接 BitKeeper 的存储库，BitKeeper 著作权拥有者 Larry McVoy 认为 Andrew 对 BitKeeper 内部使用的协议进行逆向工程，决定收回无偿使用 BitKeeper 的许可。Linux 内核开发团队与 BitMover 公司进行磋商，但无法解决他们之间的歧见。Linus 决定自行开发版本控制系统替代 BitKeeper，以十天的时间编写出 Git 第一个版本。

​		Linus 讽刺地嘲笑 git 这个名字（在英式英语俚语中表示“不愉快的人”）

​		源代码的自述文件进一步阐述了：

```
The name "git" was given by Linus Torvalds when he wrote the very first version. He described the tool as "the stupid content tracker" and the name as (depending on your way):
	random three-letter combination that is pronounceable, and not actually used by any common UNIX command. The fact that it is a mispronunciation of "get" may or may not be relevant.
	"global information tracker": you're in a good mood, and it actually works for you. Angels sing, and a light suddenly fills the room.
	stupid. contemptible and despicable. simple. Take your pick from the dictionary of slang.

Linus 在编写第一个版本时就使用了“git”这个名称。 他将工具描述为“愚蠢的内容跟踪器”，并将其描述为（取决于您的方式）：
	可以发音念出的随机三个字母组合，而且并未被实际用在任何 UNIX 指令上。它是“get”的错误发音，这点可能相关也可能无关。
	“全球信息跟踪器”：您的心情不错，对你而言它也确实说得通。天使唱歌，房间突然充满光明。
	愚蠢的。鄙视和卑鄙的。简单。从俚语字典中选择。
```







### DONE