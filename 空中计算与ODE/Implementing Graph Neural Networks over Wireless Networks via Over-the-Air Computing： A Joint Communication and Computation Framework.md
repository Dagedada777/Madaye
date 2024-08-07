### 图神经网络（GNN）前置知识：
 1.[通俗易懂的GCN]([GCN-图卷积神经网络算法讲解（通俗版）-CSDN博客](https://blog.csdn.net/weixin_50706330/article/details/127468165?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522172023056716800213028640%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=172023056716800213028640&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-4-127468165-null-null.142^v100^pc_search_result_base2&utm_term=gcn&spm=1018.2226.3001.4187))
2. [GCN算法详解]([GCN-图卷积神经网络算法讲解（通俗版）-CSDN博客](https://blog.csdn.net/weixin_50706330/article/details/127468165?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522172023056716800213028640%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=172023056716800213028640&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-4-127468165-null-null.142^v100^pc_search_result_base2&utm_term=gcn&spm=1018.2226.3001.4187))

图卷积算子：
![[Pasted image 20240706101733.png]]

无线网络特性：
+ 不稳定性：
+ 排列不变性和排列等变性：
+ 同质或异质：

在本文中并未明确提出采用哪种图卷积算子，因此本文采用GCN比较常见的算子进行推导：
$H = \sigma (\widetilde {D}^{-\frac{1}{2}}\widetilde{A} \widetilde {D}^{-\frac{1}{2}}HW)$

### GNN图卷积算子实现的步骤：

1. **输入特征聚合**：对于每个顶点，将其邻居的特征进行聚合。该步骤使用了 **Over-the-Air Computing (OAC)** 技术，通过无线通信将多个设备的信号聚合为一个信号，从而实现特征的加权和。

2. **激活函数的应用**：在聚合后的特征上应用激活函数。本文采用 **Quadrature Amplitude Modulation (QAM)** 星座图作为激活函数，将特征映射到复平面上的不同点。

3. **模型参数共享**：在蜂窝网络场景中，所有基站（BSs）和/或用户设备（UEs）共享相同的参数。本文采用了基于分布式随机梯度下降（DSGD）的模型融合方法，实现参数在不同设备间的共享。

4. **反向传播和梯度量化**：为了实现反向传播，本文将梯度通过QAM星座图进行量化，并通过OAC传输。这种量化方式尽管会导致收敛速度变慢，但最终性能并不会显著下降 。

5. **信道估计与均衡的简化**：在接收端，不需要进行信道估计和均衡，因为信道本身是计算的一部分。接收到的信号被分解为同相和正交信号，并通过模拟数字转换器将每个符号解调为复数。解调后的符号是传输符号的聚合符号（OAC结果），通过量化，可以通过现有通信系统实现OAC 

本文中利用OAC实现GNN的方法：
+  **图结构表示**：首先，将无线网络中的通信图结构转化为图神经网络（GNN）中的图结构，其中节点表示通信设备（如基站或用户设备），边表示它们之间的通信关系（如信道增益）。
	对于图结构的表示分为以下三种情况：
	1. 只考虑BS：只考虑基站作为顶点
	2. 考虑BS-UE：每个无线链路作为顶点
	3. **BS and UE:基站和终端都可以作为顶点(文章中采用本种方式)**
+  **OAC模拟**：利用OAC的特性，将通信信道增益作为权重，类比为图神经网络中的权重。在OAC中，接收端利用信道增益加权聚合发送端的信号，类似于图神经网络中的聚合操作。
+  **模拟图卷积操作**：通过模拟OAC过程中的信号传输和聚合操作，实现类似图卷积算子的功能。利用通信信道增益作为权重，实现节点特征的聚合和更新，类似于图卷积算子中的聚合和更新操作。
	1.  **信号传输和加权聚合**：在Over-the-Air Computing (OAC) 过程中，发送端将带有特征信息的信号通过无线信道传输到接收端。接收端利用通信信道增益作为权重对接收到的信号进行加权聚合，以获得邻居节点传输的特征信息。
	2. **节点特征的更新**：接收端利用加权聚合得到的邻居节点特征信息，结合自身节点的特征表示，进行节点特征的更新操作。这个更新操作可以通过类似于图神经网络中的更新操作来实现，例如应用激活函数和权重矩阵进行特征的变换和更新。
	3. **模型参数的调整**：在更新节点特征的过程中，可能涉及到模型参数的调整和优化。通过分布式随机梯度下降（DSGD）算法进行参数共享和模型融合，可以进一步优化系统的性能，确保节点特征的更新符合预期的模型学习目标。
+  **参数共享和模型融合**：采用分布式随机梯度下降（DSGD）算法进行参数共享和模型融合，以提高系统的效率。通过OAC实现参数聚合，进一步优化图神经网络的计算过程。



![[Pasted image 20240707164712.png]]
