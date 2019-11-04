---
title: 揭示季节性流感病毒波动的进化动力学
keywords: phylodynamics, influenza, tutorial
last_updated: August 8, 2017
tags: [tutorial]
summary: "本教程逐步分析了如何基于一组H3N2病毒序列重构季节性流感病毒的进化动力学，这些序列是在纽约州多个季节分离到的。该数据是跨越纽约州多个流行季节的综合数据集的一部分，该数据集已用于阐明该病毒的基因组和流行病学动力学(Rambaut et al., 2008)。我们将研究H3N2的多样性如何随时间波动。"
sidebar: beast_sidebar
permalink: phylodynamics_of_seasonal_influenza_chs.html
folder: beast
---

{% capture root_url %}{{ site.tutorials_root_url }}/phylodynamics_of_seasonal_influenza/{% endcapture %}

本教程将重构A型流感病毒H3N2亚型的[Bayesian skygrid](tree_priors#skygrid)，该病毒已经流行于北半球3个流行季节。数据集包含165个HA基因，可能需要几个小时才能运行，因此本教程将讨论如何设置此分析，然后如何基于已经执行的运行总结结果，可以从此站点下载。

{% include note.html content='本教程以之前的教程为基础，应先完成。尤其是 <a href="rates_and_dates">"估计带有时间标签的序列的速率和日期s"</a>，和 <a href="phylodynamics_of_epidemic_influenza">"流行性流感的进化动力学"</a> 提供了跳过此处的步骤的详细信息。' %}

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 数据集为'<samp>NewYork.HA.2000-2003.nex</samp>'和<a href="{{ root_url }}files/NewYork.HA.2000-2003.nex">可从此处下载</a>。</div>

##运行BEAUti

运行BEAUti，加载nexus文件(<samp>NewYork.HA.2000-2003.nex</samp>)设置`Tips`面板的`Parse Dates`为序列名称中的最后一个数字字段，如前所述。设置与上一个练习相同的进化模型(包括伽玛分布速率变化)和分子钟模型。在`Trees`标签，选择`Coalescent: Bayesian SkyGrid`作为`Tree Prior`。我们将最近采样日期(本例中`Time at last point:`<samp>2003.98</samp>，因此可追溯到1999年)之前的5年间隔构建50个网格<samp>50</samp>，从而估算出每年10个种群大小。该面板应如下所示:

{% include image.html file="image19.png" prefix=root_url %}

此刻，我们通常会生成BEAST XML文件，将其加载到BEAST中并运行它。但是，此数据集比以前大了一点，并且该模型的计算量更大，因此您可以直接分析一些已经运行的结果文件，而不用等待它完成。

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 运行更长链长的log文件<a href="{{ root_url }}files/Long_Run_H3N2_SkyGrid.zip">可以从此处下载</a>。</div>

## 分析BEAST的输出文件

通过Tracer, 我们可以根据提供的输出文件来分析运行情况(加载文件<samp>NewYork.HA.2000-2003.log</samp>)。此操作的链长为50,000,000，每5,000步采样一次，因此共有10,000个采样:

{% include image.html file="image20.png" prefix=root_url %}

为了构建贝叶斯天际线图，请在`Analysis`菜单选择`SkyGrid reconstruction...`。应出现以下窗口:

{% include image.html file="image21.png" prefix=root_url  max-width="50%" align="center"%}

设置手动范围为<samp>1999</samp>到<samp>2004</samp>，指定底部的<samp>2003.98</samp>作为`Age of the youngest tip`。点击`OK`，一段时间后，将出现以下贝叶斯天际线重构(选择固定的间隔):

{% include image.html file="image22.png" prefix=root_url %}

默认情况下y轴为对数刻度。点击`Axes`按钮，关闭`Y axis`的`Log axis`。 也需要设置`Manual range`因为HPD上限非常大。设置为<samp>0</samp>到 <samp>100</samp>将会得到以下信息:

{% include image.html file="image23.png" prefix=root_url %}

此处，可以看到季节性高峰非常强(但是可信区间表示的不确定性也非常大)。

## 参考文献

- Gill MS, Lemey P, Faria NR, Rambaut A, Shapiro B, and Suchard MA (2013) Improving Bayesian population dynamics inference: a coalescent-based model for multiple loci. Mol Biol Evol 30, 713-724.
- Minin VN, Bloomquist EW and Suchard MA (2008) Smooth Skyride through a Rough Skyline: Bayesian Coalescent-Based Inference of Population Dynamics. Molecular Biology and Evolution 25:1459-1471; doi:10.1093/molbev/msn090.
- Rambaut A, Pybus OG, Nelson MI, Viboud C, Taubenberger JK, Holmes EC (2008) The genomic and epidemiological dynamics of human influenza A virus. Nature, 453: 615-9.

## 帮助文档

BEAST网址: [http://beast.community](http://beast.community)

教程: [http://beast.community/tutorials](http://beast.community/tutorials)

常见问题: [http://beast.community/faq](http://beast.community/faq)

{% include links.html %}
