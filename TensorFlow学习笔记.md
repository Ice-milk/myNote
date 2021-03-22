# TensorFlow2学习笔记

# 1 TensorFlow2环境搭建

首先介绍一下基本信息：

电脑是win10系统，IDE（集成开发环境）是 PyCharm

搭建的环境版本是Anaconda4.8+python3.7+TensorFlow2.2.0+CUDA10.1+cuDNN7.6.5

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

## 1.3 创建虚拟环境并安装python和TensorFlow

在Anaconda Prompt中输入`conda create -n yourName python=3.5`创建虚拟环境并安装python3.5，python版本按自己喜好python3.5-3.7都可以，yourName是虚拟环境名可以自定义，如TensorFlow。

命令执行完成后输入`conda activate yourName`进入虚拟环境，然后安装TensorFlow：`pip install tensorflow`（此处命令中的tensorflow默认安装的是最新版本的GPU和CPU整合版，也可以定义详细版本tensorflow-gpu）

安装成功后键入`python`进入python环境，键入

```python
import tensorflow as tf;\
... print(tf.__version__)
```

运行后输出TensorFlow版本，说明安装成功。

>   如果报错缺少cudart64_100.dll，cudart64_101.dll或者cublas64_100.dll，是CUDA版本的原因，进入CUDA安装目录下bin文件，备份后修改相应文件的文件名至所提示的对应版本即可。

# Keras机器学习基础知识

# 2 初学入门——MNIST

TensorFlow安装完成后，打开IDE，新建工程并设置IDE应用刚刚配置好的TensorFlow虚拟环境（不同IDE配置方法不同，不赘述），然后就可以进行开发了。

>   ！注意：如果是NVIDIA独显，打开IDE时选择使用NVIDIA显卡运行，否则TensorFlow可能找不到独显而报错。

MNIST就像代码学习中的“hello world!”一样是深度学习的入门程序。MNIST是一个手写数字图像的训练集，拥有60000张学习用手写数字图片和10000张测试用手写数字图片，所有图片都有数字标签，一共有10种标签，每个数字大小都经过标准化，并保存在规格为（28 x 28pix）图片中。

现在，我们先不论具体代码的意思，运行代码试试：（此处需要科学上网下载mnist数据集）

```python
# encoding: utf-8

from __future__ import absolute_import, division, print_function, unicode_literals

import tensorflow as tf
from tensorflow import keras
mnist = keras.datasets.mnist

(train_images, train_labels), (test_images, test_labels) = mnist.load_data()
train_images, test_images = train_images / 255.0, test_images / 255.0

model = keras.models.Sequential([
  keras.layers.Flatten(input_shape=(28, 28)),
  keras.layers.Dense(128, activation='relu'),
  keras.layers.Dropout(0.2),
  keras.layers.Dense(10, activation='softmax')
])

model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

model.fit(train_images, train_labels, epochs=5)

model.evaluate(test_images,  test_labels, verbose=2)
```

运行完成后会输出模型的训练和测试效果，loss表示损失函数，用于描述模型预测值与真实值的差距大小，accuracy表示准确率。

现在我们分析一下代码。

## 2.1 MNIST代码解析

为了防止读者对于python基础知识有所遗忘，本次解析会解析每一句python语句，后面再次遇到就不赘述了。

```python
# encoding: utf-8
# 提示编辑器文本解码方式为utf-8，否则用其他编辑器打开文件会出现中文乱码

from __future__ import absolute_import, division, print_function, unicode_literals
# 这是python的一个语法兼容语句，常用与python2.x和python3.x之间的语法兼容
```

这里只引用了部分新版本语法，目前所有的新版本语法如下表：

