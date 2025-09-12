---
created: 2025-07-14 20:01
modified: 2025-07-14 20:01
aliases:
  - torch
  - pytorch
  - huoju
tags:
---
# 安装pytorch
1. python&torch的版本：pytorch与python版本之间有依赖关系，目前最新版本的pytorch2.7需要python版本>=3.9,其他版本参考[pytorch与python版本对应关系](https://github.com/pytorch/vision#installation)进行选择
2. 到[官网](https://pytorch.org/get-started/locally/)找到对应命令下载：
	- 如果通过conda install安装torch则不需要安装cuda，conda会自动安装必要的库文件，安装后报错解决：`from torch._C import *  # noqa: F403`
		```bash
		pip install "mkl<=2024.0" "intel-openmp<=2024.0"
		```
	- 如果通过pip安装，需要环境中有cuda环境，然后选择满足cuda版本的torch

## 验证

- 验证
```python
import torch
print(torch.__version__)#检查pytorch版本
print(torch.version.cuda)#检查pytorch支持的cudatoolkit版本
print(torch.backends.cudnn.version())#检查cudnn
print(torch.cuda.is_available())#检查GPU是否可用
```


# torch
- 参考：
	- [torch官方文档](https://pytorch.ac.cn/docs/stable/torch.html#creation-ops)
	- [PyTorch_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1qf4y1b7xt?spm_id_from=333.788.videopod.episodes&vd_source=ca17e8e44a1bddd49f39d4bc7c4ad336)
PyTorch 是一个基于 Python 的开源机器学习框架，由Meta开发，它以动态计算图为核心特性，支持灵活的模型构建、自动求导和 GPU 加速

## 计算图
- 计算图是一个用于描述计算有向无环图
	- 节点：代表参与计算的张量
		- 输入节点： 待优化的张量如模型参数，learnable-token
		- 中间节点：计算过程中产生的所有中间结果的张量
		- 输出节点：最终的计算结果，如损失函数的标量结果
	- 有向边：表示张量间的计算关系，如加减乘除，激活函数等
	- 自动求导：当调用 `backward()` 时，PyTorch 会沿着计算图从输出节点（通常是损失函数）反向遍历到输入节点（如模型参数），根据链式法则自动计算每个参数的梯度`grad`

- 静态图：在运行任何实际计算之前，必须完整地、一次性地定义好整个计算图的结构。这个图的结构（包括所有操作、分支、循环）在定义后就是固定的，无法根据中间结果的张量来动态改变后续的计算路径
- 动态图：在tensor运算过程中动态创建的，允许网络在运行时根据中间结果的张量，动态地创建分支和循环结构，这使得不定长序列的处理和MoE(根据权重决定激活的子网络)实现成为可能


## tensor
torch.tensor是pytorch中基本的数据类型，类似np.ndarray。每个 tensor 都有一个 `requires_grad` 属性，当设为 `True` 时，PyTorch 会追踪该 tensor 参与的所有运算，反向传播时根据计算图计算梯度：
```python
import torch
# 创建一个需要计算梯度的 tensor
x = torch.tensor(2.0, requires_grad=True)
# 定义一个简单的函数 y = x^2
y = x**2
# 计算 y 关于 x 的梯度
y.backward()
# 打印梯度值
print(f"x 的梯度值为: {x.grad}")  # 输出: 4.0
```
#### 创建
```python
torch.tensor(
	[1.0, 2.0], 
	dtype=torch.float64,#指定张量的数据类型
	device="cuda",#指定张量存储的设备
	requires_grad = True,#是否需要梯度
)
```
- 不同的创建方式：
```python
#从数字创建
tensor_single=torch.tensor(3.1)
#从list创建
tensor_a=torch.tensor([[1,2],[3,4]])
#从数组创建
tensor_b=torch.tensor(np.array([[5,6],[7,8]]))
#全1填充
tensor_ones=torch.ones(2,3)
#全0填充
tensor_zeros=torch.zeros(2,3)
#单位矩阵
tensor_diag=torch.eye(3,4)
#full填充
tensor_full=torch.full((2,3),7)
#创建但不初始化
tensor_empty=tensor.empty(2,3)
#标准正太分布
tensor_randn=np.randn(2,3,4)
#0-1均匀分布
tensor_rand=np.rand(2,3,4)
#创建随机整数数组，范围(0~99)
tensor_randint=torch.randint(0,100,(2,3,4))
#自增数列，0值9步长为2
tensor_arrange=torch.arange(0,10,2)
#等差数列，0值10四个数
tensor_linspace=torch.linspace(0,10,2)
#指定类型的创建
torch.FloatTensor([1,2])
torch.LongTensor([1,2])
```
#### 属性和方法
- 统计：
```python
#最大max、最小min、和sum、积prod
tensor.max()
#均值，dim维度从最外层计数,对shape为[1, 64, 28, 28]的tensor，这表示求不同通道的均值，keepdim是否保留dim
tensor.mean(a,dim(0,2,3),keepdim=True)
#最大/小值下标argmax、argmin
tensor.argmax(dim=0)
#1范数:绝对值之和
tensor.norm(1)
#2范数:平方和的平方根
tensor.norm(2)
```
- 形状操作：
```python
#插入维度,在第0维度插入维度，长度为1
tensor_unsqueeze=tensor_a.unsqueeze(0)
#删除维度,只能删除长度为1的维度,不指定维度时,删除所有长度为1的维度
tensor_squeeze=tensor_a.squeeze(-1)
#在第一个维度复制2次，在第二个维度复制3次
tensor_copy=tensor_a.repeat(2,3)
#两两交换维度
tensor_transpose=torch_a.transpose(0,1)
#交换维度的顺序
tensor_permute=torch_a.permute(2,1,0)
#转置,只对二维tensor有效
tensor_t=tensor_a.t()
#拆分tensor
_1,_2=tensor_x.split(2,dim=0)#步长为2
_1,_2,_3=tensor_x.split([1,2,1],dim=0)#拆分后长度分别为1，2，1
#拼接两个tensor,不增加新维度
tensor_c=torch.cat([tensor_a,tensor_b],dim=0)
#增加新维度，将多个张量堆叠成一个更高维的张量，所有tensor的形状必须完全相同
tensor_c=torch.stack([tensor_a,tensor_b],dim=0)
#将输入张量的指定维度及其之后的所有维度合并为一个维度
torch.flatten(input, start_dim=0, end_dim=-1)
#将tenser在某一维度copy多份
tensor_b = tensor_a.expand(4, -1, -1) 
```
- 数学运算：
```python
#矩阵乘法
torch.matmul(A, B)
#逆,只对二维tensor有效
tensor_inverse=tensor_a.inverse()
#限幅
tensor_clamped=torch.clamp(tensor, min=2, max=4)
#向下取整
tensor_floor=tensor_a.floor()
#向上取整
tensor_floor=tensor_a.ceil()
#四舍五入
tensor_floor=tensor_a.round()
#提取上三角,diagonal=1 排除对角线
torch.triu(,diagonal=1)
```
- else:
```python
#to方法可以指定数据类型和设备，并返回一个新的tensor
ts_b=tensor_a.to(device=torch.device('cuda:0'),dtype=torch.float16)
#从tensor中提取一个标量
tensor_a[0][0].item()
tensor.grad
```
#### 数据类型
| 类型        | 数据类型                             | 描述                                                          |
| --------- | -------------------------------- | ----------------------------------------------------------- |
| **浮点型**   | `torch.float64` / `torch.double` | 64 位浮点数，提供更高精度，但计算速度和内存占用更高。                                |
|           | `torch.float32` / `torch.float`  | 32 位浮点数，常用于深度学习计算。                                          |
|           | `torch.float16` / `torch.half`   | 16 位浮点数，用于混合精度训练，节省内存并加速计算。                                 |
|           | `torch.bfloat16`                 | 与float16相比内存占用相同，运算精度低，但动态范围与float32相同，能表示更大的数值，float16容易溢出 |
| **整型**    | `torch.int8`                     | 8 位整型，用于量化模型。                                               |
|           | `torch.int16` / `torch.short`    | 16 位整型。                                                     |
|           | `torch.int32` / `torch.int`      | 32 位整型。                                                     |
|           | `torch.int64` / `torch.long`     | 64 位整型。                                                     |
| **无符号整型** | `torch.uint8`                    | 8 位无符号整型，常用于图像数据。                                           |
| **布尔型**   | `torch.bool`                     | 布尔型，用于存储布尔值。                                                |
| **复数型**   | `torch.complex64`                | 64 位复数型，实部和虚部各为 32 位浮点数。                                    |
|           | `torch.complex128`               | 128 位复数型，实部和虚部各为 64 位浮点数。                                   |
## 数据
### Dataset
`torch.utils.data.Dataset` 是一个抽象类，你需要继承它并实现两个方法：
- `__len__()`：返回数据集大小
- `__getitem__(idx)`：根据索引返回一个样本
### DataLoader
```python
import torchvision.transforms as transforms
```
from torch.utils.data import DataLoader

train_loader = DataLoader(train_dataset, batch_size=32, shuffle=True, num_workers=4)
## 模型
定义模型时，主要使用`torch.nn`这个类来完成网络的搭建，nn 是 Neural Networks的缩写，直接表明了该模块的用途 —— 提供构建和训练神经网络所需的所有工具
### Module
nn.Module是 PyTorch 中所有神经网络模块的基类通过继承 nn.Module，你可以创建自定义的神经网络层或完整的模型，并利用 PyTorch 提供的自动求导、参数管理和设备管理等功能
##### 属性和方法
- `__init__()`：初始化模型结构，定义各层参数
- `forward()`：定义前向传播逻辑
- `eval()`：停用Dropout 层，Batch Normalization使用全局统计量
- `train()`：行为与eval对应
- `to()`：将模型参数移动到指定设备，如：
	```python
	device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
	model.to(device)
	```
- `parameters()`：返回模型的所有可训练参数（用于优化器）
- `state_dict()`：存储模型中所有可学习参数的名称和值
- `load_state_dict()`：从状态字典加载模型参数
- `add_module`：添加模块
##### else
- 保存参数：
```python
torch.save(model.state_dict(), 'model_weights.pth')  # 只保存参数
torch.save(model, 'full_model.pth')# 保存完整模型（结构+参数）
```
- 加载参数：
```python
# 加载参数模型
loaded_model = MyModel()
loaded_model.load_state_dict(torch.load('model_weights.pth'))
loaded_model.eval()  # 设置为评估模式（重要！）

# 加载完整模型
loaded_model = torch.load('full_model.pth')
```
### Parameter
https://zhuanlan.zhihu.com/p/510490016
`nn.Parameter()`用于注册一个tensor为可训练参数
### 基础层
- `nn.Linear`：全连接层，参数为输入和输出维度
```python
layer = nn.Linear(10, 20)  # 输入10维，输出20维
```
- `nn.RNN`：循环神经网络
```python
layer = nn.RNN(input_size,hidden_size,num_layers)
```
- `nn.LSTM`：长短期记忆网络，处理序列数据
```python
# 输入维度10，隐藏层维度20，1层LSTM
layer = nn.LSTM(10, 20, 1)
```
- `nn.Conv2d`：二维卷积层，用于图像等数据
```python
layer = nn.Conv2d(in_channels, out_channels, kernel_size)
```
- `nn.ConvTranspose2d`：转置卷积层，扩大特征图尺寸
```python
layer = nn.ConvTranspose2d(in_channels, out_channels, kernel_size)
```
- `nn.MaxPool2d`：二维最大池化层，用于降维
```python
layer = nn.MaxPool2d(kernel_size=2, stride=2)
```
- `nn.AdaptiveAvgPool2d`：自适应平均池化层，指定输出特征图的大小，自动计算池化窗口的大小和步长
```python
layer = nn.AdaptiveAvgPool2d(output_size=(5, 7))
```
- `nn.BatchNorm2d/1d`：二维批量归一化，加速训练
```python
layer = nn.BatchNorm2d(16)  # 对应卷积层的输出通道数
```
- `nn.Dropout`：防止过拟合，训练时随机忽略部分神经元
```python
layer = nn.Dropout(p=0.5)  # 丢弃概率为0.5
```
- `nn.Embedding`：词嵌入矩阵
```python
embedding = nn.Embedding(num_embeddings,embedding_dim)#词典大小和嵌入维度
```
### 激活函数
- `nn.ReLU`：修正线性单元，最常用的激活函数
	```python
	activation = nn.ReLU(inplace=True)
	```
	- `inplace`：是否覆盖输入张量
- `nn.Sigmoid`：S型函数，输出范围(0,1)，用于二分类
```python
activation = nn.Sigmoid()
```
- `nn.Tanh`：双曲正切函数，输出范围(-1,1)
```python
activation = nn.Tanh()
```
- `nn.Softmax`：多类别概率分布，用于分类问题
```python
# 对维度1进行softmax
activation = nn.Softmax(dim=1)
```
### 损失函数
- `nn.MSELoss`：均方误差，用于回归问题。
```python
criterion = nn.MSELoss()
```
- `nn.CrossEntropyLoss`：交叉熵，用于多分类问题。
```python
criterion = nn.CrossEntropyLoss()
```
- `nn.BCELoss`：二元交叉熵，用于二分类问题。
```python
criterion = nn.BCELoss()
```
### 容器类
- `nn.Sequential`：顺序容器，快速搭建网络。
```python
model = nn.Sequential(
	nn.Linear(10, 20),
	nn.ReLU(),
	nn.Linear(20, 2)
)
```
### 例子
```python
# 打印模型所有参数
print(model)
# 打印参数量
total_params = sum(p.numel() for p in model.parameters())
print(f"模型总参数量: {total_params/1e9:.2f} B")
```

## 训练
### Optimizer
- `torch.optim.SGD`：随机梯度下降。
```python
optimizer = torch.optim.SGD(model.parameters(), lr=0.01, momentum=0.9)
```
- `torch.optim.Adam`：自适应学习率优化器。
```python
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)
  ```

# torchvision
torchvision.models提供了一些经典网络架构的预训练模型