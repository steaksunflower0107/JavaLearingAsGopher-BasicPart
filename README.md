# 概述

本文是原Gopher(Golang云原生栈)因为校招offer的岗位需求而需转型Java栈(Java业务栈)而在学习过程中所做的笔记，核心理念是在Java的学习历程中去多做Golang与Java在语法、性能、特性、理念等方面的**类比**

从作者的本意上出发，是希望在学习Java的历程中保持做一些有思考、具有总结意义的笔记，以达到在日后更深层次的Java学习中遇到任何应用基础理念所封装的框架、高级特性等时能够有可以重温的个人沉淀，以帮助学习新事物的理解效率与形成可以创造实际价值的应用视角，应对未来的业务挑战

该文档在内容划分上主要参考了韩顺平老师课程[《【零基础 快速学Java】韩顺平 零基础30天学会Java》](https://www.bilibili.com/video/BV1fh411y7R8)

以及在文档中部分小节的概念理解上阅读与参考了《Java核心技术卷》

**万丈高楼平地起**，于作者本人的学习理念上，认为学习任何技术都应打好基础，有一定的个人沉淀。如果该文档能作为以Golang为主语言者在过渡到以Java为主语言这一阶段的参考文档、或在增添Java技术栈时的学习参考文档，或仅仅是对您有所帮助，是我莫大的荣幸

因为作者水平十分有限，不正之处、您认为的有所补充之处或含义更正确之处，恳请您不吝指正以及欢迎您提的issus，若您的issus被采纳，我将会关注您

# 本文特色

- **在基础部分附有大量Golang与Java的对比**，十分适合Gopher或对Golang有兴趣的Java同学参考，比如在**方法重写**这一小节中:
  <img width="833" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/fdbfe712-22bb-464d-9d06-311365a4454a">


<img width="838" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/9e9e2ec0-6861-4dd6-a64f-a24dfb0e5c6a">


​   您可以在本文中参考到很多诸如上述的对比例子

- 在本文中您可以参考到很多**基于作者个人视角的思考**，或许能帮助您拓宽对某个知识点的理解面，比如在**浅谈接口的实际意义**这一小节中:

  <img width="834" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/9cc1b864-84fe-4af7-b072-685830a2a822">


  您可以看到很多作者基于客观基础知识的主观性思考

- **本文重试以Demo论述知识点**，附大量代码，比如在**向下转型**这一小节中，您可以看到:

  <img width="833" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/171dea58-1a79-4ccd-8fc3-494c0feafc1b">


- **本文在不同知识点的详细程度上会有的放矢**，因为本意是Go->Java的转型，故最大的转变个人认为是由函数式编程->纯面向对象的转变，由此该偏向性造成不同章节的篇幅差别较大，比如**数组**和**包**由于其与其他知识点的隔离性，仍作为了独立章节进行笔记分享，但由于其并不是转型的关键点故其中所包含的字符数分别有2181、627个，而**类与对象**这一章节因其是转型到面向对象编程理念的核心章节，故有42622个字符

- **本文是基础向文章，以打好基础和浅识原理为主**，故少有深啃源码的篇幅

- **本文重视总结**，尤其是在学习到某一接口、集合时，会总结出常用的方法，可供您在需要时查询，比如在**List接口**这一章节中:

  <img width="675" alt="image" src="https://github.com/steaksunflower0107/JavaLearingAsGopher-BasePart/assets/112318617/794aaa06-17f8-4fe0-8c90-0585cf0eb811">


- 本文涵盖较全的Java基础知识点，共计有**162139**字符

# 章节概览

顺序遵从本文的章节排列顺序

- Java概述
- 基础语法
- 数组
- 类与对象
- 包
- 枚举类和注解
- 异常处理
- 常用类
- 集合
- 泛型
- 并发编程基础
- 反射

其中在初版摒弃了I/O这一章节，原因在个人浅薄的理解上除了其类图相对Golang中的I/O操作有较强的在基础层面上的学习价值外，用法与理念和Golang中类似，故做省略，在后续更新中可能增添这一章节

关于章节详情您可以查看MENU.md进行选择性阅读



