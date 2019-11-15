---
title: Adaptive MCMC Tutorial
keywords: adaptive, mcmc, tutorial
last_updated: July 6, 2017
tags: [tutorial]
summary: "通过适应性MCMC更新BEAST中的连续参数"
sidebar: beast_sidebar
permalink: adaptive_mcmc_chs.html
folder: beast
---

## 适应性MCMC教程

在进化分析中，分段法涉及到估计各段条件独立的的分子进化模型(比如: 按基因，密码子位置或二者)，这需要估计大量的进化参数。
从而导致此类分析的计算负担增加。
多核处理器的兴起，可以实现大规模并行计算，然而许多系统发育软件包尚未充分利用这些并行计算进行多重分析。

Adaptive Metropolis(AM)算法基于经典的游走Metropolis算法(Metropolis et al., 1953)以及早期Haario等人的研究(1999)，该研究允许高斯提议分布以当前链状态为中心，并且从之前一定数量状态计算协方差。
AM算法通过计算先前所有状态的提议分布的协方差来概括。
AM算法的一个重要的优势是它从运行开始就积累信息，从而保证了在早期阶段搜索就有效。

本质上，适应性MCMC方法，如此处提出的算法，试图解析在早期分析状态下观测到的的先前参数值来自动获悉用于一组参数的最佳参数值。
因此，适应性MCMC的转换内核是非马尔科夫型，因为它们考虑了可能存在的大量先前状态，从而为马尔可夫链中的下一个状态提出了建议。

## 先决条件

本教程需要一个已经准备好的标准的BEAST XML，如BEAUti生成的。
该XML的默认转换内核将会被一个或多个适应性方差多元正态转换内核。
注意，我们正在将该功能合并到BEAUti中。

## 默认转换内核
具有默认转换内核的BEAST XML文件可以很容易地适应所使用的适应性MCMC方法，该方法通常为所谓的scaleOperators，randomWalkOperators和 deltaExchangeOperators的混合方法。
这些转换内核所操纵的参数可以很容易地添加到适应性方差多元正态转换内核中。
例如，本研究中所讨论的对食肉动物数据集使用默认转换内核的BEAST XML(Baele et al., 2017) 包含下列转换内核:

```xml
    <scaleOperator scaleFactor="0.75" weight="2">
        <parameter idref="ND5.CP1.kappa"/>
    </scaleOperator>
    <scaleOperator scaleFactor="0.75" weight="2">
        <parameter idref="ND5.CP2.kappa"/>
    </scaleOperator>
    <scaleOperator scaleFactor="0.75" weight="3">
        <parameter idref="ND5.CP3.kappa"/>
    </scaleOperator>
    <scaleOperator scaleFactor="0.75" weight="2">
        <parameter idref="ND5.CP1.alpha"/>
    </scaleOperator>
    <scaleOperator scaleFactor="0.75" weight="2">
        <parameter idref="ND5.CP2.alpha"/>
    </scaleOperator>
    <scaleOperator scaleFactor="0.75" weight="2">
        <parameter idref="ND5.CP3.alpha"/>
    </scaleOperator>

    <scaleOperator scaleFactor="0.75" weight="2">
        <parameter idref="yule.birthRate"/>
    </scaleOperator>

    <deltaExchange delta="0.75" parameterWeights="612 612 612" weight="6">
        <parameter idref="allMus"/>
    </deltaExchange>
```

换句话说，3个转换内核作用于每个密码子位置或分区的参数 &kappa;3个转换内核作用于描述模拟位点间速率异质性分布的&alpha; 参数。
此外， 树先验的出生率&psi; &mu;参数描述相关速率参数，以上参数都会被估计。
本例子中其他的转换内核包括用于树形拓扑和分支长度的过渡内核，目前仍未将其纳入适应性MCMC分析中

## 适应性MCMC转换内核

### XML表现形式#1 (已弃用)

在我们的文章(Baele et al., 2017)中所用的方法是将以上所有的(连续参数)结合为一个适应性MCMC转换内核。
实现该目的的可能方法是首先在BEAST XML的 ```<operators>...</operators>``` 模块区建立一个复合参数，该参数包含所有要使用适应性MCMC方法估算的参数:

```xml
    <compoundParameter id="allParameters">
        <parameter idref="ND5.CP1.kappa"/>
        <parameter idref="ND5.CP2.kappa"/>
        <parameter idref="ND5.CP3.kappa"/>

        <parameter idref="ND5.CP1.alpha"/>
        <parameter idref="ND5.CP2.alpha"/>
        <parameter idref="ND5.CP3.alpha"/>

        <parameter idref="yule.birthRate"/>

        <parameter idref="ND5.CP1.mu"/>
        <parameter idref="ND5.CP2.mu"/>
        <parameter idref="ND5.CP3.mu"/>
    </compoundParameter>
```

