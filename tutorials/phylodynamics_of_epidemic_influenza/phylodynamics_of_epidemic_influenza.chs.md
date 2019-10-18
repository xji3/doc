---
title: 流感大流行的进化动力学
keywords: phylodynamics, influenza, tutorial
last_updated: August 25, 2017
tags: [tutorial]
summary: "本教程提供了如何通过BEAST对不同时间点(‘异质’数据)采集的样本序列集进行进化动力学分析的逐步讲解。我们将关注A型流感病毒的进化，尤其是2009年出现的猪源H1N1 A型流感病毒(H1N1pdm)。H1N1pdm数据集是研究此次大流行的起源与进化基因组学的所分析的基因组的子集(Smith et al., 2009)。旨在获得对该大流行的起源日期以及H1N1pdm的流行增长和基本繁殖数的估计。"
sidebar: beast_sidebar
permalink: phylodynamics_of_epidemic_influenza.chs.html
folder: beast
---

{% capture root_url %}{{ site.tutorials_root_url }}/phylodynamics_of_epidemic_influenza/{% endcapture %}


## 前言

第一步是将带有DATA或CHARACTERS的模块的NEXUS文件转化为BEAST XML输出文件。该步骤通过BEAUti(代表贝叶斯进化分析程序)完成。这是一个友好用户程序，用来设置进化模型和MCMC分析的选项。第二步是对含有数据，模型以及设置的输出文件运行BEAST。最后一步是分析BEAST输出的结果，来诊断问题以及总结结果。

{% include note.html content='This tutorial builds on the <a href="workshop_rates_and_dates">Estimating rates and dates from time-stamped sequences</a> which should be completed before starting this one.' %}

执行本教程前，您需要下载与您的计算机系统兼容的3个软件包(均适用于Mac OS X，Windows以及Linux/UNIX操作系统):

{% include beast_callout.md %}

{% include tracer_callout.md %}

{% include figtree_callout.md %}

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i>数据文件为'<samp>H1N1pdm_2009.nex</samp>' 和 <a href="{{ root_url }}files/H1N1pdm_2009.nex">，可以在此处下载</a>.</div>

## 运行BEAUti

{% include icon-callout.html file='icons/beauti-icon.png' content='<a href="beauti">BEAUti</a> 通过双击其图标运行BEAUti。BEAUti是一个交互式图形应用程序，用于设计分析和生成控制文件(BEAST XML文件)，BEAST将会运行该文件。' %}

加载NEXUS 格式比对的文件，仅需要从`File`菜单选择`Import NEXUS...`即可。

选择`H1N1pdm_2009.nex`。该文件为50个基因组(拼接好的8个基因节段)，13109个核苷酸长度。一旦导入，数据将会在Partitions下列出，如图所示:

{% include image.html file="image1.png" prefix=root_url %}  <br /><br />

### 设置叶节点日期

要分析进化动力学我们需要指定每个所选病毒的采样日期。本教程中，序列为2009年3月到5月H1N1 2009大流行期间采集的。切换到主窗口的`Tips` 面板，设置日期。

选中标签`Use tip dates`。以年为单位的实际采样时间编码于每个分类单元名称中，我们可以用面板顶端的`Parse Dates`按钮来提取。对于H1N1pdm_2009序列，可以保持默认值`Defined just by its order`，从其下拉按钮选择`last`，并点击`OK`。日期将会出现在窗口相应列。如果需要也可以检查并手动调整。

{% include image.html file="image5.png" prefix=root_url %}

{% include tip.html content='此处对于解读和解析不同格式的叶节点日期有许多不同的方法。<a href="tip_dates.html">对于这些选项的详细介绍详见此页。</a>' %}

### 设置替代模型

接下来要做的是点击主窗口顶部的`Sites`标签，来设置BEAST的进化模型:

{% include image.html file="image6.png" prefix=root_url %}

对于本教程，在继续`Clocks`标签之前，保持默认`HKY`模型，默认`Estimated`碱基频率，选择`Gamma`作为`Site Heterogeneity Model`(具有4个离散分类)。

### 设置分子钟模型

`Clock`面板的选项允许我们选择在严格分子钟和宽松分子钟(不相关的对数正太分布或指数分布)之间选择。由于本数据的多样性比较低，宽松的分子钟可能会过度参数化。因此我们设置为严格分子钟。

{% include image.html file="image7.png" prefix=root_url %}  <br /><br />

然后切换到`Trees`面板。

### 设置树的先验值

