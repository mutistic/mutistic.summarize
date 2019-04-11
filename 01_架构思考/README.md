# <a id="a_top"></a> [架构思考](https://github.com/mutistic/mutistic.summarize/tree/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83)
### 目录：
1. <a href="#a_01">架构概述</a>
99. <a href="#a_down">down</a>


|作者|Mutistic|
|---|---|
|邮箱|mutistic@qq.com|

---
## <a id="a_01"></a>[一、架构概述](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md) <a href="#a_top">top</a> <a href="#a_02"></a>
目录
1. [一、分布式架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_01)  
2. [1.1、为什么需要分布式架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_11)  
3. [1.2、理解单体架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_12)  
4. [1.3、理解分布式架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_13)  
5. [1.4、分布式架构的特征](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_14)  
6. [1.5、理论/思想](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_15)  
7. [1.6、关键词](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_16)  
8. [二、微服务架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_02)  
9. [2.1、什么是微服务架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_21)  
10. [2.2、微服务的出现与发展](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_22)  
11. [2.3、微服务的特征](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_23)  
12. [2.4、如何具体实践微服务](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_24)  
13. [2.5、常见的微服务设计模式和应用](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_25)  
14. [2.6、微服务的优点和缺点](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_26)  
15. [2.7、相关扩展](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_27)  