然后可以在```<operators>...</operators>``` 模块定义该复合参数，如下所示:

```xml
    <adaptableVarianceMultivariateNormalOperator scaleFactor="1.0" weight="21" initial="5000"
            burnin="2500" beta="0.05" coefficient="1.0" autoOptimize="true" formXtXInverse="false">
        <parameter idref="allParameters"/>			
        <transform type="log" start="1" end="7"/>
        <transform type="logConstrainedSum" start="8" end="10"/>
    </adaptableVarianceMultivariateNormalOperator>
```

适应性方差多元正态操作模块的重要属性是权重，操作模块的初始阶段以及老化阶段。
尽管可以手动设置β和系数属性，我们推荐使用其默认值，如上图所示。
为了确定该操作模块的权重，我们只需要对分配给上一节所有权重的默认转换内核进行求和。
我们推荐该操作模块的起始阶段使用默认值5000，在此阶段的分析过程中各种参数的分析值不会被解析。
我们通常会设置操作模块的老化阶段为该值的一半，如本例中的2500，代表着在分析过程中该参数值的数量有多少不会被分析。

操作模块除了需要提供符合参数外，还需要提供许多与操作模块相关的转换。
通常，需要为区间在[0, +∞]的参数提供'log'转换，区间在[-∞, +∞]的参数可以提供'none'类型转换。
注意，在此表示形式中，需要为转换提供“起始”和“终止”值(第一个参数的索引为1).
例如，与'ND5.CP1.kappa'到'yule.birthRate'相关的参数1到7,需要独立进行log转换。

转换类型'logConstrainedSum'应该用于总和为固定值的连续参数，如密码子间的相对率。
此XML的表现形式已经更新了(如下一部分所示)，旧XML表现形式的转换logConstrainedSum仅适用于参数集合，其固定总和等于集合中参数的数量。
例如，在相对率中，在通过密码子位置划分的情况下，固定的总和等于3，并且存在3个相对率参数。
这意味着使用旧的XML规范，适应性MCMC转换内核中包含一组需要估计的碱基频率(见下一部分)。

最后，请注意，可以在BEAST XML中使用多个适应方差多元正态操作模块，来调节(非必要)不同的(连续)参数。
例如，在文章(Baele et al., 2017)中我们展示了为树先验参数创建独立的操作模块的可能性。


### XML表现形式#2 (推荐)

当处理许多个参数且每个参数都有独立的转换时，第一个XML的表现形式(如我们文章的XML示例；Baele et al., 2017)并不灵活。
此外，有必要在XML文件中为新的转换内核创建一个复合参数。
在此部分，我们将呈现适应性MCMC转换内核的XML的表现形式，它既不需要构建复合参数也不需要手动推导转换的指数。
此外，如在此处呈现的例子所示，我们假设每个密码子位置的碱基频率将会被估计。

鉴于相关速率参数的复合参数由BEAUti自动创建，对于同一参数集转换内核的XML的表现形式可以通过以下方式指定:

```xml
    <adaptableVarianceMultivariateNormalOperator scaleFactor="1.0" weight="21" initial="5000"
            burnin="2500" beta="0.05" coefficient="1.0" autoOptimize="true" formXtXInverse="false">

        <transform type="log">
            <parameter idref="ND5.CP1.kappa"/>
            <parameter idref="ND5.CP2.kappa"/>
            <parameter idref="ND5.CP3.kappa"/>

            <parameter idref="ND5.CP1.alpha"/>
            <parameter idref="ND5.CP2.alpha"/>
            <parameter idref="ND5.CP3.alpha"/>

            <parameter idref="yule.birthRate"/>
        </transform>

        <transform type="logConstrainedSum" sum="1.0">
        	<parameter idref="CP1.frequencies"/>
        </transform>
        <transform type="logConstrainedSum" sum="1.0">
        	<parameter idref="CP2.frequencies"/>
        </transform>
        <transform type="logConstrainedSum" sum="1.0">
        	<parameter idref="CP3.frequencies"/>
        </transform>

        <transform type="logConstrainedSum" sum="1.0">
        	<parameter idref="allNus"/>
        </transform>

    </adaptableVarianceMultivariateNormalOperator>
```

