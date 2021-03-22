# PyTorch学习笔记

# 1开发环境搭建

首先介绍一下基本信息：

电脑是win10系统，IDE（集成开发环境）是 PyCharm

搭建的环境版本是Anaconda4.8+python3.5+PyTorch1.5.1+CUDA10.2+cuDNN7.6.5

我使用的GPU是NVIDIA 950M

作为学习平台勉强够用了，作为开发平台是明显不够的，图像处理对显卡要求较高。

IDE可以凭自己喜好安装，Visual Studio等也可以。接下来逐一介绍环境的搭建流程：

>   安装过程中忽略了众所周知的网络问题，在下载网络文件失败时，请参考其他教程配置镜像或者科学上网。

## 1.1 CUDA和cuDNN安装

如果是NVIDIA显卡，可以使用TensorFlow的GPU版本，则需要先安装CUDA和cuDNN

进入CUDA官网下载： [Link](https://developer.nvidia.com/cuda-downloads)

直接下载exe文件，然后双击运行，跟随向导安装，所有地址默认，精简安装即可。

然后进入cuDNN官网下载：[Link](https://developer.nvidia.com/cudnn)

这里需要先注册一个developer账号才能下载最新的文件。下载后解压，把解压出来的cuda文件夹里的 bin, include, lib复制到 C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2即可

然后进入系统环境变量确认CUDA已经加入环境变量，然后进入cmd键入`nvcc -V`如果显示CUDA版本号即安装成功。

## 1.2 Anaconda下载与安装

在官网安装最新的python3.7版本： [Link](https://www.anaconda.com/distribution/#windows)

安装跟着向导走，没什么要改的。如果有在意的地方参考: [Link](https://docs.anaconda.com/anaconda/install/windows/)

安装好后进入Anaconda Prompt输入`conda -V`如果正常显示版本则安装成功

## 1.3 创建虚拟环境并安装python和PyTorch

在Anaconda Prompt中输入`conda create -n yourName python=3.5`创建虚拟环境并安装python3.5，python版本按自己喜好python3.5-3.7都可以，yourName是虚拟环境名可以自定义，如PyTorch。

命令执行完成后输入`conda activate yourName`进入虚拟环境，然后安装PyTorch：`conda install pytorch torchvision cudatoolkit=10.2 -c pytorch`（此处命令中的PyTorch默认安装的是最新版本的GPU和CPU整合版，指定cuda版本10.2，详情https://pytorch.org/）

安装成功后键入`python`进入python环境，键入

```python
import torch;\
... print(torch.__version__);\
... torch.cuda.is_available
```

运行后输出PyTorch版本，说明安装成功。第二项输出如果是 True，意味着你可以使用 GPU，如果是 False，意味着只能使用CPU。

>   如果报错缺少cudart64_100.dll，cudart64_101.dll或者cublas64_100.dll，是CUDA版本的原因，进入CUDA安装目录下bin文件，备份后修改相应文件的文件名至所提示的对应版本即可。

# 60min入门PyTorch

# 2 什么是PyTorch？

它是一个基于python的科学计算包，针对两组受众:

-   代替NumPy使用gpu的能力
-   深度学习研究平台，提供最大的灵活性和速度

## 2.1 张量Tensors

张量与NumPy的ndarrays很相似，另外张量也可以在GPU上用于加速计算。

创建一个未初始化的5x3的矩阵：

```python
from __future__ import print_function
import torch

x = torch.empty(5, 3)
print(x)

输出：
tensor([[-6.8357e+10,  5.6472e-43, -6.8357e+10],
        [ 5.6472e-43, -6.8359e+10,  5.6472e-43],
        [-6.8359e+10,  5.6472e-43, -6.8379e+10],
        [ 5.6472e-43, -6.8379e+10,  5.6472e-43],
        [-6.8360e+10,  5.6472e-43, -6.8360e+10]])
```

创建一个初始化的随机矩阵：

```python
x = torch.rand(5, 3)
print(x)

输出：
tensor([[0.5071, 0.8410, 0.9506],
        [0.8846, 0.6690, 0.6658],
        [0.5558, 0.2915, 0.0320],
        [0.0161, 0.9499, 0.3386],
        [0.6966, 0.0168, 0.5074]])
```

创建一个long数据类型的0矩阵：

```python
x = torch.zeros(5, 3)
print(x)

输出：
tensor([[0., 0., 0.],
        [0., 0., 0.],
        [0., 0., 0.],
        [0., 0., 0.],
        [0., 0., 0.]])
```

用数据直接创建张量：

```python
x = torch.tensor([5.5, 3])
print(x)

输出：
tensor([5.5000, 3.0000])
```

或基于现有张量创建张量。这些方法将重用输入张量的属性，例如数据类型（dtype），除非用户提供新值

```python
x = x.new_ones(5, 3, dtype=torch.double)
print(x)

x = torch.randn_like(x, dtype=torch.float)    #  提供新的属性
print(x)                                      # 结果矩阵的行列数不变

输出：
tensor([[1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.]], dtype=torch.float64)
tensor([[-0.5869,  0.8957,  0.0051],
        [-1.8404, -0.9901, -0.2846],
        [ 1.4964,  0.2876,  0.5608],
        [-0.5406,  0.1460, -2.0828],
        [-1.1608,  1.7236,  0.6916]])
```

# torch.nn机器学习基础知识

# 2 初学入门——MNIST

PyTorch安装完成后，打开IDE，新建工程并设置IDE应用刚刚配置好的PyTorch虚拟环境（不同IDE配置方法不同，不赘述），然后就可以进行开发了。

>   ！注意：如果是NVIDIA独显，打开IDE时选择使用NVIDIA显卡运行，否则PyTorch可能找不到独显而报错。

MNIST就像代码学习中的“hello world!”一样是深度学习的入门程序。MNIST是一个手写数字图像的训练集，拥有60000张学习用手写数字图片和10000张测试用手写数字图片，所有图片都有数字标签，一共有10种标签，每个数字大小都经过标准化，并保存在规格为（28 x 28pix）图片中。

现在，我们先不论具体代码的意思，运行代码试试：（此处需要科学上网下载mnist数据集）

```python
# encoding: utf-8

from __future__ import absolute_import, division, print_function, unicode_literals

from pathlib import Path
import requests

DATA_PATH = Path("data")
PATH = DATA_PATH / "mnist"

PATH.mkdir(parents=True, exist_ok=True)

URL = "http://deeplearning.net/data/mnist/"
FILENAME = "mnist.pkl.gz"

if not (PATH / FILENAME).exists():
        content = requests.get(URL + FILENAME).content
        (PATH / FILENAME).open("wb").write(content)
```

