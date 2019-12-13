---
title: 如何为参数及设置分层次的模型
keywords: beast, tutorial
last_updated: August 28, 2017
tags: [tutorial]
summary: "为不同的场景设置分层次的进化模型。"
sidebar: beast_sidebar
permalink: hierarchical_models_chs.html
folder: beast
redirect_from: "/HPMs"
---

{% capture root_url %}{{ site.tutorials_root_url }}/hierarchical_models/{% endcapture %}

## 设置分层次的进化模型(HPMs)

本教程为了设置HPM模型，您只需要下载您的电脑系统兼容的BEAST软件包(可用于Mac OS X，Windows和Linux/UNIX操作系统):

{% include beast_callout.md %}

### 同一物种不同分区的HPM

HPM教程的第一部分描述了如何对某一物种替代模型参数使用分层进化模型设置BEAST分析。

本教程的B型流感数据集为同一病毒的多个基因序列；我们将集中分析3个基因: HA，NA和PA。

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i>
The data files can be downloaded from here:
<a href="{{ root_url }}files/InfB_HA.nex"><samp>InfB_HA.nex</samp></a>,
<a href="{{ root_url }}files/InfB_NA.nex"><samp>InfB_NA.nex</samp></a>, and
<a href="{{ root_url }}files/InfB_PA.nex"><samp>InfB_PA.nex</samp></a>
</div>

启动BEAUti，选择所有的nexus文件并将他们拖拽到'Partitions'面板(或者通过'File'-'Import Data'而导入它):

{% include image.html file="InfluenzaBGenes.png" prefix=root_url width="90%" alt="3 Influenza B genes" caption="" %}

选择所有的分区，点击 'Unlink Subst. Models'允许每个分支有不同的替代参数(检查'Site Model'是否已经改变，并且每个分区不用)。
鉴于这些分区构成了同一物种的不同基因，因此我们(通常)不会取消分子钟模型的链接。

在'Tips'面板，确保[从'taxon'标签解析采样日期](tip_dates)。

在'Sites'面板，保持默认的HKY模型，但将顶部分区(如 列表中的第一个: InfB_HA)的'Base Frequencies'设置为'Empirical'，'Site heterogeneity Model'设置为'Gamma'。
此外，将每个基因划分为3个密码子位置，这将产生总共9个序列数据分区:

{% include image.html file="codonPartitions.png" prefix=root_url width="90%" alt="partitioning according to codon positions" caption="" %}

对于本教程，我们将不会改变分子钟模型和聚合模集型的设置。
对于'Priors'面板，我们将为替代模型参数指定分层的先验。
我们可以通过cmd选项(或者Windows的ctrl选项)以及窗口底部的'Link parameters into a hierarchical model'按钮实现分区中的等效参数。
首先为第一个密码子位置选择k参数，并选择'Link parameters into a hierarchical model'。
在新的进化分层模型窗口，写入一个独特的名字(如 CP1.kappa)，设置'Normal Hyperprior Stdev'为5.0，'Gamma Hyperprior Shape’和'Scale'分别为 0.01和100.0 并点击OK:

{% include image.html file="kappaCP1HPM.png" prefix=root_url width="90%" alt="HPM for the kappa parameters at the first codon positions" caption="" %}

同样地，我们可以为第二个密码子位置的k参数(如CP2.kappa)和第三个密码子位置的k参数如CP3.kappa)创建HPMs。
描述每个密码子位置的位点间的速率异质性a参数，也可以设置HPM ( 如CP1.alpha，CP2.alpha和CP3.alpha)。
总之，我们将设置6个HPMs，等同于密码子位置(3)乘以分区参数(2，如kappa and alpha)。


### 不同蝙蝠物种的HPM

HMP教程的这一部分描述了如何基于Streicker等人(2012)之前分析的数据集设置分层进化模型的BEAST分析，从而鉴别影响蝙蝠RABV进化率变化的因素。
在美洲，蝙蝠RABV通过连续的宿主跳跃并随后成功传播到新的蝙蝠物种中(见Streicker等人，2010)，建立了与宿主相关的谱系。
这为测试宿主因素(生理，环境或生态)对病毒进化速率的影响提供了难得的机会。
在此我们将通过BEAUti来设置xml,旨在为分子钟速率和替代模型参数设定分层先验。

