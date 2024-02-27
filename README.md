# 概述

本文是原Gopher(Golang云原生栈)因为校招offer的岗位需求故转型Java栈(Java业务栈)而在学习过程中所做的笔记，笔记的核心理念是在Java的学习历程中去多做Golang与Java在语法、性能、特性、理念等方面的**类比**

从作者的本意上出发，是希望在学习Java的历程中保持做一些有思考历程、具有总结意义的笔记，以达到在日后更深层次的Java学习中遇到一些集成基础理念而封装的框架、高级特性等时能够有可以重温的个人沉淀，以帮助学习新技术的理解效率与尽快形成可以创造实际价值的应用视角，应对未来的业务挑战，同时也是作者本人在写作能力上的一次提升尝试

该文档在内容划分上主要参考了韩顺平老师的B站课程[《【零基础 快速学Java】韩顺平 零基础30天学会Java》](https://www.bilibili.com/video/BV1fh411y7R8)，以及在部分小节的中的知识点的概念理解上阅读与参考了《Java核心技术卷》

**万丈高楼平地起**，于作者本人的学习理念上，认为学习任何技术都应打好基础，有一定的个人沉淀和理解历程。如果该文档能作为同样原本以Golang为主语言的同学在过渡到以Java为主语言这一阶段、或在增添Java技术栈时的学习参考文档，或仅仅是对您在Java或Golang的学习历程中有所帮助，都是我莫大的荣幸

因为作者水平十分有限，不正之处、或您认为的有所补充之处亦或含义更正确之处，恳请您不吝指正，同时十分欢迎您能提出issus，若您的issus被采纳，我将会关注您

# 本文特色

- **在基础部分附有大量Golang与Java的对比**，十分适合Gopher在转型Java栈时或对Golang有兴趣的Java同学参考，比如在**方法重写**这一小节中:


***

  <img width="833" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/fdbfe712-22bb-464d-9d06-311365a4454a">
  
***

***

<img width="838" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/9e9e2ec0-6861-4dd6-a64f-a24dfb0e5c6a">

***

​   您可以在本文中参考到很多诸如上述的对比例子

- 在本文中您可以参考到很多**基于作者个人视角的思考**，或许能帮助您拓宽对某个知识点的理解面与开拓不同的理解方式，比如在**浅谈接口的实际意义**这一小节中:
***

  <img width="834" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/9cc1b864-84fe-4af7-b072-685830a2a822">

***
  您可以看到很多作者基于客观基础知识的主观性思考

- **本文重试以Demo论述知识点**，附大量代码，比如在**向下转型**这一小节中，您可以看到:
***

  <img width="833" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/171dea58-1a79-4ccd-8fc3-494c0feafc1b">

***
- **本文在不同知识点的详细程度上会有的放矢**，因为本意是完成Go->Java的转型，故最大的转变个人认为是由函数式编程->纯面向对象这一理念上的转变，故该偏向性造成不同章节的篇幅差别较大，比如**数组**和**包**由于其与其他知识点的隔离性，仍作为了独立章节进行分享，但由于其属于**语言共性**的内容故其中所包含的字符数分别有2181、627个，而**类与对象**这一章节由于其是转型到面向对象编程理念的核心章节，故有42622个字符

- **本文是基础向文章，以打好基础和浅识原理为主**，故少有啃源码的篇幅

- **本文重视总结**，尤其是在学习到某一接口、集合时，会总结出常用的方法，可供您在需要时查询，比如在**List接口**这一章节中:
***

  <img width="675" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/794aaa06-17f8-4fe0-8c90-0585cf0eb811">

***
- 本文涵盖较全的Java基础知识点，共计有**162139**字符

# 章节概览

顺序遵从本文的章节排列顺序

- [Java概述](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C1-JavaIntroduce/C1-JavaIntroduce.md)
- [基础语法](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C2-BasicSyntax/C2-BasicSyntax.md)
- [数组](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C3-Array/C3-Array.md)
- [类与对象](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C4-ClassAndObject/C4-ClassAndObject.md)
- [包](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C5-Package/C5-Package.md)
- [抽象类与接口](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C6-AbstractClassAndInterface/C6-AbstractClassAndInterface.md)
- [枚举类和注解](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C7-EnumerateClass%26Annotations/C7-EnumerateClass%26Annotations.md)
- [异常处理](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C8-ExceptionHanding/C8-ExceptionHanding.md)
- [常用类](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C9-CommonClass/C9-CommonClass.md)
- [集合](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C10-Collection/C10-Collection.md)
- [泛型](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C11-Generics/C11-Generics.md)
- [并发编程基础](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C12-BasicConcurrentProgramming/C12-BasicConcurrentProgramming.md)
- [反射](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/C13-Reflection/C13-Reflection.md)

其中在初版摒弃了I/O这一章节，原因在个人浅薄的理解上，对比Golang中的I/O操作，除了其类图外有较强的在基础层面上的学习价值外，用法与理念和Golang中类似，故做省略，在后续更新中会增添这一章节

关于章节详情您可以查看仓库目录下的[MENU.md](https://github.com/steaksunflower0107/JavaLearingAsGopher-BasicPart/blob/main/MENU.md)，可供您进行选择性阅读
