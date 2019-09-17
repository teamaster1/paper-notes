# WGAN 笔记——1 Towards principled methods for training generative adversarial networks

上周福航做了个GAN的分享，谈到了GAN的训练难问题，从理论公式角度谈到了GAN的loss function会给模型带来mode collapse 和 梯度消失, 这个问题在WGAN中得到了解决。作者Martin Arjovsky在WGAN之前的论文中谈到了GAN的loss问题。



在最早的GAN论文中，Goodfellow把target function设置为

$$min_{G}max_{D}V(D,G)=E_{x\sim P_{r}(x)}[logD(x)]+E_{z\sim p_{z}(z)}[log(1-D(G(z)))]$$

当generator固定时，D的最优解为

$$D*_{g}(x)=\frac{p_{data}(x)}{p_{data}(x)+p_{g}(x)}$$

因而G的优化目标为

![1568694579879](https://github.com/teamaster1/paper-notes/blob/master/paper%E5%88%86%E4%BA%AB%E4%BC%9A/WGAN1_1.PNG)

化简后

$$C(G)=-log(4)+2*JSD(p_{data}||p_{g})$$

这也就意味着如果我们先fix generator然后将discriminator训练到最优，然后再训练generator，此时generator的目标函数就是最小化$P_{data}$与$p_{g}$之间的JSD散度，**然而JSD函数是梯度消失的根源。**论文花了很大的篇幅进行推导，想了解过程可以去看论文。

![1568694579879](https://github.com/teamaster1/paper-notes/blob/master/paper%E5%88%86%E4%BA%AB%E4%BC%9A/WGAN1_2.PNG)

文章还从实验的角度证实了理论推导，如图。实验中把generator fix，然后分别训练discriminator 1,10,25 个epochs，然后再训练generator，发现经过25个epochs之后，generator的loss已经非常小了，梯度消失现象很明显。



在最开始的GAN文章中，Goodfellow为了解决梯度消失的问题提出了**the -logD alternative**技巧,把目标函数改为-logD的形式

$$min_{G}max_{D}V(D,G)=E_{x\sim P_{r}(x)}[logD(x)]+E_{z\sim p_{z}(z)}[log(-D(G(z)))]$$

在最优化D的条件下，generator的梯度可以转化为

![1568695560040](https://github.com/teamaster1/paper-notes/blob/master/paper%E5%88%86%E4%BA%AB%E4%BC%9A/WGAN1_3.PNG)

从式子中我们看到，优化策略可以概括为

1. 让$p_{g\theta}$和$P_{r}$的kl散度接近

2. 让$p_{g\theta}$和$P_{r}$的js散度最大，也就是说让两个分布尽可能不相近，这点违背了最初的意愿

   

   同时第一点会带来一个潜在的问题，也就是mode collapse。

   ![1568696033484](https://github.com/teamaster1/paper-notes/blob/master/paper%E5%88%86%E4%BA%AB%E4%BC%9A/WGAN1_4.PNG)

   >借用网上[某博客](https://medium.com/@jonathan_hui/gan-why-it-is-so-hard-to-train-generative-advisory-networks-819a86b3750b)的图，真实的数据分布记为P，生成的数据分布记为Q，图的左边表示两个分布的轮廓，右边表示两种KL散度的分布（由于KL散度的不对称性，KL(P||Q)与KL(Q||P)是不同的）。右图蓝色的曲线代表KL(Q||P)，相当于上述的可以看到，KL(Q||P)会更多地惩罚q(x) > 0而p(x) -> 0的情况（如x = 2附近），也就是惩罚“生成样本质量不佳”的错误；另一方面，当p(x) > 0而q(x) -> 0时（如x = -3附近），KL(Q||P)给出的惩罚几乎是0，表示对“Q未能广泛覆盖P涉及的区域”不在乎。如此一来，为了“安全”起见，最终的Q将谨慎地覆盖P的一小部分区域，即Generator会生成大量高质量却缺乏多样性的样本，这就是mode collapse问题。

所以我们可以看到，GAN难训练主要是因为loss function梯度中的的KL散度和JS散度，KL散度会导致mode collapse 而JS散度会导致gradient vanishing。

