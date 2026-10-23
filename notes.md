# 第一章 什么是 NLP

## NLP 起源

* 人们发现 NLP 的应用价值
* 艾伦·图灵提出图灵测试
* 符号主义 vs 统计主义 just like 几何方法 vs 代数方法

## NLP 任务

* 中文分词：是标注词性等后续工作的基础
* 子词切分：借助基础词汇的组合，分析未知的新的复杂词汇
    * BPE：byte pair encoding
* 文本分类：开始大量应用神经网络

## 词表示

* One-hot：人工排布，过于稀疏，也不能表示位置信息
* N-gram：上下文大小受限，N 过大后训练很难收敛
* word embedding：生成密集向量，可以自动提取词汇相对关系
* ELMo：双向LSTM，提取的信息有限，而且训练效率低

# 第二章 Transformer 架构

## 网络结构

* MLP：多层全连接，权重直接等于信息，参数量大
* CNN：通过卷积核这种小参数量矩阵，来提取和压缩局部信息
* RNN：通过自循环增加了时序演进机制，所以无法并行计算
* LSTM：只是在 RNN 基础上解决了信息衰减问题

## 注意力机制

实质上是计算多个 K 对应的 V 的加权合并的结果，权重比例是 Q 和全量 K 分别的相似度。

> $$ attention (Q,K,V) = softmax (\frac{QK^T}{\sqrt{d_k}})V $$

* $Q$：全量 query
* $K^T$：要查询的 key 矩阵，因为计算点积相似度而转置
* $d_k$：排除 batch size 和 attention head 后的向量的长度，一般是 Q.shape[1]
* $softmax$：因为目的是按比例组合不同的 value，所以需要归一化
* $V$：即是要查询的 value 矩阵