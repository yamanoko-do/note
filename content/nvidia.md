# 算力

- [算力榜单](https://www.topcpu.net/cpu-r5)

- 常见数量级：

| 单位符号    | 含义           | 科学计数法             |
| ------- | ------------ | ----------------- |
| K(Kilo) | 千            | $1\times 10^3$    |
| M(Mega) | 百万           | $1\times 10^6$    |
| G(Giga) | 十亿(billion)  | $1\times 10^9$    |
| T(Tera) | 万亿(trillion) | $1\times 10^{12}$ |
| P(Peta) | 千万亿          | $1\times 10^{15}$ |
| E(Exa)  | 百亿亿          | $1\times 10^{18}$ |

- FLOPs(floating point of operations)：是浮点运算次数，理解为计算量，
- FLOPS(floating point of per second)：指每秒浮点运算次数。可以理解为计算速度

| 计算卡           | 架构     | 服务器FP32算力（TFLOPS） | 显卡                | 显卡架构         | 消费级显卡FP32算力（TFLOPS） |
| ------------- | ------ | ----------------- | ----------------- | ------------ | ------------------- |
| P100（16GB）    | Pascal | 9.3               | GTX 1080 Ti（11GB） | Pascal       | 11.3                |
| V100（16-32GB） | Volta  | 15.7              | Titan V（12GB）     | Volta        | 14.9                |
| A100（40-80GB） | Ampere | 19.49             | RTX 3090（24GB）    | Ampere       | 40                  |
| H100（56GB）    | Hopper | 66.91             | RTX 4090（24GB）    | Ada Lovelace | 82.58               |
常见显卡：

| 显卡型号              | FP32算力（TFLOPS） | FP16算力(TFLOPS) |
| ----------------- | -------------- | -------------- |
| RTX 2060（6G）      | 4.6            |                |
| RTX 3060（12G）     | 12.74          |                |
| RTX 2080Ti（11G）   | 13.45          |                |
| RTX 4060 Ti       | 22.06          |                |
| RTX 3090 Ti       | 40             |                |
| RTX 4070 Ti Super | 44.1           |                |
| RTX 4080 Super    | 52.22          |                |
| RTX 4090          | 82.58          |                |

time spy
- 3060bilibili：8613
- 4060ti花嫁：13145
- 4060ti星耀：13455
- 4070tis花嫁：23247
## CUDA
[参考](https://blog.csdn.net/qq_41094058/article/details/116207333?spm=1001.2014.3001.5501)
- 冷知识：同一个驱动可以支持不同的显卡
**说明**：
- 使用显卡的CUDA需要安装以下三个东西：
	1. NVIDIA Driver：显卡驱动，含CUDA Driver，nvidia-smi有输出即可
	2. [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit-archive)：基础库文件、nvcc编译器，含NVIDIA driver（可选，一般提前独立安装好）
	3. cuDNN(可选)：用于深度学习计算的库

- 其中版本关系需要满足：
	1. CUDA Driver version >= CUDA Toolkit version
	2.  [CUDA Toolkit version及PyTorch对应关系](https://blog.csdn.net/weixin_44842318/article/details/127492491)
	3.  [cuDNN与CUDA Toolkit version对应关系](https://developer.nvidia.com/rdp/cudnn-archive)

### 安装CUDA Toolkit
[下载CUDA Toolkit](https://developer.nvidia.com/cuda-downloads)
- 配置环境变量
	```bashrc
	export PATH=$PATH:/usr/local/cuda-11.8/bin
	export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.8/lib64
	export CUDA_HOME=$CUDA_HOME:/usr/local/cuda-11.8
	```
7. 在系统环境(不推荐)与容器中使用cuda参考：
	- 系统环境：[Ubuntu 安装 GPU 驱动、CUDA、cuDNN](https://zhuanlan.zhihu.com/p/630067986)，[Ubuntu系统安装CUDA和cuDNN](https://blog.csdn.net/KRISNAT/article/details/134870009)
	- Docker：在容器中使用显卡需要在宿主机上安装[nvidia-container-tookit](docker.md#nvidia-container-tookit)，然后使用[nvidia提供的镜像](https://zerolovesea.github.io/2024/02/07/Docker%E9%83%A8%E7%BD%B2CUDA-CUDANN/)，或者自行在镜像中安装tookit和cudnn

## 云服务器
- [AutoDL实例](https://www.autodl.com/login)
- 华为云
- 阿里云
- 腾讯云