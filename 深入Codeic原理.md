#深入Codeic原理

本节我们将深入讨论Codeic的原理。

##Scratch扩展
Scratch扩展（Scratch Extensions）使得Scratch有能力与其他程序（如Codeic、LEGO WeDo或者PicoBoard）进行交互。这些程序通过一些列的命令和返回代码块与Scratch进行交互，从而让Scratch可以完成一些更底层的任务，这些代码块都被包含在**更多模块**里。

Codeic正是通过Scratch扩展功能与Scratch实现交互的。

##Codeic的扩展文件
在Codeic的扩展文件即Codeic.json中有如下代码：

	{
	    "extensionName": "Codeic",
	    "extensionPort": 8384,
	    "host": "localhost",
	    "useHTTP": true,
	    "blockSpecs":[
	        ["w","打开程序 %s", "CDPL:LaunchProgram","Console.exe"]
	    ]
	}

可以看到，Codeic的扩展所用到的端口是8384，地址是localhost，包含一个代码块**打开程序**

##侦听端口交互
Codeic在成功运行后就开始侦听一个端口：8384，Scratch正是通过这个端口与Codeic实现交互。当Scratch运行到**打开程序**代码块的时候，Scratch向Codeic发送一条命令"CDPL:LaunchProgram"，默认参数为"Console.exe"，这在Codeic的扩展文件中可以找到。

Codeic接收到这个命令和参数后，便开始在Codeic根目录的Programs文件夹中寻找名为Console.exe的程序并打开它。像Console.exe这样的，通过向Codeic发送"CDPL:LaunchProgram"运行的程序称为**扩展程序**。

##Codeic的扩展库
Codeic包含扩展库，这些扩展库都存放在Codeic根目录下的Libs文件夹中。其中最重要的扩展库是CDPL(CodeicProgramLauncher)，是Codeic的扩展程序启动器，用来启动扩展程序。

调用扩展库命令的格式为:

	[扩展库名:]命令名

如：CDPL:LaunchProgram

其中，扩展库名是可选的，如果扩展库名为空，那么会调用默认的基础扩展库"Essential"，这个扩展库包含在Codeic的核心程序中。

##扩展库调用扩展程序
Codeic提供的可调用扩展程序的扩展库为CDPL库，当然用户也可以自定义自己的扩展库调用其他程序。

Codeic接收到"CDPL:LaunchProgram"命令时，会在已加载的扩展库中寻找名为"CDPL"的扩展库，然后给这个扩展库"LaunchProgram"命令及参数"Console.exe"。CDPL接收到这个命令后，会在扩展程序找到名为Console.exe的扩展程序，然后打开它。

##Scratch与扩展程序交互
要调用扩展程序则需要添加相应的扩展程序的扩展文件，例如Console.exe的扩展文件Console.json。

	{
	    "extensionName": "Console",
	    "extensionPort": 8000,
	    "host": "localhost",
	    "useHTTP": true,
	    "blockSpecs":[
	        [" ","输出 %s", "writeLine","信息"],
	        [" ","退出","exit"]
	    ]
	}

这是Console.json的内容，其中可以看到与Codeic的扩展文件不同的是，Console的侦听端口是8000。

CDPL启动Console.exe时，Console.exe便开始侦听8000端口，Scratch里的Console扩展的指示灯则会由红变绿，Scratch便会通过8000端口与Console.exe进行交互，可以输出信息，也可以让Console.exe退出。

##扩展库与扩展程序的不同
扩展库的运行依附在Codeic核心上，侦听的端口与Codeic核心一样，默认是8384，并且Codeic调用扩展库使用的是单线程，如果一个扩展库发生阻塞，那么核心也会受到阻塞。

扩展程序由CDPL启动，侦听的端口可自定义，与Codeic核心是异步进行。






