# Paper 分享会 week 1

## GCN及相关        by 陆义杰

+ graph embedding方法

  主要讲了3种基于Word2Vec的方法

  >2013年，Google团队发表了word2vec工具。word2vec工具主要包含两个模型：跳字模型（skip-gram）和连续词袋模型（continuous bag of words，简称CBOW），以及两种高效训练的方法：负采样（negative sampling）和层序softmax（hierarchical softmax）。值得一提的是，word2vec词向量可以较好地表达不同词之间的相似和类比关系。

  下面介绍word2Vec中的两种模型： skip-gram和CBOW

  skip-gram是根据当前词预测上下文的模型，输入one-hot of a word，输出和输入相同dim的一组probability，代表每个词和这个词的相关性。神经网络类似autoencoder的模型。

  ![Alt text](image1.png)

  中间的hidden Layer就是词向量化的表示。

  在skip-gram中因为输入的vec维数比较大，导致中间的权重矩阵过于庞大。为了简化训练，会采用负采样（negative sampling）的方法。比如对于句子**the quick brown fox jumps over lazy dog**，当选取（input，output）=（fox，jumps）作为训练集时，我们希望输出层的概率为仅仅jumps位置为1，其余位置为0.概率为0位置对应的词我们叫做negative word。我们希望在这次更新权重中，仅仅更新jumps的权重和几个negative words 的权重，而不是所有权重。这种方法就叫做负采样。

  - 在论文中，作者指出指出对于小规模数据集，选择5-20个negative words会比较好，对于大规模数据集可以仅选择2-5个negative words。

  同理对于graph的向量化表示，我们首先需要将图中的node的context，就是每个node的neighborhood，下面三种方法给出了三种不同的定义方法。
  
  1. DeepWalk随机游走
  
     ![Alt text](paper-notes/paper分享会/image2.png)
  
     对于一个node，采用随机的策略从neighbor中选取下一个node，从而形成节点序列。然后用skipgram学习节点的向量表示。
  
     ![1567065501707](image3.png)
  
  2. Node2Vec
  
     和deepwalk相似，只不过在选取策略上引入p，q两个参数控制是DFS还是BFS。
  
     ![1567065675197](image4.png)
  
     dtx表示v的上一个节点t和v下一个节点x之间的距离，如果dtx=0，表示v向回走概率为1/p(广度优先)，dtx=2表示x是v的邻居但不是t的邻居，概率为1/q（深度优先）。如果x既是t的邻居又是v的邻居，概率为1.
  
  3. LINE
  
     line方法不借助于skip-gram，他直接定义图中两点的联合概率。
  
     有两种相似度定义：
  
     ![1567066110532](image5.png)
  
     first-order只能用于无向图，而second-order可以用于无向图和有向图。



