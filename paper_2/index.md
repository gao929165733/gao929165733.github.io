# [21_paper_2] Synchronous Dual Network with Cross-Type Attention for Joint Entity and Relation Extraction


[paper](https://aclanthology.org/2021.emnlp-main.219.pdf)

[code](https://github.com/HuiStudent/Cross-type_SDN)

## 1. 主要贡献和结论

- 提出SDN，采用两个同构的双向注意力LSTM对实体类型增强表征和关系类型增强表征进行编码
- 通过SDN使用交叉注意力机制显示建模实体类型和关系类型之间的相互依赖的关系
- 多任务学习策略

## 2. 问题

- 没有充分利用实体类型和关系类型之间的相互依赖性

## 3. 具体内容

### 3.1 model

![](/pic/2/1.png)

模型分为三个架构

- TA-LSTM
- Synchronous Dual Network with Cross-Type Attention
  - 在TA-LSTM上构建同步的双网络
  - 使用交叉注意力机制实现实体类型和关系类型之间的交互

实体类型学习主要是通过训练实体类型单元从一组标记的训练的数据中提取实体类型知识

#### 3.1.1 公式解析

- **Type-Attention LSTM**

  - <u><font color='red'>LSTM</font></u>，**输入**：$w_{t}$， **输出**：${h}_{t}^{c}$

    ![](/pic/2/2.png)

  - <u><font color='red'>Type-Attention Mechanism</font></u>，**输入**：$x_{t}, h_{t-1}$， **中间输出**：${K}^{(t)}=\left[{k}_{1}^{(t)}, \ldots, {k}_{m}^{(t)}\right], {V}^{(t)}=\left[{v}_{1}^{(t)}, \ldots, {v}_{m}^{(t)}\right]$

![](/pic/2/3.png)

​			**最后输出**：$h_{t}$

![](/pic/2/4.png)

![](/pic/2/5.png)

- **Synchronous Dual Network with Cross-Type Attention**

  ![](/pic/2/27.png)

  - **Entity type learning**--根据实体类型数量定义实体类型单元，将每个实体类型都看作一个标签，如PER、LOC等。实体类型学习主要是通过训练实体类型单元从一组标记的训练数据中提取实体类型知识

    - 根据公式（1）和（2）可以得到上下文向量表示：${h}_{t}^{c}$，与实体类型相关的键值对表示：$\bar{K}^{(t)}, \bar{V}^{(t)}$，其中$p$表示实体类型单元的数量

      ![](/pic/2/6.png)

    - 根据公式4可以计算出实体表示向量：${H}^{(e)}$

      ![](/pic/2/7.png)

    - <u><font color='red'>entity type prediction</font></u>

      本质上就是一个序列标注的命名实体识别任务，但是不需要识别实体的边界，只需要识别出每个token所属的实体类型。

      ![](/pic/2/8.png)

      输入文本：${s}=\left[w_{1}, \ldots, w_{n}\right]$ 

      输出三元组：![](/pic/2/26.png)

      ![](/pic/2/9.png)

      ![](/pic/2/10.png)

  - **Relation type learning**

    ![](/pic/2/11.png)

    计算方法和实体相似，但是有2M+1个序列，M为关系数量。与实体类型预测不同的是，关系类型预测是多标签分类任务，因为一个实体可能会存在多个三元组中（三元组重叠）。

    ![](/pic/2/12.png)

    ![](/pic/2/13.png)

- **Cross-Type Attention Mechanism**

  - 关系

    输入：实体表征向量：$\mathbf{h}_{t}^{e}$，关系类型相关键值对： $\hat{K}^{(t)}, \hat{V}^{(t)}$

    输出：实体关系相关向量：$\mathbf{c}_{t}^{e}$，根据公式（3）计算

  - 实体

    输入：关系表征向量：$\mathbf{h}_{t}^{r}$，关系类型相关键值对：$\bar{K}^{(t)}, \bar{V}^{(t)}$

    输出：实体关系相关向量：$\mathbf{c}_{t}^{r}$，根据公式（3）计算

  - 综合，通过引入实体类型增强表示和关系类型增强表示，得到了新的实体类型增强表示和新的关系类型增强表示

    ![](/pic/2/25.png)

- **Joint Entity and Relation Extraction**

  将实体类型增强表示和关系类型增强表示结合起来构成新的向量表示![](/pic/2/28.png)，用于NER和RE任务。

  ![](/pic/2/14.png)

  - **Named Entity Recognition**

    BIESO，序列标注

    ![](/pic/2/15.png)

    ![](/pic/2/16.png)

  - **Relation Extraction**

    多标签分类，$y_{i, j}^{r^{\prime}}$表示单词对$\left(w_{i}, w_{j}\right)$属于关系$r^{\prime} \in \mathcal{R}$的概率

    ![](/pic/2/17.png)

    - 损失函数

      ![](/pic/2/18.png)

- **Training**

  ![](/pic/2/19.png)

- **Inference**

  平均预测法，通过NER得到实体类别，给定头实体和尾实体：

  ![](/pic/2/29.png)
  
  计算他们属于第$r$种关系类型的概率。其中|$\mathbf{e}_{i}$|, |$\mathbf{e}_{j}$|表示实体的长度，
  
  ![](/pic/2/30.png)
  
  ![](/pic/2/20.png)

### 3.2 实验

#### 3.2.1 数据集

![](/pic/2/21.png)

#### 3.2.2 结果

![](/pic/2/22.png)

#### 3.2.3 消融实验

![](/pic/2/23.png)

#### 3.2.4 识别示例

![](/pic/2/24.png)

## 参考

<br>
<font size="6">加油吧，骚年！</font>


