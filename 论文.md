论文：KDD 25 - Proximity Matters - Local Proximity Enhanced Balancing for Treatment Effect Estimation

结果变量Y，处理变量T。对于某斜变量x，$\rho(x)$ 为协变量 x 的概率密度函数，支撑集 $S_x=\{0,1\}$：$CATE=\tau(x)=\mathbb{E}[Y_1-Y_0 | x]$，$ATE=\mathbb{E}[\tau(x)]=\int{\tau(x)\rho(x)dx}$
Induced Representations R= $\psi(X)$ 为映射 $\psi:S_X\rightarrow S_R$ 由原始斜变量空间映射到新表示空间的结果； 结果映射 $\phi_T: \mathcal R\times \mathcal T \rightarrow \mathcal Y$，将R与是否接受处理映射到相应事实结果。此时$Y_1=\psi_1(R),Y_0=\psi_0(R)$。
$\mathcal R，\mathcal X，\mathcal Y$ 为经验分布 at a minibatch level，有 $\mathcal{X}^{T=1}$ 与 $\mathcal{X}^{T=0}$ 不同处理的斜变量分布， 对应 $\mathcal{R}_\psi^{T=1}$ 与 $\mathcal{R}_\psi^{T=0}$。
- 估计的CATE: $\hat{\tau}_{\psi,\phi}(x)=\phi_1(\psi(x))-\phi_0(\psi(x))$
- 事实结果估计误差： $\mathcal{L}^{(F)}(\psi,\phi)=\sum^{N}_{i=1}||\phi_1(\mathcal R^{T=1}_{\psi,i})-\mathcal{Y}^{T=1}_{i}||^2_2+\sum^{M}_{j=1}||\phi_0(\mathcal R^{T=1}_{\psi,j})-\mathcal{Y}^{T=1}_{j}||^2_2$
- 异质性估计精度： $\epsilon{EPHE}=\int{(\tau(x)-\hat{\tau}_{\psi,\phi}(x))^2\rho(x)dx}$ 

由于选择偏倚重存在，CFR使用 $\mathcal{L}^{(F)}+DISC(\mathcal R_\psi^{T=0},\mathcal R_\psi^{T=1})$ 减小R中分布偏倚，核心在于量化disc函数，计算表示空间中两组之间差异。