![img](https://i.loli.net/2020/02/29/c1lJBG4bMqVPeQX.png)

详细请参考官方文档。

其实如果想使用所有的Python新版本语法，可以直接使用`from __future__ import *`

```python
import tensorflow as tf
# 导入TensorFlow文件
from tensorflow import keras
# 导入keras，TensorFlow2加强了对keras的使用
```
关于keras详见[link](https://tensorflow.google.cn/guide/keras?hl=zh_cn)需科学上网，后面也会详细介绍。

```python
mnist = keras.datasets.mnist
# 读取TensorFlow自带的MNIST训练集（会自动从网上下载）缓存为mnist变量

(train_images, train_labels), (test_images, test_labels) = mnist.load_data()
# 解析训练集数据，将数据分类缓存
```
这里是使用的TensorFlow自带的mnist训练集，所以用`对象名.load_data()`解析数据。如果是自己的训练集只需要给`train_images,train_labels,test_images,test_labels`分别赋值即可，各变量的意义如下：

| 名称         | 意义     |
| :----------- | :------- |
| train_images | 训练图像 |
| train_labels | 训练标签 |
| test_images  | 测试图像 |
| test_labels  | 测试标签 |

```python
train_images, test_images = train_images / 255.0, test_images / 255.0
# 将训练和测试图像的数据换算至(0-1)之间
```
因为MNIST训练集的图像是取值为(0-255)的数据，但是神经网络训练需要(0-1)之间的数据，所以做一个换算。
```python
model = keras.models.Sequential([
# 设置模型的各层
  keras.layers.Flatten(input_shape=(28, 28)),
# 第一层Flatten，把二维数据变成一维数据，只是格式化数据，没有学习参数
  keras.layers.Dense(128, activation='relu'),
# 第二层Dense，这是一个拥有128个节点的致密神经网络层，提取图像特征,使用的激活函数是relu
  keras.layers.Dropout(0.2),
# 第三层Dropout,正则化方法，用于克服数据过拟合
  keras.layers.Dense(10, activation='softmax')
# 第四层Dense，10节点的神经网络层，主要是判断当前识别的图片为0-9中各个数字的概率,softmax表示将输出转化为概率值
])

model.compile(optimizer='adam',
# 编译模型，optimizer是优化器，用于根据每次训练的图像和损失函数更新模型
              loss='sparse_categorical_crossentropy',
# loss是损失函数
              metrics=['accuracy'])
# metrics是量度，用于控制训练和测试的步骤，这里使用的是accuracy准确度来控制步骤
```

模型训练之前，先要设置模型结构和参数，即每层的类型和参数，再编译模型，开始训练。

```python
model.fit(train_images, train_labels, epochs=5)
# 开始训练模型，输入训练图像和标签，epochs是训练集循环训练的次数，输出accuracy准确度和loss损失函数
model.evaluate(test_images,  test_labels, verbose=2)
# 开始测试模型，输入测试图像和标签，verbose是日志显示，输出accuracy准确度和loss损失函数
# verbose = 0 为不在标准输出流输出日志信息;verbose = 1 为输出进度条记录;verbose = 2 为每个epoch输出一行记录;默认为 1
```

至此，如果运行过程不报错，那就说明本地的TensorFlow环境配置正确，并且已经成功训练出了一个可以识别数字图片的深度学习模型！但是我们的模型并没有保存，当我们下次需要使用时还要从头开始训练，关于基本图像分类方法和完整的模型训练流程，将在下一章进行讲解。

# 3 基本图像分类——服装图像分类

在这一章，我们将对基本图像的分类方法进行一个完整的介绍。本章将按照实际训练模型的流程，逐步编写代码，一边编写一边解释。

本次服装图像分类使用的是Fashion MNIST，它是手写数字图像MNIST的升级版。和手写数字图像MNIST类似，图片的规格都是28x28pix，标签也是10种，60000张训练图片和10000张测试图片。事实上，它比手写数字训练集更具挑战性，但也不是特别复杂，作为入门教程很合适。Fashion MNIST的图像大致如下：

![Fashion MNIST sprite](https://i.loli.net/2020/03/01/EDPclF9zbVdBNW3.png)

下面我们开始编写代码：

## 3.1 导入运行库

首先我们依然需要导入TensorFlow和keras

```python
from __future__ import absolute_import, division, print_function, unicode_literals

import tensorflow as tf
from tensorflow import keras
```

因为需要使用矩阵运算和作图，这里我们还需要使用python的科学计算库numpy和matplotlib库里的作图工具pyplot，它可以像matlab里的作图工具一样显示图片。

```python
import numpy as np
import matplotlib.pyplot as plt

print(tf.__version__)
```

我们先测试一下代码是否运行成功，用`print(tf.__version__)`来显示TensorFlow的版本。

如果报错缺少matplotlib，可能是没有安装matplotlib库，在命令行用pip安装即可：`pip install matplotlib`（需要科学上网或者配置pip国内源）

运行成功后，删去`print(tf.__version__)`，现在需要导入`Fasion MNIST` 

## 3.2 加载FasionMNIST

```python
fashion_mnist = keras.datasets.fashion_mnist
(train_images, train_labels), (test_images, test_labels) = fashion_mnist.load_data()
```

加载训练集返回了四个矩阵：训练图像train_images，训练标签train_labels，测试图像test_images，测试标签test_labels。同样的这里需要从外网上下载数据集，如果是手动导入把各个文件导入到相应的矩阵里即可。

Fasion MNIST里的图像读入后是28x28的矩阵，每一个像素的取值是0-255，标签是一个整数矩阵，取值0-9，标签数字代表的意义如下：

| Label | Class       | 中文释义     |
| :---- | :---------- | ------------ |
| 0     | T-shirt/top | T恤          |
| 1     | Trouser     | 裤子         |
| 2     | Pullover    | 套衫         |
| 3     | Dress       | 连衣裙       |
| 4     | Coat        | 外套         |
| 5     | Sandal      | 凉鞋         |
| 6     | Shirt       | 衬衫         |
| 7     | Sneaker     | 运动鞋       |
| 8     | Bag         | 包           |
| 9     | Ankle boot  | 及踝靴，短靴 |

因为标签数字的意义没有包含在dataset中，我们可以将它按照顺序保存在一个数组里：

```python
class_names = ['T-shirt/top', 'Trouser', 'Pullover', 'Dress', 'Coat', 'Sandal', 'Shirt', 'Sneaker', 'Bag', 'Ankle boot']
```

## 3.3 认识数据集格式

数据集加载好了，我们先认识一下训练集的格式，也检查一下是否加载成功。

```python
print(train_images.shape)
# 查看训练集中图片数量，大小
# 输出为(60000, 28, 28)
# 说明有60000张图片，规格为28x28pix

print(len(train_labels))
# 查看标签的长度
# 输出60000

print(train_labels)
# 查看标签内容
# 输出array([9, 0, 0, ..., 3, 0, 5], dtype=uint8)
# 内容为数字数组，数据类型为8位无符号整型

print(test_images.shape)
# 输出(10000, 28, 28)

print(len(test_labels))
# 输出10000
```

## 3.4 数据预处理

在训练网络之前需要进行数据预处理。首先我们查看一下数据集中的第一张图片：

```python
plt.figure()
# 创建一个图形窗口
plt.imshow(train_images[0])
# 处理训练集中的第一张图片，显示其格式，但是不能显示图片
plt.colorbar()
# 添加颜色条
plt.grid(False)
# 关闭背景网格线
plt.show()
# 显示图像
```

关于Matplotlib绘图的详细用法[link](https://morvanzhou.github.io/tutorials/data-manipulation/plt/2-2-figure/)，运行结果如下：

![png](https://i.loli.net/2020/03/02/knKFVmiryUtf9xE.png)

我们可以看到图片像素的取值范围为0-255，但是神经网络需要输入的数据范围为0-1，所以需要进行换算：

```python
train_images = train_images / 255.0
test_images = test_iamges / 255.0
```

为了验证数据的标签与图像的对应关系是否正确，我们可以显示训练集的前25张图片，并在其下显示相应标签：

```python
plt.figure(figsize=(10,10))
# 创建大小为10x10的图形窗口
for i in range(25):
# 循环显示图片
    plt.subplot(5,5,i+1)
# 整个窗口分为5行5列，当前位置为i+1
	plt.xticks([])
# 显示鼠标X轴刻度
    plt.yticks([])
# 显示鼠标Y轴刻度
    plt.grid(False)
# 关闭背景网格线
    plt.imshow(train_images[i], cmap=plt.cm.binary)
# 处理训练集中的第i张图片，cmap定义了显示灰度图像
    plt.xlabel(class_names[train_labels[i]])
# 在X轴上显示标签
plt.show()
# 显示图像
```

关于cmap（colormap）[link](https://blog.csdn.net/lanchunhui/article/details/66972783)

运行结果如图：

![image-20200902145630201](https://i.loli.net/2020/09/02/ym8THSDCA76FIaX.png)

## 3.5 构建模型

构建神经网络模型需要配置模型的层，然后编译模型。

### 3.5.1 层的配置

层是神经网络的基本构件，它可以从数据中提取特征。大多数神经网络都是有若干个层连接起来的：

```python
model = keras.Sequential([
# 模型层的配置
    keras.layers.Flatten(input_shape=(28, 28)),
# 第一层Flatten，把二维数据变成一维数据，只是格式化数据，没有学习参数
    keras.layers.Dense(128, activation='relu'),
# 第二层Dense，这是一个拥有128个节点的致密神经网络层，提取图像特征，使用relu作为激活函数
    keras.layers.Dense(10)
# 第三层Dense，10节点的神经网络层，每个节点包含一个分数，表示当前识别的图片为十个类别之一的概率
])
```

### 3.5.2 编译模型

在模型训练之前，还需要进行模型编译并进行一些其他设置。

```python
model.compile(optimizer='adam',
             loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=True),
             metrics=['accuracy'])
```

-   Optimizer - 优化器，用于根据每次训练的图像和损失函数更新模型
-   Loss function - 损失函数，它会在模型训练过程中测量模型的准确性，从而引导模型训练的方向
-   Metrics - 量度，用于控制训练和测试的步骤，这里使用的是accuracy准确度这个量度来控制步骤

## 3.6 训练模型

训练模型的步骤分为：

1.  将训练数据输入模型。在本例中，训练数据位于train_images和train_labels数组中。
2.  模型通过提取图片特征并与标签结合来学习。
3.  让模型对测试数据进行预测。这里测试数据位于test_images和test_labels数组中。
4.  将预测结果与标签比较，得出测试准确率。

### 3.6.1 将数据输入模型

用`model.fit`和训练数据来训练模型，之所以叫fit，就是让模型匹配数据。

```python
model.fit(train_images, train_labels, epochs=10)
```

epoch表示循环训练的次数，这里表示循环训练10次

### 3.6.2 评估准确性

用`model.evaluate`和测试数据来评估模型的准确性。

```python
test_loss, test_acc = model.evaluate(test_images, test_labels, verbose=2)
print('\nTest accuracy:', test_acc)
```

模型在评估时的准确率低于训练时的准确率，这种现象叫做过度拟合。也就是说，模型在预测没有训练过的图片时，准确率往往没有训练过的图片高。

### 3.6.3 作出预测

当模型训练过后，就可以用它来预测一些图片的类别了。可以用一个softmax层来将模型的输出转化为预测概率：

```python
probability_model = tf.keras.Sequential([model,
                                        tf.keras.layers.Softmax()])
```
对测试集里的每一个图片进行一次预测：

```python
predictions = probability_model.predict(test_images)
```

输出对第一张图片的预测：

```python
print(predictions[0])
```

可以看出，一次预测会输出10个数字，分别表示对各个类别的预测概率。输出最大项的编号：

```python
print(np.argmax(predictions[0]))
```

输出的结果就表示模型认为测试图片最可能所属类别的编号，然后可以用`print(test_labels[0])`来验证模型的预测。

我们试着用图表来显示预测结果：

```python
def plot_image(i, predictions_array, true_labels, imgs):
# 显示测试图片和预测结果，输入参数：
# i 图片编号
# predictions_array 预测结果概率矩阵
# true_labels 真实标签矩阵
# imgs 测试图片矩阵

    predictions_array, true_label, img = predictions_array, true_labels[i], imgs[i]
# 分别赋值，赋值后参数意义：
# predictions_array 一张图片的预测结果概率矩阵
# true_label 真实标签
# img 测试图片

    plt.grid(False)
    plt.xticks([])
    plt.yticks([])
    
    plt.imshow(img, cmap=plt.cm.binary)
    
    predicted_label = np.argmax(predictions_arry)
# 获取预测标签序号
    if predicted_label == true_label:
        color = 'blue'
    else:
        color = 'red'
# 如果预测标签与真实标签相同则显示为蓝色，否则为红色

    plt.xlabel("{} {:2.0f}% ({})".format(class_names[predicted_label],
# 绘制标签，格式为：预测标签 肯定概率 真实标签
                                        100*np.max(predictions_array),
                                        class_names[true_label]),
              color=color)# 设置文字颜色
    
def plot_value_array(i, predictions_array, true_labels):
# 绘制确信概率柱状图，参数意义：
# i 图片编号
# predictions_array 预测概率矩阵
# true_labels 真实标签矩阵

  predictions_array, true_label = predictions_array, true_labels[i]
  plt.grid(False)
  plt.xticks(range(10))
  plt.yticks([])
  thisplot = plt.bar(range(10), predictions_array, color="#777777")
# 绘制柱状图
  plt.ylim([0, 1])
# 设置Y轴高度
  predicted_label = np.argmax(predictions_array)

  thisplot[predicted_label].set_color('red')
  thisplot[true_label].set_color('blue')
# 预测标签柱状图显示红色
# 真实标签柱状图显示蓝色，如果两者相同则蓝色覆盖红色
```

### 3.6.4 验证预测

让我们来看一下测试数据的预测结果。其中预测正确的图片标签显示为蓝色，预测错误的图片标签显示为红色，柱状图显示了预测模型对图片预测的确信概率。首先看一下第一张图片：

```python
i = 0
plt.figure(figsize=(6, 3))
plt.subplot(1,2,1)
plot_image(i, predictions[i], test_labels, test_images)
# 绘制图片预测结果
plt.subplot(1,2,2)
plot_value_array(i, predicitons[i], test_labels)
# 绘制预测确信概率柱状图
plt.show()
```

运行结果：

![png](https://i.loli.net/2020/03/03/OnXNRhEfWp9eyjF.png)

同样的，第十三张图片（i=12）运行结果：

![png](https://i.loli.net/2020/03/03/XbCqGULS69Jxf1O.png)

可以发现对运动鞋的确信概率要低一些。

然后再来看一组图片的结果：

```python 
num_rows = 5
num_cols = 3
# 设置要显示图片的行数和列数
num_images = num_rows*num_cols
plt.figure(figsize=(2*2*num_cols, 2*num_rows))
# 图片窗口长设置为列数的4倍，宽设置为行数的两倍
for i in range(num_images):
    plt.subplot(num_rows, 2*num_cols, 2*i+1)
    plot_image(i, predictions[i], test_labels, test_images)
    plt.subplot(num_rows, 2*num_cols, 2*i+2)
    plot_value_array(i, predictions[i], test_labels)
plt.tight_layout()
# 致密布局
plt.show()
```

运行结果：

![image-20200303211335890](https://i.loli.net/2020/03/03/umVMKWTq9wtHX21.png)

## 3.7 已训练模型的使用

`tf.keras`为同时识别多张图片或数据而做了优化，所以即使只识别一张图片也需要将它放在一个列表中：

```python
img = test_images[12]
# 从测试集里取出一张图片

img = (np.expand_dims(img, 0))
# 将这张图片放在一个列表中

print(img.shape)
# 输出为(1,28,28)，说明列表中只有一张图，图片规格为28x28
```

然后用模型作出预测：

```python
predicitons_single = probability_model.predict(img)

print(predictions_single)
# 输出为一个第一层只包含一项的二维数组，也就是对这一张图的预测确信概率
```

查看确信概率柱状图：

```python
plot_value_array(12, predictions_single[0], test_labels)
_ = plt.xticks(range(10), class_names, rotation=45)
# 此处_是一个临时变量，只是为了引导输出。rotation表示将X轴标签旋转45°显示
```

确信概率的最大值的编号即为预测的图像分类类别：

```python
predicted_label = class_names[np.argmax(predictions_single[0])]
```

predicted_label中的值即为预测结果。

# 4 基本文本分类——IMDB数据集情绪分析

前面的章节我们针对的问题都是图像分类问题，但深度学习不仅适用于图像分类，事实上，深度学习的应用范围很广泛，这里我们介绍另外一类分类问题——文本分类。

首先我们提出一个二元分类问题：如何用分类器将影评文本分为积极（positive）和消极（nagetive）两类？

本章我们使用IMDB数据集来训练文本分类器。IMDB数据集包含 50,000 条影评文本，将它平均分成测试集和训练集，也就是说，测试机和训练集中各25,000 条评论。同时训练集与测试集是平衡的（balanced），它们包含相等数量的积极和消极评论。

我们需要用到这些库：

```python
# encoding: utf-8
from __future__ import absolute_import, division, print_function, unicode_literals

# 绘图
import matplotlib.pyplot as plt
# 控制台操作库
import os
# 
import re
# 
import shutil
# 
import string

# tensorflow相关库
import tensorflow as tf
from tensorflow.keras import layers
from tensorflow.keras import losses
from tensorflow.keras import preprocessing
from tensorflow.keras.layers.experimental.preprocessing import TextVectorization
```

## 4.1 下载IMDB数据集

可以直接从打开[网站](https://ai.stanford.edu/~amaas/data/sentiment/)下载到工程目录并解压，也可以用python语句下载和解压：

```python
url = "https://ai.stanford.edu/~amaas/data/sentiment/aclImdb_v1.tar.gz"

dataset = tf.keras.utils.get_file("aclImdb_v1.tar.gz", url,
                                    untar=True, cache_dir='../data/',
                                    cache_subdir='')
# 下载完成后注释掉以上语句
```

假设数据解压在工程目录的`/data`文件夹内，现在查看一下数据集的内容：

```python
# 获取数据集文件夹的绝对路径，将相对路径替换为绝对路径
dataset = os.path.abspath('../data/')
# 生成IMDB数据集的绝对路径
dataset_dir = os.path.join(dataset, 'aclImdb')
# 显示IMDB数据集下目录列表
print(os.listdir(dataset_dir))

# 生成训练集目录绝对路径
train_dir = os.path.join(dataset_dir, 'train')
# 打开训练集中的一个文件并输出其内容
sample_file = os.path.join(train_dir, 'pos/1181_9.txt')
with open(sample_file) as f:
    print(f.read())
```

## 4.2 加载数据集并查看其内容

使用`text_dataset_from_directory`从磁盘中加载数据集，需要数据集遵循以下规则：

```python 
main_directory/
...class_a/
......a_text_1.txt
......a_text_2.txt
...class_b/
......b_text_1.txt
......b_text_2.txt
```

​	所以我们将训练集里的其他文件夹删除，只保留`aclImdb/train/pos` 和 `aclImdb/train/neg`，可以手动删除，也可以用Python这样删除：

```python
remove_dir = os.path.join(train_dir, 'unsup')
shutil.rmtree(remove_dir)
```

机器学习项目的数据及最好分成3部分：训练集 train，验证集 validation和测试集test，可以使用`validation_split`将训练集中的20%分成验证集。

```python 
batch_size = 32
seed = 42

raw_train_ds = tf.keras.preprocessing.text_dataset_from_directory(
    train_dir, 
    batch_size=batch_size, 
    validation_split=0.2, 
    subset='training', 
    seed=seed)

输出：
Found 25000 files belonging to 2 classes.
Using 20000 files for training.
```

说明在`aclImdb/train`文件中有25000条评论数据，分属于两个类，并将20000条评论分作训练集，5000条评论分作验证集。`text_dataset_from_directory`读取数据后可以直接用`model.fit`开始训练，也可以通过遍历将它分为数据和标签，方便查看：

```python 
for text_batch, label_batch in raw_train_ds.take(1):
  for i in range(3):
    print("Review", text_batch.numpy()[i])
    print("Label", label_batch.numpy()[i])

输出：
Review b"I went in expecting the movie to be completely dumb. With such a low expectation, any form of entertainment would be a pleasant surprise. The soundtrack was the best part of the movie, but poking fun at the nonsense that goes on in singles wards was also amusing.<br /><br />This said, there were many things about The Singles Ward that were completely annoying. The entire film was poorly dubbed and made watching mouths while listening to their voices very irritating. This lack of professionalism was surpassed only by the cameos of Mormon celebrities who have no business acting.<br /><br />This film will do well among Mormondom, especially in college communities where singles ward exist. However the conclusion will offer no hope for the poor losers who find themselves unmarried. (Only the pretty girls in the Singles Ward get married, the fat, ugly ones don't, but all the ugly men do) Ultimately we realize that the whole film was an advertisement for LDSSingles.com"
Label 0
...
```

发现评论文本中有网页分段标签`<br />`可能会影响训练效果，后面会介绍处这种情况的方法。

Label 0和1表示评论的类别，可以通过`class_name`查看：

```python 
print("Label 0 corresponds to", raw_train_ds.class_names[0])
print("Label 1 corresponds to", raw_train_ds.class_names[1])

输出：
Label 0 corresponds to neg
Label 1 corresponds to pos
```

接下来创建验证集和测试集：

>   在使用validation_split和subset时，确保指定一个随机seed，或者传递shuffle=False，以保证验证集和训练集分割没有重叠。

```python 
raw_val_ds = tf.keras.preprocessing.text_dataset_from_directory(
    train_dir, 
    batch_size=batch_size, 
    validation_split=0.2, 
    subset='validation', 
    seed=seed)

输出：
Found 25000 files belonging to 2 classes.
Using 5000 files for validation.
这个语句和创建训练集的语句相同，先创建的多数分到subset，后创建的剩下的分到subset


test_dir = os.path.join(dataset_dir, 'test')
raw_test_ds = tf.keras.preprocessing.text_dataset_from_directory(
    test_dir, 
    batch_size=batch_size)

输出：
Found 25000 files belonging to 2 classes.
```

# 4.3 数据预处理

使用`preprocessing.TextVectorization`层对数据进行标准化、标记化和向量化。

文本数据标准化指的是对文本进行预处理，通常是删除标点符号或HTML元素以简化数据集。标记化指的是将字符串分割成记号(例如，通过空格分割，将句子分割成单个单词)。向量化是指将标签转换成数字，这样它们就可以被输入到神经网络中。所有这些任务都可以通过这个层来完成。

上面的输出中我们发现，评论包含各种HTML标签，如`<br />`。这些标记不会被TextVectorization层中的默认标准化程序删除(默认情况下，该层将文本转换为低格式并删除标点符号，但不会删除HTML)。我们需要编写一个自定义标准化函数来删除HTML。

>   训练集和测试集需要进行相同的预处理，所以我们可以将TextVectorization直接设置在深度学习模型中。

```python
def custom_standardization(input_data):
  lowercase = tf.strings.lower(input_data)
  stripped_html = tf.strings.regex_replace(lowercase, '<br />', ' ')
  return tf.strings.regex_replace(stripped_html,
                                  '[%s]' % re.escape(string.punctuation),
                                  '')
```

使用TexVectorization层对数据进行预处理，设置`output_mode`为`int`以便来为每一个token创建整数索引：

```python 
max_features = 10000
sequence_length = 250

vectorize_layer = TextVectorization(
    standardize=custom_standardization,
    max_tokens=max_features,
    output_mode='int',
    output_sequence_length=sequence_length)

```





# 5 使用tf.hub进行文本分类

使用评论文本将影评分为*积极（positive）*或*消极（nagetive）*两类。这是一个*二元（binary）*或者二分类问题，一种重要且应用广泛的机器学习问题。

本教程演示了使用 Tensorflow Hub 和 Keras 进行迁移学习的基本应用。

使用了 [tf.keras](https://tensorflow.google.cn/guide/keras?hl=zh_cn)，它是一个 Tensorflow 中用于构建和训练模型的高级API，此外还使用了 [TensorFlow Hub](https://tensorflow.google.cn/hub?hl=zh_cn)，一个用于迁移学习的库和平台。有关使用 [`tf.keras`](https://tensorflow.google.cn/api_docs/python/tf/keras?hl=zh_cn) 进行文本分类的更高级教程，请参阅 [MLCC文本分类指南（MLCC Text Classification Guide）](https://developers.google.cn/machine-learning/guides/text-classification/?hl=zh_cn)。

```python
from __future__ import absolute_import, division, print_function, unicode_literals

import numpy as np

import tensorflow as tf

import tensorflow_hub as hub
import tensorflow_datasets as tfds

# 以下为环境测试
print("Version: ", tf.__version__)
print("Eager mode: ", tf.executing_eagerly())
print("Hub version: ", hub.__version__)
print("GPU is", "available" if tf.config.experimental.list_physical_devices("GPU") else "NOT AVAILABLE")
```

需要下载两个库，命令行输入`pip install tensorflow_hub tensorflow_datasets`

运行成功表示环境正常，同时可以判断环境版本。

## 5.1 下载IMDB数据集

 我们







# 6 回归



# 7 过拟合与欠拟合



# 8 保存和加载

