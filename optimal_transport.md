?>Google OR-Tools

- https://developers.google.com/optimization/lp

?>POT python-ot-tools

- https://pythonot.github.io/index.html
- https://towardsdatascience.com/hands-on-guide-to-python-optimal-transport-toolbox-part-1-922a2e82e621

### 背景
&emsp;&emsp;最优运输问题最早是由法国数学家加斯帕德·蒙日(Gaspard Monge)在19世纪中期提出，它是一种将给定质量的泥土运输到给定洞里的最小成本解决方案。这个问题在20世纪中期重新出现在坎托罗维奇的著作中，并在近些年的研究中发现了一些令人惊讶的新进展，比如Sinkhorn算法。最优运输被广泛应用于多个领域，包括计算流体力学，多幅图像之间的颜色转移或图像处理背景下的变形，计算机图形学中的插值方案，以及经济学、通过匹配和均衡问题等。此外，最优传输最近也引起了生物医学相关学者的关注，并被广泛用于单细胞RNA发育过程中指导分化以及提高细胞观测数据的数据增强工具，从而提高各种下游分任务的准确性和稳定性。

&emsp;&emsp;当前，许多现代统计和机器学习问题可以被重新描述为在两个概率分布之间寻找最优运输图。例如，领域适应旨在从源数据分布中学习一个训练良好的模型，并将该模型转换为采用目标数据分布。另一个例子是深度生成模型，其目标是将一个固定的分布，例如标准高斯或均匀分布，映射到真实样本的潜在总体分布。在最近几十年里，OT方法在现代数据科学应用的显著增殖中重新焕发了活力，包括机器学习、统计和计算机视觉。
### Wasserstein距离
&emsp;&emsp;https://zhuanlan.zhihu.com/p/58506295

&emsp;&emsp;距离度量是机器学习任务中最重要的一环。比如，常见的人工神经网络的均方误差损失函数采用的就是熟知的欧式距离。然而，在最优运输过程中，优于不同两点之间均对应不同的概率，如果直接采用欧式距离来计算运输的损失（或者说对运输的过程进行度量和评估），则会导致最终的评估结果出现较大的偏差(即忽略了原始不同点直接的概率向量定义)。

&emsp;&emsp;Wasserstein distance 是在度量空间 M，定义概率分布之间距离的距离函数。

&emsp;&emsp;KL散度用于衡量分布间的差异程度，又称为相对熵，信息增益。KL散度不满足距离的定义：因为其不对称、不满足三角不等式。如果在高维空间两个分布不重叠或者重叠部分可忽略，则KL和JS散度反应不了远近，只是一个常量。

### 定义
#### 1.Optimal Transport
$$
\begin{array}{r}
\gamma^{*}=\arg \min _{\gamma \in \mathbb{R}_{+}^{m \times n}} \sum_{i, j} \gamma_{i, j} M_{i, j} \\
\text { s.t. } \gamma 1=a ; \gamma^{T} 1=b ; \gamma \geq 0
\end{array}
$$
#### 2.Regularized Optimal Transport
$$
\begin{array}{r}
\gamma^{*}=\arg \min _{\gamma \in \mathbb{R}_{+}^{m \times n}} \sum_{i, j} \gamma_{i, j} M_{i, j}+\lambda \Omega(\gamma) \\
\text { s.t. } \gamma 1=a ; \gamma^{T} 1=b ; \gamma \geq 0
\end{array}
$$
#### Entropic regularized OT
$$
\Omega(\gamma)=\sum_{i, j} \gamma_{i, j} \log \left(\gamma_{i, j}\right)
$$
著名的Sinkhorn算法用的就是熵正则，此形式使得问题严格凸，因此有唯一解，最终优化问题可被形式化为：
$$
\gamma_{\lambda}^{*}=\operatorname{diag}(u) K \operatorname{diag}(v)
$$
其中u和v为向量，$K=\exp (-M / \lambda)$