每个宿主相关谱系的独立比对的fasta 格式文件可以从下边下载。
<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i>
包含所有文件的 .zip文件可以从此处下载 :
<a href="{{ root_url }}files/BatRabies.zip"><samp>BatRabies.zip</samp></a>
</div>

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i>
The data files can be downloaded from here:
<a href="{{ root_url }}files/Te_EF1b.fas"><samp>Te_EF1b.fas</samp></a>,
<a href="{{ root_url }}files/Te_EF2.fas"><samp>Te_EF2.fas</samp></a>,
<a href="{{ root_url }}files/Te_EF3.fas"><samp>Te_EF3.fas</samp></a>,
<a href="{{ root_url }}files/Te_LB1.fas"><samp>Te_LB1.fas</samp></a>,
<a href="{{ root_url }}files/Te_LB2.fas"><samp>Te_LB2.fas</samp></a>,
<a href="{{ root_url }}files/Te_LC.fas"><samp>Te_LC.fas</samp></a>,
<a href="{{ root_url }}files/Te_LN.fas"><samp>Te_LN.fas</samp></a>,
<a href="{{ root_url }}files/Te_M2.fas"><samp>Te_M2.fas</samp></a>,
<a href="{{ root_url }}files/Te_PS.fas"><samp>Te_PS.fas</samp></a>,
<a href="{{ root_url }}files/Tr_DR.fas"><samp>Tr_DR.fas</samp></a>,
<a href="{{ root_url }}files/Tr_EF1a.fas"><samp>Tr_EF1a.fas</samp></a>,
<a href="{{ root_url }}files/Tr_EFSA.fas"><samp>Tr_EFSA.fas</samp></a>,
<a href="{{ root_url }}files/Tr_LI.fas"><samp>Tr_LI.fas</samp></a>,
<a href="{{ root_url }}files/Tr_LS.fas"><samp>Tr_LS.fas</samp></a>,
<a href="{{ root_url }}files/Tr_LX.fas"><samp>Tr_LX.fas</samp></a>,
<a href="{{ root_url }}files/Tr_M1.fas"><samp>Tr_M1.fas</samp></a>,
<a href="{{ root_url }}files/Tr_MYSA.fas"><samp>Tr_MYSA.fas</samp></a>,
<a href="{{ root_url }}files/Tr_NL.fas"><samp>Tr_NL.fas</samp></a>,
<a href="{{ root_url }}files/Tr_PH.fas"><samp>Tr_PH.fas</samp></a>,
<a href="{{ root_url }}files/Tr_TB.fas"><samp>Tr_TB.fas</samp></a>, and
<a href="{{ root_url }}files/Tr_TBSA.fas"><samp>Tr_TBSA.fas</samp></a>
</div>

启动BEAUti，选择所有的fasta文件，并将它们拖拽到'Partitions'面板:

{% include image.html file="21BatPartitions.png" prefix=root_url width="90%" alt="21 bat partitions" caption="" %}

选择所有的分区，并点击'Unlink Subst. Models'允许每个分支根据不同的替代模型参数进行进化(检查'Site Model'是否已经改变，并特定于每个分区)。
选择'Unlink Clock Models'允许每个分支在不同的进化速率下进化(检查'Clock Model'是否已经改变，并特定于每个分区)。

在'Tips'面板，确保[从'taxon'标签解析采样日期](tip_dates) (使用‘Parse Dates’. 设置'Order'为‘last’，并点击 OK。)。

在'Sites'面板，保持默认的HKY模型，但将顶部分区(如 列表中的第一个:Te_EF1b)的'Base Frequencies'设置为'Empirical'，'Site heterogeneity Model'为'Gamma':

{% include image.html file="setSubstitutionModel.png" prefix=root_url width="90%" alt="setting the first substitution model" caption="" %}

要将相同的设置应用于所有其它的分区，选择所有的分区，并点击‘Clone Settings’按钮，并保持默认的'Te_EF1b'分区作为克隆的源分区:

{% include image.html file="cloneSettings.png" prefix=root_url width="90%" alt="cloning substitution model settings" caption="" %}

每个分区的'Clocks'面板保持默认的‘Strict Clock’模型。
在‘Trees’面板，确保取消链接树先验(面板顶部左侧)，保持默认的恒定种群大小作为先验。