该面板包含对树的设置。首先，起始树指定为‘随机生成’。此处的另一个主要设置是指定‘树先验值’，来描述在聚合模型中种群大小如何随时间变化。默认的树先验设置为恒定大小的聚合先验。[不同的树先验(聚合模型和其他的模型)在此处描述](tree_priors)。

为了估计流行增长率，我们将该人口统计模型改为指数增长的聚合先验，这在直观上显示了病毒的爆发。切换`Tree Prior`的选项为`Coalescent: Exponential Growth`:

{% include image.html file="image8.png" prefix=root_url %}

### 设置先验

现在切换到`Priors`标签。此面板有一个表格展示了当前所选模型的每一个参数，以及每个参数的先验分布。较强的先验允许用户可以通过选择差异较小的特定分布来‘推断’分析。或者，我们可以选择一个弱的(离散的)先验来尝试对分析的影响最小化。注意，必须为每个参数指定先验分布，而BEAUti提供的默认选项，不一定是针对问题和要分析的数据所需要的。

{% include image.html file="image9.png" prefix=root_url %}

在本例中，指数增长率的默认先验值(拉普拉斯分布)首选相对较小的增长率，因为默认的比例为(<samp>1.0</samp>)。然而，在这种流行病规模上，增长率参数取值可以取相对较大的值。因此，我们可以通过设置比例为<samp>100</samp>来增加先验分布的方差。对先验分布设置不同的比例来检查增长率的估计(例如scale = 1, 10, 100)是有用的。

其它的先验可以保持为默认选项。

### 设置采样模块

模型中的每个参数有一个或多个“operators” (它们在其它的MCMC软件包中，如MrBayes和LAMARC，分别称作移动，建议或转换内核)。采样模块指定参数如何随着MCMC的运行而改变。BEAUti中的`Operators`标签的表格列出了参数，其采样模块以及对这些采样模块的调节设置:

{% include image.html file="image10.png" prefix=root_url %}

注意聚合生长率参数(`exponential.growthrate`)的采样模块`randomWalk`。该采样模块对于既可以正值又可以取负值的参数(严格取正值的参数可以使用比例采样模块)是合适的。在该表格中不需要任何改变。

### 设置MCMC选项

BEAUti中的`MCMC`提供了控制MCMC链长以及生成的log文件的设置。

{% include image.html file="image11.png" prefix=root_url %}

对于本数据集我们起初设置链长为<samp>100,000</samp>，取样频率为<samp>100</samp>。`File name stem:`应该设置为<samp>H1N1pdm_2009</samp>，但也可以调整它(或许添加更多有关分析的指示)。

现在，我们准备创建BEAST XML文件。从`File`(或者窗口的底部按钮)选择`Generate XML...`。BEAUti会要求您在保存文件之前再检查一次以前的设置(并会指出是否不合适)。继续并为文件选择一个名称---它会提供您在MCMC面板中提供的名称，我们通常以'.xml'结尾文件名(尽管在Windows计算机上，您可能希望给文件扩展名'.xml.txt')。

{% include tip.html content="为方便起见，请保持BEAUti窗口打开，以便您可以更改值并根据本教程后面的要求重新生成BEAST文件" %}

## 运行BEAST

一旦创建了BEAST XML文件，就可以使用BEAST执行分析本身。

{% include icon-callout.html file='icons/beast-icon.png' content='<a href="beast">BEAST</a> 通过双击BEAST图标运行' %}

BEAST启动后，将出现一个对话框，您可以在其中选择XML文件:

{% include image.html file="image12.png" prefix=root_url max-width="50%" align="center" %}

点击`Choose File...` 按钮，选择刚才创建的XML文件，然后按下`Run`。将会执行分析并且关于运行进度的详细信息也会写入屏幕。完成后，将在与XML文件相同的位置创建日志文件和树文件。

[有关BEAST对话框中其他选项的更多信息，请参见此页面](beast)。

## 分析BEAST的输出

{% include icon-callout.html file='icons/tracer-icon.png' content='为了分析运行BEAST的结果，我们将使用<a href="tracer">Tracer</a>。双击Tracer图标运行Tracer。' %}

从`File`菜单选择`Import Trace File...`选项。选择前一部分创建的log文件<samp>H1N1pdm_2009.log</samp>。该文件将加载，并且将显示一个类似于以下窗口。

{% include image.html file="image13.png" prefix=root_url %}

