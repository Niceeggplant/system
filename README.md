# IMKB
本科毕设：打造了一个制造业知识图谱展示和管理的平台。 使用了爬虫爬取网络上制造业的数据，通过分词和相似度匹配抽取制造业本体。 web程序使用了SpringBoot+Vue+Element-UI+D3.js，实现了知识图谱的可视化和系统管理。

**声明：本项目前后端工程借鉴了网上的教学视频，框架与其大致相似，链接为：https://www.bilibili.com/video/BV1HA411p7aA?spm_id_from=333.999.0.0**

# 文件：
- back: 后端工程，使用Idea开发，具体的说明，请进入文件夹后查看md文件
- front：前端工程，使用webstorm开发，具体的说明，请进入文件夹后查看md文件
- spider：爬虫文件，使用的是Scrapy框架，主要作用就是爬取百度百科中有关制造业的词条，形成用于展示的知识图谱的节点和关系
- word2vec：用来进行词条相似度计算，主要是过滤掉那些与制造业无关的词条内容，这里有很多成熟的中文自然语言处理工具可以使用，我使用的是jieba分词。后面这两个文件夹中的内容主要就是为了获取一些可以用来展示的节点和关系信息。我当时就把百度百科的词条作为节点，词条的一些其他内容作为节点属性，节点之间的关系就简单的进行了定义，能把所有的节点连接起来就好。
- 演示视频：毕业答辩时候用来进行系统演示的视频，主要是前端的展示

# Scrapy爬虫
Scrapy是使用Python编写的一个网络爬虫框架， Scrapy框架中有两类爬虫，CrawlSpider是Spider的一个子类，除了能够像Spider一样可以爬取url列表中的网页外，还定义了规则(Rule)来从爬取到的网页中按照定义的规则获取链接到其他页面的url，并加入到url列表中继续进行爬取。
4.2.2  数据获取
百度百科中网络数据获取的流程如图所示，给定初始url后，由定义好的爬虫框架对百科内容进行爬取，将与已有的制造业词汇的相似度超过60%的链接继续加入到url队列中进行深度爬取。

 ![image](https://user-images.githubusercontent.com/46115362/170701628-4ba2d31e-7a7c-458d-ae7f-c7c6dc27e7d1.png)


在进行数据爬取工作之前，需要对百度百科网页的链接进行分析，如搜索关键词“制造业”，百度百科页面的网址为https://baike.baidu.com/item/制造业，
这样很容易就可以看到，百度百科的url结构为https://baike.baidu.com/item/{词条}，
只需要改变词条的内容就可以获得相应的搜索内容。这样，就可以将《国民经济行业分类》中所有制造业的分类条目，作为搜索的词条名称，放入到初始url中，然后以这些网址为起点进行爬取。
定义好初始url后，需要对链接提取器和链接过滤规则进行定义。这里需要对整个百科词条的内容分布进行分析，词条的主要组成部分如表所示。

![image](https://user-images.githubusercontent.com/46115362/170701826-22c9392f-f496-429b-88f2-6f1ab30ec20f.png)


获取到页面中的链接地址后，接下来需要对提取到的链接进行过滤。由于在网页中，每一个链接地址都是与百科词条名称相对应的，因此可以将该地址对应的名称与已有的词语进行相似度匹配，来确定该链接是否属于制造业的内容。该方法主要用到了4.1章节中提到的相似度计算方法，先将待计算的两个词利用训练好的Word2vec模型转换成词向量，然后使用余弦相似度的度量方法计算这两个词向量之间的相似度。在进行相似度匹配的时候，会给定一个候选词库，这个词库是已经确定的属于制造业领域内的词汇。将待筛选的词逐个与候选词进行相似度判断，并按照相似度的大小从高到低进行排序，如果最终的相似度超过60%，那么就将该词对应的链接加入到初始url中，进行下一轮的爬取。


![image](https://user-images.githubusercontent.com/46115362/170701924-524e59b7-df96-42af-ab6c-2a5e547be268.png)

