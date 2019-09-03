---
title: 连续空间中的系统地理发育学扩散-以西尼罗病毒(WNV)为例
keywords: phylogeography, WNV, tutorial
last_updated: August 9, 2017
tags: [tutorial]
summary: '本教程基于一组病毒基因组序列集对入侵北美的西尼罗病毒的空间动力学重构进行了逐步讲解，该数据集的病毒是在美国不同县不用时间点(异质性数据)分离的(<a href="http://www.pnas.org/content/109/37/15066.short">Pybus et al., 2012, PNAS, 109(37), 15066-15071</a>)。
WNV 是一种由蚊子传播的RNA病毒，其主要宿主为鸟类，首次于199年8月在美国纽约城市检测到。数据为在1999-2008年采集到的104条基因组。我们将估计WNV在连续空间中的祖先位置，在入侵期间的传播速率，测试病毒是否以相对恒定的速率传播。此外，此外，我们将采用程序 ‘Renaissance counting’ (<a href="https://academic.oup.com/bioinformatics/article-lookup/doi/10.1093/bioinformatics/bts580">Lemey et al., 2012, Bioinformatics, 28(24), 3248-3256</a>)，以非同义/同义替代率(dN/dS)的形式来量化特定位点的选择压力。文艺复兴计数在核苷酸空间的整个进化历史中映射替换，然后‘计算’ 相应的同义替换和非同义替换数，他们的‘中性’预期，并将经验贝叶斯程序应用于这些计数以得出dN/dS的估计值。'
sidebar: beast_sidebar
permalink: workshop_continuous_diffusion_wnv_chs.html
folder: beast
---

{% capture root_url %}{{ site.tutorials_root_url }}/workshop_continuous_diffusion_wnv/{% endcapture %}

## 前言

第一步是将比对好的fasta格式的文件转换为BEAST XML输入文件。该操作通过程序BEAUti(t代表着贝叶斯进化分析实用程序)来完成。这是一个用户友好的程序，用于设置MCMC分析的进化模型和选项。第二步是使用包含数据，模型和设置的输入文件实际运行BEAST。最后一步是探索BEAST的输出，以便诊断问题并总结结果。

要执行本教程，您需要您的计算机系统兼容的三个软件包(这三个软件包均适用于Mac OS X，Windows and Linux/UNIX 操作系统):

{% include beast_callout.md %}

{% include beagle_callout.md %}

{% include tracer_callout.md %}

{% include figtree_callout.md %}

{% include spread3_callout.md %}

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 本教程需要的所有文件
<a href="{{ root_url }}files/continuousTutorialFiles.zip">可以从此处下载</a>。
如果您已经下载了压缩文件夹，没有必要再下载本教程链接的其他文件或文件夹。
</div>
<!--
<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 本教程所需所有文件压缩文件夹<a href="{{ root_url }}discreteTutorialFiles.zip">可以从此处下载</a>。 (如果您下载了该压缩文件，没有必要再下载链接中的其他文件)t) </div>
-->

<!--
## 练习1:宿主和地理位置的重建
-->

### 运行BEAUti

BEAUti程序是一个用户友好的程序，用于设置BEAST的模型参数。双击其图标运行BEAUti。

#### 载入序列数据文件

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 本教程的数据文件称为'<samp>WNV.fas</samp>' 与 <a href="{{ root_url }}files/WNV.fas">可以从此处下载</a>.</div>

<!--
本教程的输入文件 batRABV.fas，可以[从此处下载]({{ root_url }}files/WNV.fas)。
-->
载入比对文件，仅仅需要从文件菜单选择`Import Data...`选项:

{% include image.html file="01_ImportData.png" prefix=root_url caption="" %}

并浏览WNV.fas文间来加载。

<!--
##### 序列比对
-->

选择WNV.fas文件。该文件为包含比对过的104个WNV基因组，其长度为11029个核苷酸。一旦加载，序列数据将会在`Partitions`下列出，如图所示:

{% include image.html file="02_sequencePartition.png" prefix=root_url caption="" %}

<!--
双击表格行(不是Partition名称) 来展示实际比对序列:

{% include image.html file="sequenceAlignment.png" prefix=root_url caption="" %}
-->

#### 指定采样日期信息

默认情况下，假定所有分类单元的日期都为零(即假设序列同时采样)。在本教程中，WNV序列在不同年份间进行采样可追溯到1999年。要设置这些日期，使用窗口顶部的选项卡切换到`Tips`面板。

<!--
{% include image.html file="fig3.png" prefix=root_url caption="" %}
-->

选择标签为`Use tip dates`对话框。以年为单位的实际采样时间以每个分类单元的名称编码，我们可以简单地编辑‘Date’ 列中的值以反映这些值。但是，如果分类单名称包含校准信息，则在BEAUti中指定序列日期的便捷方法是使用`Data`面板顶端左侧的`Parse Dates`按钮。单击此按钮将显示一个对话框:

{% include image.html file="03_guessDates.png" prefix=root_url  max-width="25%" align="center" caption="" %}