与[之前的教程](rates_and_dates)类似，所有迹线的有效样本大小(ESSs)都比较小(少于100的ESS在Tracer中分别用红色突出显示)。窗口的右下角是样本的频率图，在上图的`posterior`迹线中具有多个峰。

如果选择右侧标签为`Trace`的选项卡，我们将看到原始的追踪---针对MCMC链中步骤的采样值:

{% include image.html file="fig14.png" prefix=root_url %}

显然，默认情况下，链长的10％的老化是不够的(在链的第一部分，后验值仍在增加)。双击左上角的`Burn-In`列，并且编辑(在上面的情况下，至少2i<samp>20,000</samp> 是必需的)。 但是，仍然很清楚，链长为<samp>100,000</samp>是足够的。查看ESS值(通常以低两位数表示)表明链长<samp>10,000,000</samp>更合适。在现代计算机上，这可能只需要大约20分钟，但是我们提供了此长度的运行结果，您可以在本节的其余部分中使用该输出。

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i>运行较长链长的文件<a href="{{ root_url }}files/Long_Run_H1N1pdm_Exponential.zip">可以从此处下载</a>.</div>
</div>

加载新的log文件(<samp>H1N1pdm_2009.log</samp>)到Tracer(也可以保留旧的进行比较)。点击`Trace`标签，查看原始的迹线图。

同样，我们选择了可生成1000个样本的选项，ESS>300，对于聚合模型参数而言，样本之间几乎没有自相关。图中没有明显的趋势表明MCMC尚未收敛，并且迹线中没有大规模的波动，这表明混合不良。

当我们对MCMC的行为感到满意时，我们现在可以继续关注的参数之一:我们选择指数增长聚合模型作为树先验。在左侧表格选择`exponential.growthRate`。现在选择标签为 `Marginal Prob Distribution`的选项来选择密度图。这显示了该参数的后验概率密度图。您应该看到类似以下的图:

{% include image.html file="image15.png" prefix=root_url %}

如您所见，后验概率密度大致呈钟形。默认值是显示核密度估计(KDE)，它是拟合到数据的平滑概率密度。切换到顶部的`Display:`选项为`Histogram`，查看不平滑的频率图。这里仍然有很多噪音，但这是对分布的良好估计。

{% include callout.html type="warning" content="The <code>age(root)</code> 统计信息提供了整个树的最近共同祖先的时间的估计。在这种情况下，病毒从猪跨种传播至人类，流行开始可能是合理的估计。MRCA的平均估计和95％的HPD是多少?<br /><br /><br />" %}

在`Analysis`菜单的`Demographic Reconstruction...`可以可视化增长估计。选择此选项并设置如下所示的对话框:

{% include image.html file="image16.png" prefix=root_url %}

选择`Demographic Model: Exponential Growth (Growth Rate)`---注意，必须选择在BEAUti中选择的树先验，在此处不能修改。Tracer会自动识别模型的参数(`exponential.popSize`和`exponential.growthRate`)。`Maximum time is the root height's:`选项选择`Upper 95% HPD`。这意味着它将扩展到根年龄可信区间的范围。设置`Age of youngest tip:`为<samp>2009.403</samp>(病毒最近采样的日期)。也可以选择`Use manual range for bins:`使时间刻度更整洁---选择<samp>2009.0</samp>为<samp>2009.42</samp>。然后点击`OK`，窗口将出现:

{% include image.html file="image17.png" prefix=root_url %}

这显示了中位数增长率的指数增长线和该增长95% HPD区间作为实心区域。它是对数刻度，因此是一条直线。可以使用`Setup...`按钮进行轴设置。垂直虚线表示树根日期的95% HPD。

{% include callout.html type="primary" content="exponential.growthRate (\\( r \\))提供了H1N1pdm 2009流行增长的估计。鉴于\\( N t = N_0 e^{-r t} \\) (with \\( N_0 \\)为当前的种群大小，\\( r = 21 \\)的倍增时间大约为0.03年或者12天。有趣的是，已经证明基本增长比(\\( R_0 \\))与增长率有关---[详细信息见此页](/estimating_R0.html)。然而，基本增长数不仅取决于(\\( r \\))，而且还可以很好地估计世代时间分布，它反映了传播链中两次连续感染之间的时间。如果我们假设生成时间分布遵循伽马分布，则\\( R_0 = (1 +  r / b) ^a \\)，\\( a \\)和\\( b \\)是伽马分布的参数(\\( a = \mu^2 / \sigma^2 \\), \\( b = \mu / \sigma^2 \\))。" %}

