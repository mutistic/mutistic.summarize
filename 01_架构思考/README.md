# <a id="a_top"></a> [架构思考大纲](https://github.com/mutistic/mutistic.summarize/tree/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83)
### 目录：
1. <a href="#a_01">架构概述</a>
98. <a href="#a_notes">Notes</a>
99. <a href="#a_down">down</a>


|作者|Mutistic|
|---|---|
|邮箱|mutistic@qq.com|

---
## <a id="a_01"></a>[一、架构概述](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md) <a href="#a_top">top</a> <a href="#a_02"></a>
#### 目录  
[一、分布式架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_01)  
[1.1、为什么需要分布式架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_11)  
[1.2、理解单体架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_12)  
[1.3、理解分布式架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_13)  
[1.4、分布式架构的特征](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_14)  
[1.5、理论/思想](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_15)  
[1.6、关键词](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_16)  
[二、微服务架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_02)  
[2.1、什么是微服务架构](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_21)  
[2.2、微服务的出现与发展](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_22)  
[2.3、微服务的特征](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_23)  
[2.4、如何具体实践微服务](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_24)  
[2.5、常见的微服务设计模式和应用](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_25)  
[2.6、微服务的优点和缺点](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_26)  
[2.7、相关扩展](https://github.com/mutistic/mutistic.summarize/blob/master/01_%E6%9E%B6%E6%9E%84%E6%80%9D%E8%80%83/1_%E6%9E%B6%E6%9E%84%E6%A6%82%E8%BF%B0.md#a_27)  


---
### <a id="a_notes">[Notes](https://github.com/mutistic/mutistic.mybatis/blob/master/com.mutistic.mybatis/notes)</a> <a href="#">last</a> <a href="#a_down">down</a>
[01_架构思考思维导图](https://github.com/mutistic/mutistic.mybatis/blob/master/com.mutistic.mybatis/notes/Postman_SpringBoot-MyBatis.json)  
![]()

---
<a id="a_down"></a>  
<a href="#a_top">Top</a> 
<a href="#a_catalogue">Catalogue</a>