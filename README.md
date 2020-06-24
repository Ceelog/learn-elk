# ELK 实践小册

本小册全面介绍 ELK Stack 包含的各个组件及其使用方法，让零基础的读者也能轻松搭建整个套件，并运用到实际的生产环境中。

在示例中，我们将打造一个大规模日志实时分析系统，实现每天数十亿条日志或者 PB 级数据的分析处理。

## 在线阅读

[https://roadmapedu.com/c/Ceelog/ELKStack实践小册/](https://roadmapedu.com/c/Ceelog/ELKStack%E5%AE%9E%E8%B7%B5%E5%B0%8F%E5%86%8C/)

## 在线演示环境

由于 Elastic Stack 涉及的组件比较多，对初学者来说，将每个组件独立运行起来可能比较困难。

本小册配套搭建了一个在线演示环境，读者可以直接访问概览整个系统：

- [Nginx - http://learn-elk.roadmapedu.com:8000](http://learn-elk.roadmapedu.com:8000)
- [Kibana - http://learn-elk.roadmapedu.com:5601](http://learn-elk.roadmapedu.com:5601)
- [Kafka - http://learn-elk.roadmapedu.com:9090](http://learn-elk.roadmapedu.com:9090)

对以上 Nginx 网站的访问日志，将实时索引到 Elasticsearch，并可以通过 Kibana 查看。