
# Rank 1
##算法主要步骤
- 候选结节位置提取
- FP Reduction 
- 结合肺内整体特征，对结节区域再次分类
![整体架构](https://github.com/mini-Shark/feijiejie/raw/master/image/rank1_1.png)
###1. 候选结节提取

主要利用FPN网络来做候选位置提取，使用FPN对原始图像滑窗，得出每个窗是否`包含`结节的概率和结节可能出现的位置，以及每个窗的特征（这里不清楚他用这个窗的特征来干什么）。这里他提到了在难例挖掘上用了focal loss，其实也就是mask-rcnn用的，因为FPN只是一个网络的部件结构，作者对比RPN，U-NET。所以说这里就不太清楚FPN的前面他到底用的是什么结构。再者，由于天池的数据没有mask,只有bbox，所以作者用的是检测框架
![FPN架构](https://github.com/mini-Shark/feijiejie/raw/master/image/rank1_2.png)
###2. FP Reduction

根据上一步提供的检测中心点，固定尺寸的提出图像块（因为显存优势，图像的分辨率略高，图像块略大）。然后输入到3DCNN中分类，这里分类模型主要是用了ResNet, DenseNet, SEnet。而且肯定都对他们做了一定修改（3D,计算速度等）

###3. 结合肺内整体特征，对结节区域再次分类
*利用第二阶段3DCNN的深度特征，通过网络学习病例的整体特征，随后与结节自身的深度特征以及肺内统计信息Concat得到一个新的特征，输入分类器进行分类。*
原文如上所述，我猜他们应该是在上面的结果基础之上，又在结节区域附近区块，然后用上面的网络来提特征，租后加上原始结节位置的特征（也是来自于上面的CNN）然后利用肺内的统计信息来做最后的分类（这里的肺内统计信息不知道说的什么，pyradimioc里面的？不清楚）
![上下文网络架构](https://github.com/mini-Shark/feijiejie/raw/master/image/rank1_2.png)

---