最优传输理论OT量化disc(·)：通过计算将一个分布“搬运”到另一个分布所需的最小成本来量化分布之间的差异性。Kantorovich问题：将有n，m个单元的分布$\alpha$搬运到分布$\beta$， $\mathbb{W}(\alpha,\beta):=\min_{\pi \in \Pi(\alpha, \beta)} \langle D, \pi \rangle = \min_{\pi \in \Pi(\alpha, \beta)} \sum_{i,j} D_{ij} \pi_{ij}$ 
$\Pi(\alpha, \beta):=\{\pi \in \mathbb R^{n\times m}_+ :\pi1_m=a,\pi^T 1n=b$
成本矩阵D，Dij表示将$\alpha$中第$i$个单元搬运到$\beta$中第几个单元所需要的成本，表示为两个单元在R中的欧式距离平方 $D_{ij} = ||\mathcal{R}_{\psi,i}^{T=0} - \mathcal{R}_{\psi,j}^{T=1} ||_2^2$ ，传输方案$\pi$，表示元素 $\pi_{ij}$ 从$\alpha$中第$i$个单元传输到第$j$个单元的质量大小，约束条件保证传输过程质量守恒。

但是，这类方法过度关注全局分布差异，忽略了局部临近性细微差别，在不同组中具有相似邻居的处理单元和对照单元应有更高的概率匹配在一起，因为相似单元应具有相似的结果。论文创新点在于CFR-Pro框架，pairwise临近正则化PPR，以保持局部临近性；信息子空间投影ISP 降低复杂度应对维度灾。

**在差异计算中保持局部邻近性的成对邻近正则化器**
PPR融合了Gromov Wasserstein度量，并将表示空间中的运输问题重新表述为：$\mathbb{F}(\mathcal{R}_\psi^{T=0},\mathcal{R}_\psi^{T=1}):=\min_{\pi \in \Pi(\alpha, \beta)}(\kappa\langle \pi,D \rangle+(1-\kappa)\sum_{i,j,k,l}P_{i,j,k,l}\pi_{i,j}\pi_{k,l})$其中 0≤κ≤1控制相对强度；第一项衡量全局差异。第二项通过$D_{i,j}^t​=||​R_{\psi,i}^{T=t}-R_{\psi,j}^{T=t}​||​^2_2$​量化每组内的局部邻近性，并通过$P_{i,j,k,l​}=||​D_{i,k}^{T=0}-D_{j,l}^{T=1}||_2​​$​引入这种局部邻近性。
文章中举了个例子，如果$R_{\psi,i}^{T=0}$​和$R_{\psi,j}^{T=0}$​之间的距离接近$R_{\psi,j}^{T=1}$和$R_{\psi,l}^{T=1}$之间的距离（即$​||​D_{i,k}^{T=0}-D_{j,l}^{T=1}||_2^2$→0），则表明会传输更大质量，由更大的$π_{i,j},​π_{k,l}$​表示。反之，则传输更少质量。推导出的传输计划鼓励匹配具有相似邻居的单元，从而保持局部邻近性。
使用构造的数据集（处理组用对照组的数据点旋转90度后施加较小水平位移）
- CFR只追求全局分布对齐，其匹配连线是混乱的，传输矩阵也远离对角线，因为它忽略了样本间的局部结构。
- ESCFR通过丢弃分布不重叠的样本，获得了比CFR更清晰的传输矩阵，但结果仍然存在噪声和模糊性。
- CFR-Pro通过成对邻近正则化器（PPR）利用局部邻近性，成功地实现了对角线匹配策略，证明了其在这种复杂偏差下的优越性。

**应对维度灾难的信息子空间投影器**

高维空间中，基于欧氏距离的度量会失效，因为所有点对之间的距离都变得相似，导致需要极多的样本才能准确估计分布差异。ISP通过PCA方法最小化信息损失降维，找到将高维表示投影到一个信息保留最大化的低维子空间，然后在该子空间内计算OT和PPR所需的距离。
P=k/d表示降维比例，配备PPR和ISP模块的分布差异公式化为$P_{\kappa,P}(\psi):=min_{\pi \in \Pi(\mathcal{R}_\psi^{T=0},\mathcal{R}_\psi^{T=1})}(\kappa\langle \pi,D^{U^*} \rangle+(1-\kappa)\sum_{i,j,k,l}P_{i,j,k,l}\pi_{i,j}\pi_{k,l}^{U^*})$
**CFR-Pro**

CFR-Pro文中如图所示，协变量X首先通过 ψ(⋅)映射到表示R，然后通过 ϕ(⋅)映射到潜在结果。学习目标是最小化事实结果估计的风险和组间差异。给定小批量分布 $X^{T=1}$和$X^{T=0}$，事实结果估计的误差估计为$\mathcal{L}^{(F)}(\psi,\phi)=\sum^{N}_{i=1}||\phi_1(\mathcal R^{T=1}_{\psi,i})-\mathcal{Y}^{T=1}_{i}||^2_2+\sum^{M}_{j=1}||\phi_0(\mathcal R^{T=1}_{\psi,j})-\mathcal{Y}^{T=1}_{j}||^2_2$。之后，组间差异计算为$P_{\kappa,P}(\psi)$如上式。CFR-Pro的整体学习目标是:$\mathcal{L}_{λ,κ,P}^{(CFR−Pro)​}:=\mathcal{L}^{(F)}(ψ,ϕ)+λ⋅P_{κ,P}​(ψ)$
其中λ控制分布对齐的强度，κ控制(5)中的PPR，P控制的降维比率。
如此表述，整个模型的损失函数是标准的多任务学习形式：事实预测+λ\*分布差异损失。

实验中使用了IHDP和ACIC两个数据集，多种基线模型验证，有列出超参数保证可复现。