{% include image.html file="treePriorSettings.png" prefix=root_url width="90%" alt="unlinking tree prior" caption="" %}

在'Priors'面板，我们将会为替代模型的参数和分子钟速率指定分层先验。
我们可以通过cmd选项(或者Windows的ctrl选项)以及窗口底部的'Link parameters into a hierarchical model'按钮实现分区中的等效参数。
首先为第一个密码子位置选择k参数，并选择'Link parameters into a hierarchical model'。
在新的进化分层模型窗口，写入一个独特的名字(如 CP1.kappa)，设置'Normal Hyperprior Stdev'为5.0，'Gamma Hyperprior Shape’和'Scale'分别为 0.01和100.0 并点击OK:

{% include image.html file="kappaHPM.png" prefix=root_url width="90%" alt="hpm for kappa parameters" caption="" %}

对于具有相同优先级设置的'alpha'参数和'alpha'作为独特命名的操作模块可以重复此操作。

{% include image.html file="alphaHPM.png" prefix=root_url width="90%" alt="hpm for alpha parameters" caption="" %}

为'clock.rate'参数和聚合模型的种群大小创建同样的HPM。
最后，按shift键选择从第一个到最后一个'last clock.rate'，并点击'Link parameters into a hierarchical model'。
'clock.rate'作为独特的名字，并设置'Normal Hyperprior Stdev'为100.0，'Gamma Hyperprior Shape''Scale'分别为0.001，1000.0，并点击'OK':

{% include image.html file="clockRateHPM.png" prefix=root_url width="90%" alt="hpm for clock.rate parameters" caption="" %}

最后，通过shift键选择所有种群大小，并未其设置HPM，设置'Normal Hyperprior Stdev'为100.0，'Gamma Hyperprior Shape''Scale'分别为0.001，1000。

{% include image.html file="popsizeHPM.png" prefix=root_url width="90%" alt="hpm for popsize parameters" caption="" %}

根据我们对这些数据的经验来看，种群大小的有效混合是很难的。
为了解决它，在'Operators'面板我们将会为种群大小的'scale'操作模块从3提高到9。
Operators的混合度很有问题，因此我们为操作模块的权重设置为30。

{% include image.html file="operators.png" prefix=root_url width="90%" alt="operators panel" caption="" %}

在'mcmc'面板，设置链长频率为250,000,000，记录频率为250,000，'BatRabies_HPM'为文件根名:

{% include image.html file="mcmcPanel.png" prefix=root_url width="90%" alt="MCMC panel" caption="" %}

我们将会进一步进行分析，因为我们将会为大量参数进行操作(每21个比对序列/替代速率以及分子钟速率有关)。
检查xml文件是否在BEAST中运行，但是没有必要运行完此文件。

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i>
The full length log file for this first HPM setup can be downloaded from here:
<a href="{{ root_url }}files/BatRabies_HPM.log"><samp>BatRabies_HPM.log</samp></a>
</div>

我们也可以检查温带地区狂犬病毒('Te_'前缀)与热带地区和亚热带地区蝙蝠病毒谱系('Tr_'前缀)的替代速率相比是否存在差异。
为此，在'Prior'面板我们可以为带有'Te_'前缀的分区的所有进化速率以及带有'Tr_'前缀的分区的所有进化速率设置独立的分层先验。
可以运行更长链长来检查结果；本例中日志文件的分层方式为'Te_clock.rate.mean'和'Tr_clock.rate.mean' (他们在日志处)。

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i>
第二个HPM设置的全场日志文件可以从此处下载:
<a href="{{ root_url }}files/BatRabies_HPM2.log"><samp>BatRabies_HPM2.log</samp></a>
</div>

总速率是否存在差异，如果存在，哪个病毒进化的更快?


## 参考文献

Streicker D. G., Turmelle A. S., Vonhof M. J., Kuzmin I. V., McCracken G. F., Rupprecht C. E. (2010) Host phylogeny constrains cross-species emergence and establishment of rabies virus in bats. Science 329:676-679.

Streicker D. G., Altizer S. M., Velasco-Villa A. and Rupprecht C. E. (2012) Variable evolutionary routes to host establishment across repeated rabies virus host shifts among bats. Proc. Natl. Acad. Sci. USA 109(48): 19715-19720.

{% include links.html %}