此操作尝试从分类单名称中包含的信息中猜测日期。它的工作原理是尝试在每个名称中找到一个数字字段。如果分类单元名称包含多个数字字段，则可以指定如何找到与采样日期对应的数字字段。您可以(1)指定日期字段的顺序(例如，第一个，最后一个或其间的某个位置)或者(2)指定前缀(紧接在每个名称中的日期字段之前的某些字符)和该字段所在顺序，或者(3)定义正则表达式(REGEX)。

解析数字时，您可以要求BEAUti为每个猜测日期添加固定值。例如，可以添加`1900`将日期从2位数转换为4位数。在分类单元名称中任何给定的`00`都将称为`1900`。但是，如果`00`或者`01`，等表示在2000年，2001年采样的序列，`2000` 需要被添加。这将通过选择`unless less than: ..`和`..in which case add:..`选项添加例如2000到任何小于10的日期来实现。在本教程中，这些操作不是必需的，因为日期在序列名称的末尾完全指定。还有一个选项可以解析日历日期，以及解析具有各种精度的日历日期。对于WNV 序列，可以保持默认的`Defined just by its order`，从`order`下拉菜单中选择`last`，设置`Prefix`为下划线并按`OK`。日期将显示在主窗口的相应列中。

{% include image.html file="04_datesSpecified.png" prefix=root_url caption="" %}

您现在可以检查这些并根据需要手动编辑它们。在窗口的顶部，您可以设置日期的单位(年，月，日)以及它们是否相对于过去的时间点(如2005年) 或者从现在开始的时间向后指定(如放射性碳年龄)。

`Height`列列出了相对于时间0的提示年龄(本例子为2007.63)。`Uncertainty` 列允许指定采样时间的精确度。这在我们的情况下是有用的，因为一些采样日期在确切的日期是已知的，而其他采样日期仅已知采样年份(在分类名称中没有小数或在Date列中具有.0小数的那些)，BEAST并且BEAST允许整合后者的不确定性。要充分利用我们的采样日期的不确定性，请选择所有类别(按住cmd键)并在Tip 窗口中选择仅在年份中已知的采样日期，然后单击`Set Uncertainty`。

{% include image.html file="05_setUncertainty.png" prefix=root_url caption="" %}

输入‘1.0’作为1年的精度值。这将指示BEAST在这些采样日期中添加半年并围绕此新值构建1年的统一窗口。要在MCMC分析中估计此窗口约束内的相应采样日期，请在Tips面板的底部左侧选择`sampling uniformly from precision`，保持`Apply to taxon set: All taxa`为默认。

{% include image.html file="06_tipdateSampling.png" prefix=root_url caption="" %}


#### 指定特征信息

接下来要做的是单击主窗口顶部的选项卡`Traits`。特征可以是特定分类群固有的任何特征，例如，地理位置或宿主物种。该步骤将基于每个序列的特征规范将纬度和经度分配为每个分类群的双变量地理位置。<!-- in the WNV\_lat\_long.txt file, which [downloaded from here](files/WNV_lat_long.txt). -->
<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 以制表符分隔的文件，将每个分类与经度和纬度相关联'<samp>WNV_lat_long.txt</samp>' <a href="{{ root_url }}files/WNV_lat_long.txt">可以从此处下载</a>.</div>

要将序列与特征相关联，我们需要在`Traits`选项卡下导入新特征(点击`Import Traits...`)。这将打开一个新窗口，允许导入具有特征的文件。浏览并打开WNV\_lat\_long.txt制表符分隔文件，其中包含以下内容:

	traits	lat	long
	WG007_Hs_2005.59	31.82	-106.56
	WG009_Hs_2005.67	32.28	-106.74
	WG011_Hs_2006.66	31.78	-106.50
	WG013_Hs_2007.48	31.84	-106.53
	WG024_Hs_2003.53	33.81	-117.83
	WG080_Hs_2004.56	34.22	-118.44
	WG091_Hs_2004.66	34.14	-117.15
	WG099_Hs_2004.49	34.04	-117.02
	WG101_Hs_2005.57	33.95	-117.73
	WG103_Hs_2005.58	33.85	-118.15
	WG116_Hs_2005.65	33.84	-118.08
	WG124_Hs_2005.69	32.31	-111.21
		...
	DQ080059\_Pn\_2003	38.58	-121.49

点击`OK`后，在左侧窗口中选择'lat'和'long'特征，并指定`create partition from trait..`。在弹出的窗口中，输入此分区的名称，例如'location':

{% include image.html file="07_traitSelection.png" prefix=root_url caption="" %}

这个带有2个'Sites'且具有连续'Data Type'的新'location'将显示在`Partitions`标签下:

{% include image.html file="08_traitPartitions.png" prefix=root_url caption="" %}

####设置序列和特征进化模型

接下来要做的是点击主窗口顶部的`Sites`选项卡。这将揭示BEAST的进化模型设置。哪些选项出现取决于数据是核苷酸，氨基酸还是性状。

本教程假设您熟悉可用的进化模型，但是在BEAUti中选择模型还有几点需要注意:

选择`Partition into codon positions`选项假设数据为比对过的密码子。然后，该选项将估计每个密码子位置独立的替代率，或者1+2vs3的替代率，具体取决于设置。

选择`Unlink substitution model across codon positions`将指定BEAST为每个密码子位置估计独立的的转换-颠换比率或一般时间可逆速率矩阵。

