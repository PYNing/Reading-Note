**主线**：重现bug------探查bug------解决bug

**对软件开发的作用**：书里的所有条目都是针对故障的诊断与调试写的，不过其中有很多建议通用来用用来缩减代码中的bug数量，并且可以使你在遇到这样的bug时能够更为迅速地将其修复。严谨的调试技术和优秀的软件开发方式之间能够形成良性的循环。

**值得思考的问题**：在修复完bug之后，怎样防止有人向程序中引入类似的bug？



# **整书布局**：

**Part1 策略与方法**

第一章 调试策略（第1条\~第8条）

第二章 调试方法

第三章 调试时所用的工具和技术

**Part2 各阶段的具体技巧**

第四章 调试器

第五章 编写程序

第六章 编译软件

第七章 运行系统

**Part3 调试多线程的代码**

第八章 调试多线程的代码



# 第一章 宏观策略

### 第1条：通过事务追踪系统处理所有的问题

**要点：**

（1）确保每项事务都能够以短小（short）、自足（self-contained）而又正确（correct，即可以正确编译并运行）的范例（example），精确地描述出该问题的重现方式。

（2）对事务进行分类，并根据每项事务的优先级与严重程度来安排工作。

（3）通过事务追踪系统来记录进度。

**理解：**

典型的事务追踪系统（issue-tracking system）如Git，Git相应的事务追踪就是Commit。每次Commit将信息写详细，这有利于追溯。



### 第2条：在网上确切地查询你所遇到的问题，以寻求解决问题的灵感

**要点：**

（1）把错误消息打上双引号，以便在网上准确地进行搜索。

（2）认知查看StackExchange系列网站上面的回答。

（3）如果上述两种办法都不见效，可以提问或提交事务。

**理解：**

（1）给错误信息打上双引号搜索，达到过滤搜索引擎的无关搜索结果的目的；

（2）上searchcode.com等查询API正确的使用方法；

（3）Stack Overflow提问、github提issue，或发邮件。



### 第3条：确保前置条件与后置条件都能够达到满足

**要点**：仔细检查例程的前置条件和后置条件。

**理解**：前置条件即程序在即将执行例程时所具备的状态，以及传递给改例程的输入值。后置条件即程序执行完后的状态及返回值。



### 第4条：从具体问题入手向上追查bug，或从高层程序入手向下追查bug

**要点：**

（1）如果能够明确指出故障的原因，那么应该从下往上查找错误，例如，在程序崩溃、程序冻结以及程序发错错误消息等情况下，就应该如此。

（2）如果故障很难锁定，那么应该从上往上查找错误，例如，在遇到性能问题、安全问题以及可靠性问题的时候，就应该如此。

**理解：**

（1）向上：从问题的具体表现入手，向上追查其来源。

（2）向下：从应用程序或系统的顶层入手，逐步向下探查，直至找到其根源。



### 第5条：在能够正常运作的系统与发生故障的系统之间寻找差别

**要点：**

（1）在能够正常运作的系统和出现故障的系统之间对比，找出行为上的区别，以求发现故障的原因。

（2）影响系统行为的所有原因都要考虑到，包括代码、输入、调用时的参数、环境变量、服务以及动态链接库。

**理解：**

（1）比较日志的差异。

（2）比较动态链接库的差异。

查看与可执行文件有关的动态链接库：ldd(Unix) 、带/dependents选项的dumpbin(Visual Studio)

查看程序所定义和使用的符号：nm(Unix)、带/exports/imports选项的dumpbin(Visual Studio)、javap（JAVA语言）

（3）比较环境变量差异。

（4）比较操作系统版本差异。

  

### 第6条：使用软件自身的调试机制

**要点**：找出你正在调试的这款软件所支持的调试机制，并以此排查你所遇到的问题。

**理解：**

（1）我们应该花一些时间，看看自己要调试的这款软件内置了哪些调试机制。

（2）例如，Unix Shell就提供了-x选项，用来详细展示该shell所执行的命令。

（3）常见的调试机制：

（a）禁用后台执行或多线程执行等特性来简化程序的调试工作。

（b）有选择地执行其中某一部分，以便通过测试用例来精确地再现相关故障。

（c）提供与性能相关的报表及其他信息。

（d）把更多的信息记录在日志文件中。

  

### 第7条：试着用多种工具构建软件，并将其放在不同的环境下执行

**要点：**

（1）用多种编译工具来构建软件，并将其放在各种平台下中执行，可以给调试工作提供很多有价值的思路。

（2）如果遇到一个很难调试的算法，那么可以考虑改用高级语言将其重新实现一遍。

**理解：**

（1）对于C和C++这样较为接近硬件的语言来说，底层处理器架构会对程序的行为造成影响。例如x86架构和ARM架构在处理未对其的内存访问及内存布局时有所区别。32bit/64bit架构，不同操作系统，长整形（long）及指针值等原始数据类型所占据的大小可能也会有所改变。

（2）使用不同的编译器：如果一款编译器对代码检查得比较宽松，而另一款编译器检查得比较严格，那么就可以揭示出移植方面的问题；如果一款编译器不对某个逻辑问题发出警告，而另一款编译器发出了警告，那么就可以揭示出逻辑方面的问题。



### 第8条：把工作交点放在最为重要的问题上

**要点：**

（1）并不是所有的问题都值得解决。

（2）修复优先级较低的问题可能会耽误你的时间，使你无法拿出更多时间去处理那些更为紧迫的事务。

**理解：**

