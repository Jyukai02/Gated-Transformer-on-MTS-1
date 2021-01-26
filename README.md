# Gated-Transformer-on-MTS
基于Pytorch，使用改良的Transformer模型应用于多维时间序列的分类任务上

## 实验环境
环境|描述|
---|---------|
语言|Python3.7|
框架|Pytorch1.6|
IDE|Pycharm和Colab|

## 数据集
多元时间序列数据集, 文件为.mat格式，训练集与测试集在一个文件中，且预先定义为了测试集数据，测试集标签，训练集数据与训练集标签。 <br>
数据集下载使用百度云盘，连接如下：<br>
  链接：https://pan.baidu.com/s/1u2HN6tfygcQvzuEK5XBa2A <br> 
  提取码：dxq6 <br>
  复制这段内容后打开百度网盘手机App，操作更方便哦<br>

---

数据集维度描述
DataSet|Number of Classes|Size of training Set|Size of testing Set|Max Time series Length|Channel|
-------|-----------------|--------------------|-------------------|----------------------|-------|
ArabicDigits|10|6600|2200|93|13|
AUSLAN|95|1140|1425|136|22|
CharacterTrajectories|20|300|2558|205|3|
CMUsubject16|2|29|29|580|62|
ECG|2|100|100|152|2|
JapaneseVowels|9|270|370|29|12|
Libras|15|180|180|45|2|
UWave|8|200|4278|315|3|
KickvsPunch|2|16|10|841|62|
NetFlow|2|803|534|997|4|
PEMS|7|267|173|144|963|
Wafer|2|298|896|198|6|
WalkvsRun|2|28|16|1918|62|
 
## 数据预处理
详细数据集处理过程参看 dataset_process.py文件。<br>
- 将数据集处理成为torch.utils.data.Dataset对象，对数据集进行处理，成员变量定义的有训练集数据，训练集标签，测试集数据，测试集标签等。<br>
- 数据集中不同样本的Time series Length不同，处理时使用所有样本中（测试集与训练集中）**最长**的时间步作为Time series Length，使用**0**进行填充。<br>
- 数据集处理过程中保存未添加Padding的训练集数据与测试集数据，还有测试集中最长时间步的样本列表以供探索模型使用。<br>
- NetFlow数据集中标签为**1和13**，在使用此数据集时要对返回的标签值进行处理。<br>

## 超参描述
超参|描述|
----|---|
d_model|模型省略了NLP中对词的编码，仅使用一个线性层映射成d_model维的稠密向量，此外，保证在每个模块衔接的地方的维度相同|
d_hidden|Position-wise FeedForword 中隐藏层的维度| 
d_input|时间序列长度，其实是一个数据集中最长时间步的维度 **固定**的，直接由数据集预处理决定|
d_channel|多元时间序列的时间通道数，即是几维的时间序列 **固定**的，直接由数据集预处理决定|
d_output|分类类别数 **固定**的，直接由数据集预处理决定|
q,v|Multi-Head Attention中线性层映射维度|
h|Multi-Head Attention中头的数量|
N|Encoder栈中Encoder的数量|
dropout|随机失活|
EPOCH|训练迭代次数
BATCH_SIZE|mini-batch size|
LR|学习率|
optimizer_name|优化器选择 建议Adagrad和Adam|

## 模型描述
- 仅使用Encoder：由于是分类任务，模型删去传统Transformer中的decoder，**仅使用Encoder**进行分类
- Two Tower：在不同的Step之间或者不同Channel之间，显然存在着诸多联系，传统Transformer使用Attentino机制用来关注不同的step或channel之间的相关程度，但仅选择一个进行计算。不同于CNN模型处理时间序列，它可以使用二维卷积核同时关注step-wise和channel-wise，在这里我们使**双塔**模型，即同时计算step-wise Attention和channel-wise Attention。
- Gate机制：对于不同的数据集，不同的Attention机制有好有坏，对于双塔的特征提取的结果，简单的方法，是对两个塔的输出尽心简单的拼接，不过在这里，我们使用模型学习两个权重值，为每个塔的输出进行权重的分配，公式如下。<br>
                                                  h = W · Concat(C, S) + b <br>
                                                  g1, g2 = Sof tmax(h) <br>
                                                  y = Concat(C · g1, S · g2)<br>
                                                  