尽管可以对 需要对数转换的参数指定在一组```<transform type="log">...</transform>```标签中，但是对于总结某一常量的每组参数仍需独立的```<transform type="logConstrainedSum">...</transform>```标签。
注意，此出对于不同位密码子间的相对速率的XML的表现形式使用了最近(i.e. BEAST 1.10)引入的相关速率的指定方式(BEAUti自动生成):

```xml
    <siteModel id="CP1.siteModel">
    	<substitutionModel>
    		<hkyModel idref="CP1.hky"/>
    	</substitutionModel>
    	<relativeRate weight="3.0">
    		<parameter id="CP1.nu" value="0.3333333333333333" lower="0.0" upper="1.0"/>
    	</relativeRate>
    	<gammaShape gammaCategories="4">
    		<parameter id="CP1.alpha" value="0.5" lower="0.0"/>
    	</gammaShape>
    </siteModel>

    <siteModel id="CP2.siteModel">
        <substitutionModel>
       		<hkyModel idref="CP2.hky"/>
        </substitutionModel>
       	<relativeRate weight="3.0">
        	<parameter id="CP2.nu" value="0.3333333333333333" lower="0.0" upper="1.0"/>
        </relativeRate>
        <gammaShape gammaCategories="4">
       		<parameter id="CP2.alpha" value="0.5" lower="0.0"/>
        </gammaShape>
    </siteModel>

    <siteModel id="CP3.siteModel">
        <substitutionModel>
       		<hkyModel idref="CP3.hky"/>
        </substitutionModel>
        <relativeRate weight="3.0">
        	<parameter id="CP3.nu" value="0.3333333333333333" lower="0.0" upper="1.0"/>
       	</relativeRate>
        <gammaShape gammaCategories="4">
       		<parameter id="CP3.alpha" value="0.5" lower="0.0"/>
        </gammaShape>
    </siteModel>
```

对于数据集的碱基频率和相对速率的参数化通常会用Dirichlet先验对新的XML的表现形式进行补充:

```xml
    <dirichletPrior alpha="1.0" sumsTo="1.0">
		<parameter idref="CP1.frequencies"/>
	</dirichletPrior>
	<dirichletPrior alpha="1.0" sumsTo="1.0">
		<parameter idref="CP2.frequencies"/>
	</dirichletPrior>
	<dirichletPrior alpha="1.0" sumsTo="1.0">
		<parameter idref="CP3.frequencies"/>
	</dirichletPrior>

	<dirichletPrior alpha="1.0" sumsTo="1.0">
		<parameter idref="allNus"/>
	</dirichletPrior>
```


## 自动加载平衡

使用多核CPU，BEAST可以通过XML的表现形式执行自动加载平衡。
本教程的例子包含3个分区，从而评估3种可能性。
这些可能性受到了加载平衡，从而允许BEAST确定应将哪些分区划分为多个子分区以获得最佳性能:
只需要将元素可能性放入&lt;optimizedBeagleTreeLikelihood&gt; XML的元素如下所示:

```xml
<optimizedBeagleTreeLikelihood id="optimalLikelihood">
    <treeLikelihood idref="ND5.CP1.treeLikelihood"/>
    <treeLikelihood idref="ND5.CP2.treeLikelihood"/>
    <treeLikelihood idref="ND5.CP3.treeLikelihood"/>
</optimizedBeagleTreeLikelihood>
```

BEAST 将会在屏幕上展示哪些分区将会被分割，以及就性能而言该分区的结果是什么。
注意，必须在&lt;mcmc&gt;的&lt;likelihood&gt;模块中使用新的XML元素:

```xml
<mcmc id="mcmc" chainLength="1000000" fullEvaluation="0" autoOptimize="true">
    <posterior id="posterior">
        <prior id="prior">
        ...
        </prior>
        <likelihood id="likelihood">
            <optimizedBeagleTreeLikelihood idref="optimalLikelihood"/>
        </likelihood>
    </posterior>
</mcmc>
```

鉴于在使用GPUs时，每个独立位点的似然性最终在同一设备中进行了评估。
因此，该加载平衡方法仅在多核CPUs上有用。

## 参考文献

G. Baele, P. Lemey, A. Rambaut and M. A. Suchard (2017) Adaptive MCMC in Bayesian phylogenetics: an application to analyzing partitioned data in BEAST. Bioinformatics 33(12): 1798-1805.

N. Metropolis et al. (1953) Equations of state calculations by fast computing machines. J. Chem. Phys, 21, 1087–1091.

H. Haario et al. (2001) An adaptive Metropolis algorithm. Bernouilli, 7, 223–242.

{% include links.html %}