（1）许多大型软件系统都含有数量及其众多的bug（有一些是已知bug，还有一些则尚未发现），想要高效地进行调试，就必须把应该受到关注的bug与可以忽略的bug明智地区分开。

（2）如果决定忽略某个优先级比较低的问题，那就应该把这个态度明确地表达出来。你可以在事务追踪系统里面表明自己"不打算解决"该问题。

  

# **第二章 通用的方法与做法**

### 第9条：相信自己能够把问题调试好

**要点：**

（1）确信问题是可以追查并解决的。

（2）给调试工作留出足够的时间。

（3）安排好工作环境，使自己不受干扰。

（4）遇到难题的时候可以先去睡一觉。

**理解：**

（1）如果你根本不相信自己能够克服这个问题，那你的思维就会徘徊不前，甚至想要干脆放弃，在这种情况下，你解决不了实质的问题，而是会盲目地打补丁，以掩盖该问题所引发的各种症状，这样做对代码是有害的。

（2）（对应要点第3点）要想调试复杂的问题，就必须在没有干扰的状态下工作。人脑需要经过一段时间之后才能进入心流（flow）状态。人在处于心流状态时，能够把自身情绪与自己所做的事情相互契合起来，并通过一种成就感来提升自己做事的毅力和效果。

（3）没有人会认为调试是一件简单的事情，所以要高效地调试，就必须有毅力。调试工作之所以陷入僵局，基本上都是因为缺乏毅力，例如，我们可能没有去写某个测试用例，没有去查看某个日志文件，或是没有去尝试从另外一个角度来研究问题。

（4）想成为一个高效的调试工程师，就必须持续地投入精力，去学习环境、工具及相关的知识。只有这样，你才能够在复杂度持续提升的技术工作中保存优势。现在看来，笔者当年在调试时最常犯的错误，就是没有花足够的功夫来把调试工作所需的基础设施搭建好。



### 第10条：高效地重视程序中的问题

**要点：**

（1）如果能够准确重现程序中的问题，那么我们的调试过程就会得到简化。

（2）创建一个简短且自足的范例，以便重现程序中的问题。

（3）设法创建一套可以制作副本的执行环境。

（4）采样版本控制系统给特定的软件版本打上标记，以便根据此标记来获取与之对应的代码。

**理解：**

（1）（对应要点第3点）我们还需要在可以制作副本的执行环境下进行调试。如果不把正在调试的代码与运行该代码的环境固定起来，那么你可能会在一个根本没有bug的地方去白费功夫。



### 第11条：修改完代码之后，要能够尽快能够看到结果

**要点：**

（1）设法在修改代码之后尽快看到其结果，以提升调试的效率（例如构建过程应该能够记录文件之间的依赖关系，使得我们在修改了某处代码之后只有少数几个文件需要重新编译）。

（2）配置一套快速的自动化构建及部署流程（例如使用脚本自动化执行部署文件、重启服务器、清除缓存、初始化数据库等过程）。

（3）测试软件时，要令其尽快地将故障暴露出来（例如单元测试，或是可以放在回归测试框架中进行测试）。

**理解：**

（1）构建软件的环节会占用很多时间，而且这些时间并没有用来解决软件中的问题，因此需要设法缩短每一轮调试所花费的时间。



### 第12条：将复杂的测试场景自动化

**要点：**

通过脚本语言来自动化执行复杂的测试用例

**理解：**

例如能够解析JSON数据的jq命令



### 第13条：使自己尽可能多地观察到与调试有关的数据

**要点：**

（1）如果能够同时看到比较多的数据，那么我们就可以更加专注地进行调试，从而找到数据所体现出的模型以及数据之间的相互联系。

（2）尽可能将显示区域扩至最大。

（3）把相对静态的数据打印到纸上。

**理解：**

（1）（对应要点第2点），如果日志文件和栈跟踪信息中的某些行比较长，那么为了符合这个80列宽的限制，它们会分成很多行来显示，而这样是不利于进行阅读和分析的。因此，我们可以使编译器在水平方向上尽量占满整个显示器，这样其中的某些模式就会自然地浮现出来。



### 第14条：考虑对软件进行更新

**要点：**

（1）在更新之后的环境里面重新尝试你所编写的代码，看看这次会不会出错。

（2）不要对更新软件所带来的效果抱有过高的期望。

（3）要考虑因第三方组件而引发bug的可能性。

**理解：**

（1）至少应该尝试用最新的版本来构建、链接或运行程序代码，以尽量消除因第三方组件而引入bug的概率。

（2）有很多中间件本身编写得就有错误，或是向后兼容性不太好。因此，有经验的用户通常会较为谨慎地升级，他们会升级到能够解决其问题，而且发布时间第二早的那个bug修复版本。

（3）如果升级失败，或者升级到的版本无法解决你所面对的bug，那么可以根据该方案退回原有的版本。

（4）对软件进行更新的最大意义，就在于给我们提供一个新的起点，令我们可以下定决心来好好清理自己的代码。



### 第15条：查看第三方组件的源代码，以了解其用法

**要点：**

（1）如果你依赖某个第三方组件，那就应该获取其源码。

（2）通过查看第三方组件的源代码探寻与第三方API及一些奇怪的错误消息有关的问题。

（3）要和第三方程序库的debug版本相链接。

（4）只有当其他办法都不可行的时候，才需要对第三方的源代码进行修改。

**理解：**

（1）在查看第三方程序库的代码时，我们可能并不是要在其中寻找bug，而是想更好地理解它的工作原理以及它与你所写代码之间的配合方式。

