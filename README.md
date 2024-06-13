## 1. 模型介绍
NeRF（Neural Radiance Fields）是一种面向三维隐式空间建模的深度学习模型，其核心思想是通过神经网络隐式地学习一个三维场景的表示，并基于该表示合成任意视角下的清晰图像。NeRF使用全连接神经网络（MLP多层感知机）作为模型基础，通过输入稀疏的多角度带pose的图像进行训练，学习出一个神经辐射场模型。该模型能够模拟光线在三维空间中的传播和交互，从而实现对场景的精确重建和新视角下的图像合成。


## 2. 数据集介绍
为了验证NeRF模型在物体重建和新视图合成任务上的性能，我们选取Fern数据集，Fern数据集是与NeRF（神经辐射场）相关研究相关的一个数据集作为实验对象。首先，我们使用相机从不同角度对物体进行拍摄，获取一系列多角度的图片。这些图片将作为训练数据，用于估计相机参数和训练NeRF模型。此外，为了评估模型的性能，我们还预留了一部分测试图片，用于在定量指标上评价模型的合成效果。
    

## 3. 实验设置
### 3.1 训练测试集划分
我们将拍摄得到的多角度图片划分为训练集和测试集。训练集用于估计相机参数和训练NeRF模型，测试集则用于评估模型的性能。为了确保模型的泛化能力，测试集中的图片应包含与训练集不同的视角和光照条件。
### 3.2 网络结构
NeRF模型的核心是一个全连接神经网络（MLP），它接受光线的起点、方向和场景中的位置作为输入，输出该位置上的颜色和密度值。在本实验中，我们采用了现成的NeRF框架，并根据实验对象的特点对网络结构进行了适当调整。


### 3.3 训练参数
Batch size: 由于NeRF模型的输入是光线信息，而非传统的图像像素块，因此batch size的设置与传统图像分类或目标检测任务有所不同。在本实验中，我们根据实验设备的性能和内存限制，将batch size设置为适当的大小。<br/>
Learning rate: 学习率是影响模型训练速度和效果的重要参数。在本实验中，我们采用了逐步衰减的学习率策略，初始学习率设置为较大的值以加快训练速度，随着训练的进行逐渐减小以避免过拟合。<br/>
优化器: 我们选择了Adam优化器来训练NeRF模型。Adam优化器结合了AdaGrad和RMSProp两种优化算法的优点，能够在训练过程中自适应地调整每个参数的学习率。<br/>
Iteration/Epoch: 由于NeRF模型的训练过程涉及到对光线信息的编码和解码，因此训练时间较长。在本实验中，我们根据实验设备的性能和训练时间的要求，设置了适当的迭代次数和训练轮数。<br/>
Loss function: NeRF模型的损失函数包括重建损失和正则化损失两部分。重建损失用于衡量模型合成图像与真实图像之间的差异，正则化损失则用于避免模型过拟合。我们采用了均方误差（MSE）作为重建损失函数，并在训练过程中加入了L2正则化项以约束模型权重的大小。<br/>
评价指标: 为了定量评估NeRF模型的性能，我们采用了PSNR（峰值信噪比）评价指标。PSNR用于衡量合成图像与真实图像在像素级别上的差异，意义：<br/>
PSNR接近 50dB ，代表压缩后的图像仅有些许非常小的误差。<br/>
PSNR大于 30dB ，人眼很难查觉压缩后和原始影像的差异。<br/>
PSNR介于 20dB 到 30dB 之间，人眼就可以察觉出图像的差异。<br/>
PSNR介于 10dB 到 20dB 之间，人眼还是可以用肉眼看出这个图像原始的结构，且直观上会判断两张图像不存在很大的差异。<br/>
PSNR低于 10dB，人类很难用肉眼去判断两个图像是否为相同，一个图像是否为另一个图像的压缩结果。<br/>
