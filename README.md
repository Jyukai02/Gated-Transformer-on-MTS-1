# Gated-Transformer-on-MTS
使用改良的Transformer模型应用于多维时间序列的分类任务上

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
### 数据集维度描述
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
将数据集处理成为torch.utils.data.Dataset对象，对数据集进行处理，成员变量定义的有训练集数据，训练集标签，测试集数据，测试集标签等。<br>
数据集中不同样本的Time series Length不同，处理时使用所有样本中（测试集与训练集中）最长的时间步作为Time series Length，使用0进行填充。<br>