选择`Unlink rate heterogeneity model across codon positions`将指定BEAST为每个密码子位置估计一组速率异质性参数(伽马形状参数和/或不变位点的比例)。

对于本教程中的核苷酸替换模型，保留默认的HKY替换模型，将碱基频率保持默认为'Estimated'，‘Site Rate Heterogeneity’为‘None’，并将数据划分为3个分区以用于编码位置(3个分区: 位置1,2,3位)。由于复兴计数过程中我们要获取特定位点的dN/dS估计，我们无法模拟每个密码子位置分区的异质性速率，因此我们假设我们通过对编码区位置的相对速率建模来捕获多数位点速率异质性。

{% include image.html file="09_substModel.png" prefix=root_url caption="" %}

<!-- PL edited up to here -->

接下来点击`Substitution model窗口的'location'，保持默认的`Homogenous Brownian model`，并且选择`Bivariate trait represents latitude and longitude`。此选项生成特定于双变量空间特征的扩散统计(经度和纬度按此排序)。并且选择`add random jitter to tips`，该选项将从特定抖动窗口大小随机均匀绘制的噪声添加到重复(位置)特征。将`jitter window size`设置为0.5。当并非所有序列都与唯一位置相关时，噪声避免了布朗扩散模型的不良性能。
{% include image.html file="10_traitModel.png" prefix=root_url caption="" %}

#### 设置‘molecular clock’模型

`Clocks`面板中的‘Molecular Clock Model’选项允许我们在严格和宽松(不相关的对数正态或不相关指数分布)分子钟间选择。我们将选择对数正态宽松分子钟(Uncorrelated)模型执行我们的运行:

{% include image.html file="11_clockModel.png" prefix=root_url caption="" %}

现在切换到`Trees`面板。

#### 设置树先验

此面板包含有关树的设置。首先，将起始树指定为‘randomly generated’。此处的另一个主要设置是指定‘Tree prior’，该选项描述了根据聚结模型预计种群大小如何随时间变化。默认的树先验设置为恒定大小的聚合先验。我们将选择标准参数化的指数增长聚合模型作为人口统计树先验 (`Coalescent: Exponential Growth`)，这直观地映射了病毒的爆发。

{% include image.html file="12_treePrior.png" prefix=root_url caption="" %}

#### 祖先状态设置

在`States`面板，我们将通过选择`Reconstruct synonymous/non-synonymous change counts`来打开复兴计数程序:

{% include image.html file="13_robustCounting.png" prefix=root_url caption="" %}

对于'location'分区，检查是否选中了选项`Reconstruct states at all ancestors`(默认):

{% include image.html file="14_locationStates.png" prefix=root_url caption="" %}

#### 设置先验值

查看`Priors`标签下的先验设置。该面板有一个表格，显示当前所选模型的每个参数以及每个参数的先验分布。<未明确指定的先验值将显示为红色，而不适当的先验(因此导致不正确的后验和不正确的边际似然值)显示为黄色(如，allMus)。单击此参数的先验，将出现先验的选择窗口。密码子特定位置的相对比率(CP1.mu, CP2.mu and CP3.mu)，其被限制平均值为1，仍然需要合适的先验分布。我们在这里指定对数正态分布，其参数log(平均值)为0，log(标准差)为1.5。请注意，单击确定确认此设置后，先前的设置变为黑色。
>
在本例中，我们可以保留默认的先验

{% include image.html file="15_priors.png" prefix=root_url caption="" %}

#### 设置采样模块

模型中的每个参数都有一个或多个“operators”(他们在其它的MCMC软件包，如：MrBayes和LAMARC中分别称为移动，建议或转换内核)。采样模块指定参数在MCMC运行时如何更改。BEAUti中的`Operators`标签有一个表格，列出了参数，参数的采样模块，运算符和调整设置:

{% include image.html file="16_operators.png" prefix=root_url caption="" %}

第一列是参数名称。这些将被称为类似于kappa的名称，kappa表示HKY模型的kappa参数(转换-巅换偏差)。下一列具有作用于每个参数的运算符类型。例如缩放采样模块按比例向上或向下缩放参数，随机游走采样模块向参数添加或减去一定量，统一操作模块只是在一个范围内统一选取一个新值。与树有关或树节点的分歧时间有关的参数，具有特殊的采样模块。从BEAST v1.8.4开始，可以使用不同的选项来探索树空间。在本教程中，我们将使用‘classic operator mix’，它由一组树转换内核组成，这些内核会建议对树进行更改。同时也有一个选项来固定树的拓扑结构，以及‘new experimental mix’选项，该选项目前处于开发中，其目的是提高大型系统发育树的混合度。

下一列为`Tuning`，为采样模块提供调整设置。某些采样模块没有任何调整设置，因此在此列下为n/a。调整参数将确定每个采样模块将进行的移动有多大，这将影响MCMC接受该更改的频率，这反过来又会影响分析的效率。对于多数采样模块(如随机游走和子树滑动采样模块)，较大的调整参数意味着较大的移动。然而，对于比例采样模块，调整参数值接近0.0意味着更大的移动。在窗口的顶部是一个名为`Auto Optimize`的选项，选择后，当MCMC运行时，将自动调整调整设置以尝试实现最高效率。在采样模块的列表运行结束时，它们的性能和这些调整设置的最终值将写入标准输出。
<!--
之后，这些设置可用于设置起始调整设置，以便将后续运行中达到最佳性能所花费的时间降至最低。
-->

下一列‘Weight’，指定每个采样模块相对于其他采样模块的应用频率。有些参数往往非常有效地采样-如kappa参数-这些参数可以使其采样模块降低权重，以便它们不会被经常更改。我们将从使用此分析的默认设置开始。从BEAST v1.8.4开始，可以使用不同的选项来探索树空间。在本教程中，我们将使用‘classic operator mix’，它由一组树转换内核组成，这些内核会建议对树进行更改 同时也有一个选项来固定树的拓扑结构，以及‘new experimental mix’选项，该选项目前处于开发中，其目的是提高大型系统发育树的混合度。在本教程分析中我们可以保留默认的采样模块设置


#### 设置MCMC选项

BEAUtid的`MCMC` 提供了控制MCMC链的设置。首先为‘Length of chain’，即MCMC在完成之前在链中所运行的步骤数。运行多长时间取决于数据集的大小，模型的复杂性和所需答案的精确度。默认值10,000,000完全是任意的，应根据数据集的大小进行调整。稍后我们将看到如何使用Tracer分析生成的log文件，以检查特定链长是否足够。

接下来的两个选项指定当前参数值应在屏幕上显示并记录在log文件中的频率。屏幕输出仅用于监视程序的进度，因此可以设置为任何值(尽管设置得太小，屏幕上显示的信息量将减慢程序的速度)。对于log文件，应该相对于链的总长度设置该值。过于频繁的采样将导致非常大的文件，在估计的精确度方面几乎没有额外的好处。样本太少，log件中不包含有关参数分布的大量信息。您可能希望存储不超过10,000个样本，因此应将其设置为链长的1/10,000。

{% include image.html file="17_mcmc.png" prefix=root_url caption="" %}

对于本数据集，我们最初将链长设置为100,000，因为这将在大多数现代计算机上相当快地运行。因为执行的复兴计数过程时对于大型编码序列比对而言每次执行状态都将被log，这是相当耗时间的，所以我们将参数log设置为5000，并且每100个状态显示于屏幕。

下一个选项允许用户设置文件主干名称; 将其设置为‘WNV_homogeneous’('homogeneous' for 'homogeneous Brownian diffusion')。接下来的两个选项提供参数和树的log文件。这些将根据文件主干名称设置。您还可以将采样模块分析记录到文件中。还可以选择仅从先验分布采样的选项，这可用于评估从数据中提取信息时我们的后验估计的差异。在这里，我们不会选择此选项，而是分析实际数据。

最后，可以选择执行边际似然估计来评估模型拟合，这在本练习中是不需要的。因此，此时我们已准备好生成BEAST XML文件并使用它来运行贝叶斯进化分析。要执行此操作，请从`File`菜单选择`Generate BEAST File...` 选项或者单击窗口底部的类似标记的按钮。为文件选择一个名称(例如，WNV_homogeneous.xml通过将xml扩展名添加到文件名主干)并保存文件。为方便起见，您可以打开BEAUti窗口，以便可以更改值并在必要时重新生成BEAST文件。



### 运行BEAST

创建BEAST XML文件后，可以使用BEAST执行分析本身。运行BEAST的准确说明取决于您使用的计算机，但在大多数情况下，将出现一个对话框，您可以在其中选择XML文件:

{% include image.html file="18_BEASTgui.png" prefix=root_url  max-width="50%" align="center" caption="" %}

点击‘Choose File’按钮，选择刚刚创建的XML文件，然后点击‘Run’。安装BEAGLE库(https://github.com/beagle-dev/beagle-lib)后，可以将其与BEAST结合使用以加快计算速度。如果未安装，请取消选择BEAGLE库的使用。如果正在使用BEAST的命令行版本，则在BEAST可执行文件的名称后面给出XML文件的名称。然后将执行分析，其中包含有关正在写入屏幕的运行进度的详细信息。完成后，将在与XML文件相同的位置创建log文件和树文件。

### 分析BEAST输出文件

为了分析运行BEAST的结果，我们将使用Tracer程序。运行Tracer的确切说明因您使用的计算机而异。双击Tracer图标; 一旦运行，Tracer将看起来类似，无论它运行在哪个计算机系统上。

从`File`菜单选择`Import Trace File...`选项 。果可用，请选择您在上一节中创建的log文件(WNV_homogeneous.log)。或者，将日志文件拖放到Tracer窗口中。该文件将加载，您将看到一个类似于下面的窗口。请记住，MCMC是一种随机算法，因此实际数字不会完全相同。

{% include image.html file="19_tracerShort.png" prefix=root_url caption="" %}

左侧是加载的日志文件的名称及其包含的追踪。后面有痕迹(这是树可能性和先验概率的乘积的对数)，以及连续参数。选择左侧的迹线会根据所选的选项卡在右侧显示此迹线的分析。首次打开时，会选择'posterior'追踪以及 这些追踪的各种统计信息将会显示在`Estimates`标签。

在窗口的右上角是一个计算所选跟踪统计信息的表。统计数据及其含义如下表所示。

* 平均值 - 样本的平均值(不包括老化的)。
* Stdev - 平均值的标准误差。这考虑了有效的样本大小，因此小的ESS会产生很大的标准误差。
* 中位数 - 样本的中值(不包括老化的)。
* 95% HPD 下限 - 最高后验概率密度(HPD)间隔的下限。HPD是包含95％采样值的最短间隔。
* 95% HPD 上限 - 最高后验概率密度(HPD)间隔的上限。
* 自相关时间(ACT) - MCMC链中的平均状态数，两个样本必须分开才能使它们不相关(如后验概率的独立样本)。ACT是根据迹线中的样本估算的(不包括老化的)。
* 有效样本大小(ESS) - 有效样本大小(ESS)是迹线等价的独立样本数。这被计算为链长(不包括老化的)除以ACT。

请注意，所有迹线的有效样本大小(ESSs)都很小(小于100的ESS在Tracer中以红色突出显示，>100但<200的为黄色)。这个结果并不好。较低的ESS意味着您的样本仅相当于少数不相关的样本追踪，这可能不能很好地代表后验分布。在窗口的右下方是样品的频率图，假设低ESS非常粗糙，这是预期的。检查许多连续参数的追踪迹线表明链仍然处于老化阶段(后验值在整个链中仍在增加)，并且此运行不允许我们总结参数的边际后验概率分布。

对这种情况的简单回应是我们需要更长链长。

本练习中，运行更长的输出文件为: <!-- [here](files/WNVlongRuns.zip).-->
<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 运行更长WNV连续系统地理学的log和树文件<a href="{{ root_url }}files/WNVlongRuns.zip">可从此处下载</a>.</div>

导入运行更长同质模型的log文件，并确保MCMC运行已达到稳定:图中没有明显的趋势表明MCMC尚未收敛，并且没有在迹线中表明混合不良的大规模波动。

由于我们对后验概率的结果感到满意，我们现在可以转到我们感兴趣的统计数据:扩散率。选择左侧表格的'location.diffusionRate'。这显示了该统计量的后验概率密度的图，该统计量通过测量覆盖每个分支覆的距离(基于在每个分支的父节点和后代节点处推断的空间坐标)，来跟踪扩散速率，即完整树的距离的和除以树的长度。它使用两个坐标之间的圆距离，这将提供以km/yr为单位的估计值。您应该在`Estimates`选项卡中看到与此类似的图:

{% include image.html file="20_dispersalRate.png" prefix=root_url caption="" %}

WNV以何种速度入侵北美?

### 总结树

我们已经知道如何使用Tracer诊断我们的MCMC运行并生成我们模型参数的边际后验分布的估计。然而，BEAST还会与模型的其他参数同时对树进行采样(系统发育或系谱)。这些被单独写入一个名为'trees'的文件中。此文件是标准的NEXUS格式文件。因此，它可以很容易地加载到其他软件中，以检查它包含的树。一种可能性是将树加载到诸如PAUP*之类的程序中，并以类似的方式构建一致树以总结一组自展树。在这种情况下，一致树中报告的已解析节点的支持值将是这些进化枝的后验概率

但是，在本教程中，我们将使用作为BEAST包的一部分工具来汇总我们的采样树中包含的信息。该工具称为TreeAnnotator，一旦运行，您将看到一个如下所示的窗口。

{% include image.html file="21_treeAnnotator.png" max-width="50%" align="center" prefix=root_url caption="" %}

* TreeAnnotator采用单个'target'树，并使用来自整个树样本的汇总信息对其进行注释。总结信息包括平均节点年龄(以及HPD间隔)，后验概率支持值和每个分支上的平均进化速率(对于宽松分子钟模型，其可以变化)。程序为在指定的'target'树中观察到的每个节点或分支计算这些值。它有以下选项:

* Burnin-是MCMC运行链长的步数，Burnin(作为状态树)，或者树的数量，Burnin(作为树)，也就是应该从总结中排除的量。对于上面的示例，使用200,000,000步的链，10％的burnin对应20,000,000步。或者，对每50,000个步骤进行采样会产生4000个树，并且要获得相同的10％burnin，则需要将树的数量设置为400。

* 后验概率限制-这是节点的最小后验概率，以便TreeAnnotator存储带注释的信息。每个节点的默认值为0.0，因此无论其支持什么，每个节点都将汇总信息。确保此值保持为0.0，因为每个节点都需要位置注释以进一步可视化。

* 目标树类型-有3个选项`Maximum clade credibility tree`或者`User target tree`。对于后一个选项，可以将NEXUS树文件指定为目标树文件，如下所示。选择第一个选项时，TreeAnnotator将检查输入树文件中的每个树，并选择具有其所有节点的后验概率的最高乘积的树。

* 节点高度-此选项指定应将哪个节点高度T(times)用于输出树。.如果指定`Keep target heights`，节点高度将与目标树相同。节点高度也可以概括为树样本的平均值或中位值。节点的平均或中值高度实际上可能高于其父节点的平均或中值高度(因为与大量其他采样的树相比，MCC树中的特定祖先-后代关系可能会不同)。这将导致人为负分支长度，但可以通过`Common Ancestor heights`选项避免。让我们使用默认的中位数高度作为总结树。

* 目标树文件-如果`User target tree`选项被选择，接下来您可以使用`Choose File...` 来选择一个包含目标树的NEXUS文件。

输入树文件-使用`Choose File...` 按钮来选择输入树文件。该文件是BEAST生成的树文件

*输出文件-选择一个输出树文件的名字(比如，'WNV_homogeneous.tre')。

一旦选择以上所有选项好，点击`Run`按钮。TreeAnnotator将会分析输入树文件并将总结树写入您所指定的文件。该树为标准的NEXUS格式树，因此可以载入任何支持其格式的建树程序包。然而，它也包含额外的仅可以在FigTree中展示的信息。

### 查看注释树

运行FigTree，并从`File`菜单选择`Open...` 命令。选择您在前部分用TreeAnnotator 生成的树文件。树将会在FigTree窗口展示出。窗口左侧是控制树如何展示的选项和设置。在本例中，我们希望显示树中存在的每个进化枝的后验概率以及每个节点的年龄估计。为此，需要更改一些设置。

首先，通过`Tree`菜单下的增加节点顺序重新排序节点顺序。单击左侧控制面板中的分支标签，然后单击左侧的箭头打开该部分。现在在`Display`弹出菜单下选择'posterior'。后验概率将不会实际显示出，直到勾选`Branch Labels`标题旁边的复选框之前。

{% include image.html file="22_FigTree.png" prefix=root_url caption="" %}

我们现在想要在树上以条形形状来表示每个节点的日期估计的不确定性。TreeAnnotator将此信息以95％最高后验密度(HPD)间隔的形式展示在树文件中(参见上面的HPD描述)。选择控制面板中的`Node Bars`并打开此部分;选择`height_95%_HPD`来展示节点高度的95% HPDs。我们还可以为此进化史绘制时间刻度轴(选择`Scale Axis`并取消`Scale bar`的选择)。为了适当的缩放，打开控制面板的`Time Scale`部分，设置`Offset`为2007.63(最近采样日期)，在`Scale Axis`下选择`Reverse Axis`。

在`Layout`面板选择复选框`Align Tip Labels`提高清晰度。

最后，打开Appearance面板，更改`Line Weight` 使用较粗的线条绘制树。在同一个面板下，更改`Colour by` 并选择'rate'。选择 `Legend`选项和‘rate’ 作为属性以显示分支着色的图例。无论如何，这些选项实际上都不会改变树的拓扑或分支长度，因此可以随意浏览选项和设置。您也可以保存树，这将保存您的所有设置，以便当您再次将其加载到FigTree时，它将完全按照您选择的方式显示。

## 评估扩散速率的变化

为了研究在整个狂犬病流行期间传播速率是否大致恒定，我们可以拟合允许扩散过程中分支特定速率变化的模型(称为‘relaxed random walks’，RRWs)并测试与标准均匀布朗扩散过程相比这些是否导致模型拟合改善。为此，我们还在'Cauchy RRW'分析了相同的数据。这可以在'location'替代模型下的`Sites`标签设置:

{% include image.html file="23_cauchy.png" prefix=root_url caption="" %}

对于不同的模型，我们可以使用路径采样(PS)和踏脚石采样来估计边际可能性(MLE)，这些最近已在BEAST中实施(Baele at al., 2012, 2013)。通常，在进行标准MCMC分析之后执行PS/SS模型选择。然后，PS和SS采样可以从MCMC分析停止的地方开始(因此您应该经运行MCMC分析足够长的时间，以便在尝试进行PS/SS分析之前它已经达到后验概率收敛)，从而消除了对PS和SS的需求，以至于后验概率达到聚合。为了设置这样的分析，我们可以返回到BEAUti并从`Perform marginal likelihood estimation (MLE)`菜单选择'path sampling (PS) / stepping-stone sampling (SS)'，该选项在标准MCMC链完成后执行附加分析。在MCMC面板中，单击`settings`对PS/SS进行设置。

{% include image.html file="23_PSSSsampling.png" prefix=root_url caption="" %}

我们需要为后验概率和先验值之间的路径设置步数(本例中为50)，MCMC链的长度(本例中为1,000,000) 用于为每一步估计特定后验概率的强度，以及对于每个MCMC抽样记录频率(本例为每1,000个)。请注意，使用这些设置，边际似然估计将花费大约完成此数据50,000,000代标准MCMC运行所需的时间。使用 Beta(\alpha,1.0)分布均匀间隔分位数定义不同后验概率的强度，其中α等于0.30，如踩踏石采样文章所述(Xie et al. 2011)，这种方法显优于路径采样文章中提出的均匀扩散(Lartillot and Philippe 2006)。注意，最近边际似然估计，如广义踏脚石采样(Fan et al., 2011; Baele et al., 2016)，已经可用，但是仍未与特征扩散模型的比较进行测试。

基于上述设置，均匀布朗分析产生的PS和SS的边际似然估计分别为-24182.20和-24176.83。对于Cauchy RRW，相同的估计中我们得到-24074.30和-24072.10。是否存在扩散率异质性的重要证据？这对平均扩散率估计有很大影响吗？
可以使用颜色注释在MCC树中描绘谱系之间的速率变化。总结Cauchy RRW模型的MCC树，在FigTree的`Appearance`下，为颜色设置为'location.rate'。



### 使用SpreaD3可视化贝叶斯系统地理学重建

SpreaD3,即使用数据驱动文档(D3)对进化动力学进行空间系统发育重建，是一种可视化贝叶斯系统地理学分析输出的软件，构成了一个用户友好的应用程序，用于分析和可视化由贝叶斯序列和特征进化过程推断产生的病原体动力学重建。SpreaD3允许在自定义地图上进行可视化，并生成HTML页面，以便在现代浏览器如Firefox，Safari以及Chrome中显示。SpreaD3的功能之一与先前进行的连续系统地理分析有关，包括可视化位置注释的MCC树。此特定步骤的详细教程在[此处](https://rega.kuleuven.be/cev/ecv/software/SpreaD3_tutorial#sectionFourThree).
<!--
我们还提供了PDF版本的SpreaD3教程。
-->
可在以下快速操作方法摘要中找到简要说明.
比较同质模型和RRW模型的动画版本:您注意到有什么区别吗?

### 用dN/dS估计特定位点的量化选择

作为WNV分析的一部分，我们执行了'Renaissance Counting'程序以获得特定位点的dN/dS估计。这些估计值记录在 [WNV_homogeneous.dNdS.log](files/WNVlongRuns/WNV_homogeneous.dNdS.log) 文件并总结于 [WNV_homogeneous.dNdS.summary.txt](files/WNVlongRuns/WNV_homogeneous.dNdS.summary.txt)。在本概述中，列出每个密码子位点的平均dN/dS估计值和可信区间(CPD Low  and CPD Up)。此外，根据这三种类别的估计提供分类:负选择由‘-’代表，中性选择由‘0’代表，正向选择由‘+’代表。最终，当认为位点显著的负向或正向选择时(基于dN/dS估计的可信区间为1的频率)，这用‘*’表示。通常，平均dN / dS估计值较低，其值的变化相当离散。这是由于该数据中取代数的稀疏性，尤其是非同义替换。因此，负面选择是WNV历史中主导的进化力量。然而，估计有五个位点为正向选择(并不强)(449，1367，1991，2209，2518，2522和2842)。是否检测到这些位点中的任何一个在[该]研究中也是正向选择的(http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3667762/)?


## 简要操作概述

### 运行BEAUti

* 通过从`File`菜单选择`Import Data...` 选项导入NEXUS格式的比对文件。 选择文件'WNV.fas'。

* 在`Tips`标签，选择`Use tip dates`选项，以及点击`Parse Dates`按钮。保持默认的按其顺序定义，设置顺序为最后，并且通过数字解析:为每个添加以下值: 1900.0， ...除非小于16.0，... 在这种情况下添加: 2000.0。选择所有的分类单元(按下cmd键)，在 `Tips` 窗口，采样日期仅以年为已知(具有.0 的十进制)，并点击 `Set Uncertainty`。输入‘1.0’作为1年的精确度。在`Tips`面板的做下侧选择`sampling uniformly from uncertainty`，并保持`Apply to taxon set: All taxa`为默认。

* 在`Traits`标签，点击`Import trait`。浏览并加载WNV\_lat\_long.txt 制表符文件。

* 载如文件后，在面板左侧选择'Lat'和'Long'特征，选择`Create partition from trait...` ，为该分区命名为'location':

* 在`Sites`标签，保持默认的HKY替代模型，碱基频率选项选择`Estimated`，‘Site Rate Heterogeneity’设置为‘None’，并将数据划分为3个分区以用于编码区位置(3个分区: 分区1，2，3)。

* 单击`Substitution model`窗口左侧的位置并保留默认的'Homogenous Brownian model'并选择`Bivariate trait represents latitude and longitude` 以及`Jitter`设置为'0.5'.

* 在`Clocks`标签，选择`Lognormal relaxed molecular clock (Uncorrelated)`模型。

* 在`Trees`标签，选择`exponential growth`模型作为人口统计树的先验值(`Coalescent: Exponential Growth`)并保持默认的随机起始树。

* 在`States`标签，通过选择`Reconstruct synonymous/non-synonymous change counts`，打开复兴计数程序。
在`Priors`标签， 设置`allMus` 先验为对数正态分布，平均值为0.0，标准差为1.5。

* 在`MCMC`标签，设置链长为100,000，取样频率为5000。将主干文件名设置为'WNV\_homogeneous'并生成beast文件 ('WNV\_homogeneous.xml')。

### 运行BEAST，并加载xml文件。

### 用Tracer分析输出文件。分析运行较长链长的输出文件。

### 用treeAnnotator总结运行较长链长的树。

### FigTree中可视化树

### 运行SpreaD3。

我们将首次总结MCC树，然后总结整个树分布中的信息。

* 在`Data`面板选择输入类型: `MCC tree with CONTINUOUS traits`，加载您的MCC树文件。

* 选择‘location1’作为`Latitute attribute`的名称，‘location2’作为`Longitude attribute`的名称。

* 设置`most recent sampling date`为2007-07-15.

* 加载美国的GeoJSON [file](../workshop_discrete_diffusion/files/gz_2010_us_040_00_500k.json)文件。

* 保持所有默认的设置，然后点击`Output`生成JSON文件。

* 在`Rendering`面板，保持D3 renderer作为默认的选项，并加载生成的JSON文件。

* 点击`Render to D3`生成HTML网页，一个浏览器窗口将自动打开。

{% include image.html file="24_Spread.png" prefix=root_url caption="" %}

这使MCC树可视化其节点位置的不确定性。为了总结基于整个树分布的位置不确定性，我们可以继续以下过程。

* 在`Data`面板中选择输入类型: `Tree distribution with CONTINUOUS traits`，加载'.trees'文件。

* 保持默认的`Time slices`(`Generated from MCC tree`)并加载MCC树。

* 设置`most recent sampling date`为2007-07-15。

* 设置`2D rate attribute`为'location.rate'。

* 保持所有的默认设置，并选择`Output`生成JSON文件。

* 转到`Merge`面板，并选择刚才生成的JSON文件。 通过右下角的`+`按钮，加载第二行。选择MCC 树的JSON文件。

* 从第一个JSON文件选择`Areas`，和`Points`，第二个JSON文件(MCC树)选择`Lines`和`GeoJSON`。

* 从`File`菜单选择`Merge...` 并保存为新的合并的JSON文件。

* 转到`Rendering`面板，保持D3 renderer作为默认的选择，并加载并加载生成的JSON文件。

* 点击`Render to D3`生成HTML网页，一个浏览器窗口将自动打开。


<!--
* 在`Data`面板中选择输入类型: `Tree distribution with CONTINUOUS traits`，加载'.trees'文件。

*选择‘location1’作为`Latitute attribute`的名称，‘location2’作为`Longitude attribute`的名称。

* 设置`most recent sampling date`为2007-07-15.

* 加载美国的GeoJSON [file](../bat_rabies_discrete_diffusion/files/gz_2010_us_040_00_500k.json)文件。

保持所有默认的设置，然后点击`Output`生成JSON文件。

* 转到`Rendering`面板，保持D3 renderer作为默认的选项，并加载生成的JSON文件。

* 点击`Render to D3`生成HTML网页，一个浏览器窗口将自动打开。

{% include image.html file="24_Spread.png" prefix=root_url caption="" %}
-->

## 总结和资源

本教程仅涉及使用BEAST进行浅层次的分析。希望能够对所有BEAST分析所共有的基本步骤提供一个大体的介绍，并为更具挑战性的研究提供基础。BEAST是一个持续的开发项目，定期添加新的模型和技术。BEAST网站提供了邮件列表的详细信息，用于公布新功能并讨论包的使用。该网站还包含一系列教程和题材，用于回答使用BEAST的特定进化问题，以及XML输入格式，常见问题和错误消息的描述。


## 参考文献

* [Lemey, P., A. Rambaut, J. J. Welch, and M. A. Suchard. 2010. Phylogeography takes a relaxed random walk in continuous space and time. Molecular biology and evolution 27:1877-1885.](https://academic.oup.com/mbe/article-lookup/doi/10.1093/molbev/msq067)

* [Pybus, O. G., M. A. Suchard, P. Lemey, F. J. Bernardin, A. Rambaut, F. W. Crawford, R. R. Gray, N. Arinaminpathy, S. L. Stramer, M. P. Busch, and E. L. Delwart. 2012. Unifying the spatial epidemiology and molecular evolution of emerging epidemics. Proceedings of the National Academy of Sciences of the United States of America 109:15066-15071.](http://www.pnas.org/content/109/37/15066.short)

* [Baele, G., P. Lemey, T. Bedford, A. Rambaut, M. A. Suchard, and A. V. Alekseyenko. 2012. Improving the accuracy of demographic and molecular clock model comparison while accommodating phylogenetic uncertainty. Molecular biology and evolution 29:2157-2167.](https://academic.oup.com/mbe/article-lookup/doi/10.1093/molbev/mss084)

* [Baele, G., W. L. Li, A. J. Drummond, M. A. Suchard, and P. Lemey. 2013. Accurate model selection of relaxed molecular clocks in bayesian phylogenetics. Molecular biology and evolution 30:239-243.](https://academic.oup.com/mbe/article-lookup/doi/10.1093/molbev/mss243)

* [Baele, G., P. Lemey, M. A. Suchard. 2016. Genealogical working distributions for Bayesian model testing with phylogenetic uncertainty. Systematic biology 65:250-264.](https://academic.oup.com/sysbio/article-lookup/doi/10.1093/sysbio/syv083)

* [Lemey, P., V. N. Minin, F. Bielejec, S. L. Kosakovsky Pond, and M. A. Suchard. 2012. A counting renaissance: Combining stochastic mapping and empirical Bayes to quickly detect amino acid sites under positive selection. Bioinformatics 28(24):3248–3256.](https://academic.oup.com/bioinformatics/article-lookup/doi/10.1093/bioinformatics/bts580)

* [Bielejec, F., G. Baele, B. Vrancken, M. A. Suchard, A. Rambaut and P. Lemey. 2016. SpreaD3: interactive visualisation of spatiotemporal history and trait evolutionary processes. Mol. Biol. Evol., 33(8): 2167–2169. doi: 10.1093/molbev/msw082](https://academic.oup.com/mbe/article-lookup/doi/10.1093/molbev/msw082)

{% include links.html %}
