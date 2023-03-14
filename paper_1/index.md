# [21_paper_1] TPLinker: Single-stage Joint Extraction of Entities and Relations Through Token Pair Linking


[paper](https://arxiv.org/pdf/2010.13415.pdf)

[code](https://link.zhihu.com/?target=https%3A//github.com/131250208/TPlinker-joint-extraction)

## 1. 主要贡献和结论

- TPLinker将抽取标注框架统一为**字符对链接问题**，即**Token Pair Linking problem**
- 解决三元组**重叠问题**和**暴露偏差**（指在训练阶段是gold实体输入进行关系预测，而在推断阶段是上一步的预测实体输入进行关系判断；导致训练和推断存在不一致）问题
- TPLinker是**单阶段解码**，训练和推断阶段抽取三元组不存在差异

## 2. 相关工作

![](/pic/1/21-paper-1.jpg)

实体关系的joint抽取模型可分为2大类

### **第1类：多任务学习**（共享参数的联合抽取模型）

多任务学习机制中，实体和关系共享同一个网络编码，但本质上仍然是采取pipeline的解码方式（故仍然存在误差传播问题）。近年来的大部分joint都采取这种共享参数的模式，集中在魔改各种**Tag框架**和**解码方式**。

- **多头选择**[[1\]](https://www.sciencedirect.com/science/article/abs/pii/S095741741830455X?via%3Dihub)：构建N×N×C 的关系分类器对每一个实体pair进行关系预测（N为序列长度，C为关系类别总数），输入的实体pair其实是每一个抽取实体的最后一个token。后续基于多头选择机制，也有paper引入预训练语言模型和bilinear分类。
- **层叠式指针标注**[[2\]](https://arxiv.org/pdf/1909.03227.pdf)：将关系看作是SPO（Subject-Prediction-Object）抽取，先抽取主体Subject，然后对主体感知编码，最后通过层叠式的指针网络抽取关系及其对应的Object。
- **Span-level NER**[[3\]](https://arxiv.org/pdf/1909.07755.pdf)：通过片段排列抽取实体，然后提取实体对进行关系分类。

### **第2类：结构化预测**（联合解码的联合抽取模型）

结构化预测则是一个全局优化问题，在推断的时候能够联合解码实体和关系（而不是像多任务学习那样，先抽取实体、再进行关系分类），只需要在一个阶段解码，解决暴露偏差。结构化预测的joint模型也有较多，比如**统一的序列标注框架**[[4\]](https://zhuanlan.zhihu.com/p/274938894#ref_4)、**多轮QA+强化学习**[[5\]](https://zhuanlan.zhihu.com/p/274938894#ref_5)等，不过有的联合解码方式通常很复杂。

## 3. 具体内容

### 3.1 TPLinker的标注框架

![](/pic/1/21-paper-2.png)

TPLinker通过链接(linking)3种类型的Span矩阵来实现，为方便起见，论文作者将3种标注方式画到一个图里了，如上图所示（记关系类别总数为R个）：

- **紫色标注**：EH to ET，表示实体的头尾关系，是1个**N*N**矩阵；如两个实体：New York City:M(New, City) =1; De Blasio:M(De, Blasio) =1。
- **红色标注**：SH to OH，表示subject和object的头部token间的关系，是R个**N*N**矩阵；如三元组(New York City, mayor,De Blasio):M(New, De)=1。
- **蓝色标注**：ST to OT，表示subject和object的尾部token间的关系，是R个**N*N**矩阵；如三元组(New York City, mayor,De Blasio):M(City, Blasio)=1。

因此，可以得到TPLinker共有**2R+1**个矩阵。值得注意的是：为防止稀疏计算，下三角矩阵不参与计算；虽然实体标注不会存在于下三角矩阵种，但关系标注是会存在的。为此，论文采取转换操作是：如果关系存在于下三角，则将其转置到上三角，并有“标记1”变为“标记2”。

### 3.1 TPLinker的解码过程

![](/pic/1/21-paper-3.png)

上图给出了一个完整的标注和编码示意图，标记有“0、1、2”三种。编码部分实际上是将原始的Span矩阵会展开为一个**N*(N+1)/2**序列进行编码(使用映射来记住原始矩阵中的位置)，也就是将token pair的每一个token编码拼接在一起。TPLinker的解码过程为：

- 解码EH-to-ET可以得到句子中所有的实体，用实体头token idx作为key，实体作为value，存入字典D中，记为D={entity token idx：entity}；
- 对每种关系r，解码ST-to-OT得到token对存入集合E中，解码SH-to-OH得到token对并在D中关联其token idx的实体value；
- 对上一步中得到的SH-to-OH token对的所有实体value对，在集合E中依次查询是否其尾token对在E中，进而可以得到三元组信息。

结合上图的具体case,我们具体描述一下解码过程：

- 解码EH-to-ET中得到3个实体：{New York,New York City,De Blasio}; 字典D为：`{New:(New York,New York City),De:(De Blasio)}`

以关系“**mayor**”为例

- 解码ST-to-OT得到集合E：{(City,Blasio)};解码SH-to-OH得到{(New,De)}，其在字典D中可关联的subject实体集合为{New York,New York City};object集合{De Blasio};
- 遍历上述subject集合和object集合，并在集合E中查询尾token，发现只有一个实体三元组{New York City,mayor,De Blasio}

以关系“**born in**”为例,

- 解码ST-to-OT得到集合E：{(Blasio,York),(Blasio,City)};解码SH-to-OH得到{(De,New)}，其在字典D中可关联的subject实体集合为{De Blasio};object集合为{New York,New York City};

- 遍历上述subject集合和object集合，并在集合E中查询尾token，可得到2个实体三元组：{De Blasio,born in,New York}和{De Blasio,born in,New York City}

由于关系**live in**与**born in**一样，所以我们最终可得到5个三元组：

- (New York City, mayor, De Blasio), (De Blasio, born in, New York), (De Blasio, born in, New York City), (De Blasio, live in, New York), (De Blasio, live in, New York City)

### 3.2 实验结果

![](/pic/1/21-paper-4.png)

如上图所示，TPLinker也超越了之前的一众SOTA。此外，TPLinker相较于之前的SOTA——CasRel，推断速度也快3.6倍。

## span-level NER

- **定义：**
  - span-level NER 是一种应对嵌套实体任务的方法，基于片段排列的方式，提取所有可能的片段排列，当某个片段排列是我们需要的正确排列作为正确实体，其他的作为负面实体，通过SoftMax对每一个Span进行实体类型判断，将原来的序列标注问题转化成分类问题。{{< figure src="/pic/span_ner.jpg" title="基于片段排列的实体识别" >}}
- **思路：**
  - 整个算法为一个分类模型，对于含有n个token的文本，理论上存在n(n+1)/2种片段排列；如果文本过长，会产生大量的负样本，在实际中需要限制span长度并合理削减负样本。
- **优化：**
  - 使用分词代替按字分割句子（中文），从而减少样本数量，并且可以增加样本的对抗性和合理性
  - 随机在负样本中选取一定量的样本
  - 拓展正面实体的边界获取对抗性较高的负样本
  - 限制实体的最大长度

## 参考

[1]Joint entity recognition and relation extraction as a multi-head selection problem

[2]A Novel Cascade Binary Tagging Framework for Relational Triple Extraction

[3]Span-based Joint Entity and Relation Extraction with Transformer Pre-training

[4]Joint extraction of entities and relations based on a novel tagging scheme

[5]Entity-Relation Extraction as Multi-Turn Question Answering

[杰神知乎](https://zhuanlan.zhihu.com/p/346897151)

<br>
<font size="6">加油吧，骚年！</font>


