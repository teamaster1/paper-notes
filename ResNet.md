<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
# Deep Residual Learning For Image Recognition
## 1.摘要
    深度神经网络的出现给图像识别，图像分类任务带来了很大的突破。研究表明网络的深度对提高准确率有很大影响。
    同时，增加网络的深度野意味着更大的训练难度。第一，在传统模型中，深度的增加往往会带来梯度消失弥散/梯度爆炸。
    第二，过大的深度会带来退化（degradation）问题，网络准确率达到最值后会随深度增加而降低。这不能用overfitting解释，
    因为因overfitting造成的准确率下降只发生在测试集，而degradation同样出现在训练集中。
   
## 深度残差网络
    怎样做到既增加网络深度同时又不出现degradation的问题？
    文章提出了这样的构造。首先在一个浅层网络后面增加许多个identity映射，令拟合函数H(x)=Id(x),增加网络深度。
    其次，在每一个identity block，训练残差F（x）=H（x）-x，理想情况下F（x）将所有x映射为0，训练的难度降低，然后在F（x）层后直接加input x。
    这样做的好处有两点：
    首先，从参数数量来看，训练F（x）+x的方式与训练H（x）参数量相同，因为+x一步不需要weights。两种训练方式体量相同，但训练残差的难度小于训练Id映射
    比如输入x=5，训练将5映射到5.1，H（x）=5.1， F（x）=0.1,假设输入从5.1变为5.2，残差变化率为100%，而id映射变化率为2%，显然残差对于输入更加敏感，
    更容易训练。
    其次，增加一个shortcut connection有利于反向传播训练时梯度传递到更深层。
##  公式推导
![image](http://github.com/itmyhome2013/readme_add_pic/raw/master/images/nongshalie.jpg)