{% include callout.html type="warning" content="取\\( \mu = 3 \\)天，\\( \sigma = 2 \\)天，H1N1pdm 2009的\\( R_0 \\)的平均估算值是多少\\( R_0 \\)?<br /><br /><br />" %}

## 总结树

我们已经看到了如何使用Tracer诊断MCMC运行并产生对模型参数的边际后验分布的估计。接下来，我们可以使用BEAST软件包中提供的[TreeAnnotator](treeannotator)来汇总采样树中包含的信息。

{% include icon-callout.html file='icons/utility-icon.png' content='双击图标运行TreeAnnotator。' %}

TreeAnnotator 总结单个'target'树，并使用来自整个树样本的总结信息对其进行注释。汇总的信息包括平均节点年龄(带有HPD间隔)，后验概率支持值以及每个分支的的平均进化速率 (对于宽松分子钟模型此处可以变化)。程序为观测到的指定的'目标树'。

设置`Burnin (as states):`为<samp>1,000,000</samp>。相当于链长的10%，在Tracer中我们可以证明以上已经足够。其它的选项使用默认值---`Posterior probability limit: 0`，`Target tree type: Maximum clade credibility tree`，以及`Node heights: Median`。

使用`Choose File...`按钮来选择输入树的文件，<samp>H1N1pdm_2009.log</samp>。

{% include tip.html content='对于BEAST的大多数包来说，如果有按钮可以选择文件，那么也可以通过拖拽文件到此处' %}

为输出的树文件选择名字(例如，<samp>H1N1pdm_2009.MCC.tre</samp>)。

一旦选择以上所有的选项，点击按钮`Run`。TreeAnnotator将会输出树的文件，并将总结的树写入所指定的文件。树为标准的NEXUS格式文件，因此可以可以导入支持该格式的任何一个画树包。然而，也包含仅仅可以通过[FigTree](figtree)程序来识别额外的信息。

## 查看注释树

{% include icon-callout.html file='icons/figtree-icon.png' content='<a href="figtree">FigTree</a> 用户友好的图形程序，用于查看BEAST提供的树和相关信息。双击FigTree图标运行它。' %}

现在运行FigTree，从`File`菜单选择`Open...`命令。选择在前部分用TreeAnnotator创建的树文件。树将会在FigTree展示。窗口左侧是控制树如何展示的选项和设置。在本例中，我们想呈现树中每个分支的后验概率，估计每个节点的年龄。因此需要改变一些设置。

首先，通过`Tree`菜单下的`Increasing Node Order`调整节点顺序。切换到控制面板左侧的`Branch Labels`，通过点击左侧的箭头打开此部分。现在选择`Display:`下的`posterior`。降低`Sig. Digits`为 <samp>2</samp>。

现在，我们希望在树上显示条形图，以表示每个节点的日期中估计的不确定性。TreeAnnotator会将相关信息以95%最高后验概率密度(HPD)的置信区间形式放在树中。切换到控制面板中的`Node Bars`并打开此部分；选择`height_95%_HPD`来展示节点高度的95% HPDs。

我们还可以绘制此进化史的时间轴。打开`Scale Axis`(关掉`Scale bar`)选择`Scale Axis`选项中的`Reverse Axis`(也可以略微增加字体大小)。为了适当缩放，请打开控制面板的`Time Scale`部分，选择`Offset:`为<samp>2009.403</samp>(病毒取样的最近日期)。

最后，打开`Appearance`面板，并更改`Line Weight`用粗线绘制树。生成的树将如下所示:

{% include image.html file="image18.png" prefix=root_url %}

任何一个选项都不会改变树的拓扑结构或分支的长度，因此可以随意探索这些选项和设置。还可以保存树，这将保存您的大多数设置，以便当您再次将其加载到FigTree时，将几乎按照您选择的方式显示它。该树还可以导出图形文件(pdf，eps，等)。

## 参考文献
- Smith GJD, Vijaykrishna D, Bahl J, Lycett SJ, Worobey M, Pybus OG, Ma SK, Cheung CL, Raghwani J, Bhatt S, Peiris JSM, Guan Y & Rambaut A (2009) Origins and evolutionary genomics of the 2009 swine-origin H1N1 influenza A epidemic. Nature 459, 1122-1125.

## 帮助文档

BEAST网址: [http://beast.community](http://beast.community)

教程: [http://beast.community/tutorials](http://beast.community/tutorials)

常见问题: [http://beast.community/faq](http://beast.community/faq)

{% include links.html %}
