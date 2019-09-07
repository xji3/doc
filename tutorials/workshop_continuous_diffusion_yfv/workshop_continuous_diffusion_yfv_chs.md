---
title: 连续空间中的系统地理学扩散- 以YFV为例
keywords: phylogeography, YFV, tutorial
last_updated: June 12, 2019
tags: [tutorial]
summary: '本教程基于一组在不同时间点分离的病毒基因组序列，逐步讲解了在巴西流行的重建黄热病病毒(YFV)空间动力学重建(Faria et al. 2018)。YFV每年在南美洲和非洲造成29,000到60,000人的死亡，是热带地区最严重的蚊媒传播病毒。YFV主要通过深林循环传播，在传播过程中非人灵长类动物被蚊子携带者感染，但也可以通过城市循环传播，在该传播过程中人类通常被以人为食的伊蚊感染。最近，巴西经历了几十年来最大的黄热病爆发。为了表征最近爆发的YFV的传播历史和动力学，Faria等人(2018)的研究分析了在2017年1月到4月期间收集的65个YFV基因组组成的数据集。为此，他们应用连续扩散(Lemey et al. 2010, Pybus et al. 2012)来估计病毒在连续空间中的祖先位置。在本教程中，我们详细介绍了如何设置该连续系统地理学分析以及如何分析/可视化输出'
sidebar: beast_sidebar
permalink: workshop_continuous_diffusion_yfv_chs.html
folder: beast
---

{% capture root_url %}{{ site.tutorials_root_url }}/workshop_continuous_diffusion_yfv/{% endcapture %}

## 前言

第一步是将比对好的fasta格式的文件转换为BEAST XML输入文件。该操作通过程序BEAUti(代表着贝叶斯进化分析实用程序)来完成。这是一个用户友好的程序，用于设置MCMC分析的进化模型和选项。第二步是使用包含数据，模型和设置的输入文件实际运行BEAST。最后一步是探索BEAST的输出，以便诊断问题并总结结果。

要执行本教程，您需要您的计算机系统兼容的三个软件包(这三个软件包均适用于Mac OS X，Windows和Linux/UNIX 操作系统)::

{% include beast_callout.md %}

{% include beagle_callout.md %}

{% include tracer_callout.md %}

{% include figtree_callout.md %}

{% include spread3_callout.md %}

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 本教程所需要的所有文件 <a href="{{ root_url }}files/continuousTutorialFiles.zip"> 可以从此处下载</a>.
如果您已经下载了压缩文件夹，没有必要再下载教程链接的其它的文件/文件夹。
</div>

### 运行 BEAUti

BEAUti程序是一个用户友好的程序，用于设置BEAST的模型参数。双击其图标运行BEAUti。

#### 加载序列数据文件

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i>本教程的数据文件名称为'<samp>YFV_sequences.fasta</samp>'和<a href="{{ root_url }}files/YFV_sequences.fasta">可以从此处下载</a>.</div>

载入比对文件，仅仅需要从文件菜单选择`Import Data...` 选项，浏览到'YFV\_sequences.fasta'文件并加载它。该文件为比对过的65个YFV基因组序列，长度为10236个核苷酸。一旦加载，序列数据将会在`Partitions`列出，如图所示:

{% include image.html file="YFV_screenshot_01_partitions_1.png" prefix=root_url caption="" %}

#### 指定采样日期信息

默认情况下，假定所有分类单元的日期都为零(即假设序列同时采样)。在本教程中，YFV序列在2017年间不同时间采集。为了设置该日期，通过主窗口顶部的选项卡切换到`Tips`面板。

选择标记为`Use tip dates`的对话框。以年为单位的实际采样时间以每个分类单元的名称编码，我们可以简单地编辑‘Date’列中的值以反映这些值。但是，如果分类单名称包含校准信息，则在BEAUti中指定序列日期的便捷方法是使用`Data`面板顶端左侧的`Parse Dates`按钮。单击此按钮将显示一个对话框:

{% include image.html file="YFV_screenshot_02_tips_1.png" prefix=root_url  max-width="25%" align="center" caption="" %}

此操作尝试从分类单名称中包含的信息中猜测日期。它的工作原理是尝试在每个名称中找到一个数字字段。如果分类单元名称包含多个数字字段，则可以指定如何找到与采样日期对应的数字字段。您可以(1)指定日期字段的顺序(如，第一个，最后一个或其间的某个位置)或者(2)指定前缀(紧接在每个名称中的日期字段之前的某些字符)和该字段所在顺序，或者(3)定义正则表达式(REGEX)。

解析数字时，您可以要求BEAUti为每个猜测日期添加固定值。例如，可以添加`1900`将日期从2位数转换为4位数。在分类单元名称中任何给定的`00`都将称为`1900`。但是，如果`00`或者`01`，等表示在2000年，2001年采样的序列，`2000` 需要被添加。这将通过选择`unless less than: ..`和`..in which case add:..`选项添加例如2000到任何小于10的日期来实现。在本教程中，这些操作不是必需的，因为日期在序列名称的末尾完全指定。还有一个选项可以解析日历日期，以及解析具有各种精度的日历日期。

对于YFV序列，日期值必须通过指定`dd-MM-yyyy`格式将日期值解析为日历日期。对于其它选项，选择`Defined by a prefix and its order`，从顺序的下拉菜单中选择`last`，设置`Prefix`为符号`|`并点击`OK`。日期将显示在主窗口的对应列中。

{% include image.html file="YFV_screenshot_03_tips_2.png" prefix=root_url caption="" %}

您现在可以检查这些并根据需要手动编辑它们。在窗口的顶部，您可以设置日期(年，月，日)，以及它们是否相对于过去的时间点(如2005年)或者从现在开始的时间向后指定(如放射性碳年龄)。

`Height`列列出了相对于时间0的提示年龄(本例子为2007.63)。`Uncertainty` 列允许指定采样时间的精确度。这在我们的情况下是有用的，因为一些采样日期在确切的日期是已知的，然而BEAST允许在不确定性上整合不太精确的采样日期(如，仅知道采样月份或年份)。

#### 指定特征信息

接下来要做的是单击主窗口顶部的选项卡`Traits`。特征可以是特定分类群固有的任何特征，例如，地理位置或宿主物种。该步骤将基于每个序列的特征规范将纬度和经度分配为每个分类群的双变量地理位置。

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 以制表符分隔的文件，将每个分类与经度和纬度相关联'<samp>YFV_coordinates.txt</samp>' <a href="{{ root_url }}文件/YFV_coordinates.txt">可以从此处下载</a>.</div>

要将序列与特征相关联，我们需要在`Traits`选项卡下导入新特征(点击`Import Traits...`)。这将打开一个新窗口，允许导入具有特征的文件。浏览并打开'YFV\_coordinates.txt'制表符分隔文件，其中包含以下内容:

	traits	lat	long
	NC|ES504|NHPrimate|DomingosMartins|EspiritoSanto|20-02-2017	-20.360797	-40.65981
	NC|ES505|NHPrimate|DomingosMartins|EspiritoSanto|22-02-2017	-20.360797	-40.65981
	FioRJ|Library9YIBRA_M218_2176|Primate|Bahia|10-03-2017	-15.037801	-41.934681
	FioRJ|460|Human|MinasGerais_NovoCruzeiro|30-01-2017	-17.432895	-41.998972
	FioRJ|1818|Human|EspiritoSanto_Cariacia|10-03-2017	-20.284466	-40.431258
	FioRJ|2115|Monkey|EspiritoSanto_Cariacia|09-03-2017	-20.284466	-40.431258
	FioRJ|3919|Human|EspiritoSanto_DomingosMartins|10-04-2017	-20.360797	-40.65981
	FioRJ|438|Monkey|Espirito_Santo_DomingosMartins|31-01-2017	-20.360797	-40.65981
	FioRJ|465|Human|MinasGerais_Itambacuri|30-01-2017	-18.034092	-41.683337
	FioRJ|480|Human|MinasGerais_TeofiloOtoni|28-01-2017	-17.860009	-41.509104
	FioRJ|532|Monkey|MinasGerais_CoronelMurta|13-01-2017	-16.588013	-42.196772
	FioRJ|1536|Human|EspiritoSanto_Vitoria|22-02-2017	-20.297618	-40.295777
		...
	MF170971|Monkey|MinasGerais_SaoRoqueDeMinas|NA|30-01-2017	-20.177911	-46.439305

点击`OK`后，在左侧窗口中选择'lat'和'long'特征，并指定`create partition from trait..`。在弹出的窗口中，输入此分区的名称，例如'location':

{% include image.html file="YFV_screenshot_05_traits_2.png" prefix=root_url caption="" %}

这个带有2个'Sites'且具有连续'Data Type'的新'location'将显示在`Partitions`标签下:

{% include image.html file="YFV_screenshot_06_partitions_2.png" prefix=root_url caption="" %}

#### 设置序列和特征进化模型

接下来要做的是点击主窗口顶部的`Sites`选项卡。这将揭示BEAST的进化模型设置。哪些选项出现取决于数据是核苷酸，氨基酸还是性状。

本教程假设您熟悉可用的进化模型，但是在BEAUti中选择模型还有几点需要注意:

选择`Partition into codon positions`选项假设数据为比对过的密码子。然后，该选项将估计每个密码子位置独立的替代率，或者1+2vs3的替代率，具体取决于设置。选择`Unlink substitution model across codon positions`将指定BEAST为每个密码子位置估计独立的的转换-颠换比率或一般时间可逆速率矩阵。选择`Unlink rate heterogeneity model across codon positions`将指定BEAST为每个密码子位置估计一组速率异质性参数(伽马形状参数和/或不变位点的比例)。

对于本教程中的核苷酸替换模型，保留默认的HKY替换模型，将碱基频率保持默认为`Estimated`，`Site Rate Heterogeneity`为`Gamma`，并保持默认的`Partition into codon positions`为`Off`。

{% include image.html file="YFV_screenshot_07_sites_1.png" prefix=root_url caption="" %}

接下来点击`Substitution model'窗口的'location'，选择`Cauchy RRW model`，并且选择`Bivariate trait represents latitude and longitude`。此选项生成特定于双变量空间特征的扩散统计(经度和纬度按此排序)。并且选择`add random jitter to tips`，该选项将从特定抖动窗口大小随机均匀绘制的噪声添加到重复(位置)特征。将`jitter window size`设置为0.01。当并非所有序列都与唯一位置相关时，噪声避免了布朗扩散模型的不良性能。

{% include image.html file="YFV_screenshot_07_sites_2.png" prefix=root_url caption="" %}

#### 设置‘molecular clock’模型

`Clocks`面板中的‘Molecular Clock Model’选项允许我们在严格和宽松(不相关的对数正态或不相关指数分布)分子钟间选择。我们将选择对数正态宽松分子钟(Uncorrelated)模型执行我们的运行:

{% include image.html file="YFV_screenshot_09_clocks.png" prefix=root_url caption="" %}

现在切换到`Trees`面板。

#### 设置树先验

此面板包含有关树的设置。首先，将起始树指定为‘randomly generated’。此处的另一个主要设置是指定‘Tree prior’，该选项描述了根据聚结模型预计种群大小如何随时间变化。默认的树先验设置为恒定大小的聚合先验。再此，我们将选择灵活的的skygrid聚合模型作为人口统计树先验 (`Coalescent: Bayesian SkyGrid`)，设置为36个网格点(`Number of parameters`)，`A time at last transition point`设置为`0.6948424`。通过这样做，网格点实际上接近于系统发育持续时间跨越的流行病学周数(Faria et al. 2018)。

{% include image.html file="YFV_screenshot_10_trees.png" prefix=root_url caption="" %}

#### 祖先状态重建

在`States`面板，我们将通过选择，检查`location`分区设置为`Reconstruct states at all ancestors`(默认):

{% include image.html file="YFV_screenshot_11_states.png" prefix=root_url caption="" %}

#### 设置先验值

查看`Priors`标签下的先验设置。该面板有一个表格，显示当前所选模型的每个参数以及每个参数的先验分布。<未明确指定的先验值将显示为红色，而不适当的先验(因此导致不正确的后验和不正确的边际似然值)显示为黄色(如，allMus)。单击此参数的先验，将出现先验的选择窗口。密码子特定位置的相对比率(CP1.mu, CP2.mu and CP3.mu)，其被限制平均值为1，仍然需要合适的先验分布。我们在这里指定对数正态分布，其参数log(平均值)为0，log(标准差)为1.5。请注意，单击确定确认此设置后，先前的设置变为黑色。
>
在本例中，我们可以保留默认的先验:

{% include image.html file="YFV_screenshot_12_priors.png" prefix=root_url caption="" %}

#### 设置采样模块

模型中的每个参数都有一个或多个“operators”(他们在其它的MCMC软件包，如：MrBayes和LAMARC中分别称为移动，建议或转换内核)。采样模块指定参数在MCMC运行时如何更改。BEAUti中的`Operators`标签有一个表格，列出了参数，参数的采样模块，运算符和调整设置:

{% include image.html file="YFV_screenshot_13_operators.png" prefix=root_url caption="" %}

第一列是参数名称。这些将被称为类似于kappa的名称，kappa表示HKY模型的kappa参数(转换-巅换偏差)。下一列具有作用于每个参数的运算符类型。例如缩放采样模块按比例向上或向下缩放参数，随机游走采样模块向参数添加或减去一定量，统一操作模块只是在一个范围内统一选取一个新值。与树有关或树节点的分歧时间有关的参数，具有特殊的采样模块。从BEAST v1.10开始，可以使用不同的选项来探索树空间。在本教程中，我们将使用‘classic operator mix’，它由一组树转换内核组成，这些内核会建议对树进行更改。同时也有一个选项来固定树的拓扑结构，以及‘new experimental mix’选项，该选项目前处于开发中，其目的是提高大型系统发育树的混合度。

下一列为`Tuning`，为采样模块提供调整设置。某些采样模块没有任何调整设置，因此在此列下为n/a。调整参数将确定每个采样模块将进行的移动有多大，这将影响MCMC接受该更改的频率，这反过来又会影响分析的效率。对于多数采样模块(如随机游走和子树滑动采样模块)，较大的调整参数意味着较大的移动。然而，对于比例采样模块，调整参数值接近0.0意味着更大的移动。在窗口的顶部是一个名为`Auto Optimize`的选项，选择后，当MCMC运行时，将自动调整调整设置以尝试实现最高效率。在采样模块的列表运行结束时，它们的性能和这些调整设置的最终值将写入标准输出。

下一列‘Weight’，指定每个采样模块相对于其他采样模块的应用频率。有些参数往往非常有效地采样-如kappa参数-这些参数可以使其采样模块降低权重，以便它们不会被经常更改。我们将从使用此分析的默认设置开始。从BEAST v1.8.4开始，可以使用不同的选项来探索树空间。在本教程中，我们将使用‘classic operator mix’，它由一组树转换内核组成，这些内核会建议对树进行更改 同时也有一个选项来固定树的拓扑结构，以及‘new experimental mix’选项，该选项目前处于开发中，其目的是提高大型系统发育树的混合度。在本教程分析中我们可以保留默认的采样模块设置。

#### 设置MCMC选项

BEAUtid的`MCMC` 提供了控制MCMC链的设置。首先为‘Length of chain’，即MCMC在完成之前在链中所运行的步骤数。运行多长时间取决于数据集的大小，模型的复杂性和所需答案的精确度。默认值10,000,000完全是任意的，应根据数据集的大小进行调整。稍后我们将看到如何使用Tracer分析生成的log文件，以检查特定链长是否足够。

接下来的两个选项指定当前参数值应在屏幕上显示并记录在log文件中的频率。屏幕输出仅用于监视程序的进度，因此可以设置为任何值(尽管设置得太小，屏幕上显示的信息量将减慢程序的速度)。对于log文件，应该相对于链的总长度设置该值。过于频繁的采样将导致非常大的文件，在估计的精确度方面几乎没有额外的好处。样本太少，log件中不包含有关参数分布的大量信息。您可能希望存储不超过10,000个样本，因此应将其设置为链长的1/10,000。

{% include image.html file="YFV_screenshot_14_MCMC.png" prefix=root_url caption="" %}

对于本数据集，我们最初将链长设置为500,000，因为这将在大多数现代计算机上相当快地运行。因为执行的复兴计数过程时对于大型编码序列比对而言每次执行状态都将被log，这是相当耗时间的，所以我们将参数log设置为50,000，并且每50,000个状态显示于屏幕。

下一个选项允许用户设置文件主干名称; 将其设置为‘WNV_homogeneous’(YFV\_RRW\_cauchy'('RRW\_RRW\_cauchy' for 'relaxed random walk Cauchy diffusion model')。接下来的两个选项提供参数和树的log文件。这些将根据文件主干名称设置。您还可以将采样模块分析记录到文件中。还可以选择仅从先验分布采样的选项，这可用于评估从数据中提取信息时我们的后验估计的差异。在这里，我们不会选择此选项，而是分析实际数据。

最后，可以选择执行边际似然估计来评估模型拟合，这在本练习中是不需要的。因此，此时我们已准备好生成BEAST XML文件并使用它来运行贝叶斯进化分析。要执行此操作，请从`File`菜单选择`Generate BEAST File...` 选项或者单击窗口底部的类似标记的按钮。为文件选择一个名称(例如，'YFV\_RRW\_cauchy.xml'通过将xml扩展名添加到文件名主干)并保存文件。为方便起见，您可以打开BEAUti窗口，以便可以更改值并在必要时重新生成BEAST文件。

### 运行BEAST

创建BEAST XML文件后，可以使用BEAST执行分析本身。运行BEAST的准确说明取决于您使用的计算机，但在大多数情况下，将出现一个对话框，您可以在其中选择XML文件:

{% include image.html file="YFV_screenshot_15_BEAST.png" prefix=root_url  max-width="50%" align="center" caption="" %}

点击‘Choose File’按钮，选择刚刚创建的XML文件，然后点击‘Run’。安装BEAGLE库(https://github.com/beagle-dev/beagle-lib)后，可以将其与BEAST结合使用以加快计算速度。如果未安装，请取消选择BEAGLE库的使用。如果正在使用BEAST的命令行版本，则在BEAST可执行文件的名称后面给出XML文件的名称。然后将执行分析，其中包含有关正在写入屏幕的运行进度的详细信息。完成后，将在与XML文件相同的位置创建log文件和树文件。

### 分析BEAST输出文件

为了分析运行BEAST的结果，我们将使用Tracer程序。运行Tracer的确切说明因您使用的计算机而异。双击Tracer图标； 一旦运行，Tracer将看起来类似，无论它运行在哪个计算机系统上。

从`File`菜单选择`Import Trace File...`选项 。果可用，请选择您在上一节中创建的log文件('YFV\_RRW\_cauchy.log')。或者，将日志文件拖放到Tracer窗口中。该文件将加载，您将看到一个类似于下面的窗口。请记住，MCMC是一种随机算法，因此实际数字不会完全相同。

{% include image.html file="YFV_screenshot_16_Tracer_1.png" prefix=root_url caption="" %}

左侧是加载的日志文件的名称及其包含的追踪。选择左侧的迹线会根据所选的选项卡在右侧显示此迹线的分析。首次打开时，'age(root)'会被选择以及这些追踪的各种统计信息将会显示在`Estimates`标签。

在窗口的右上角是一个计算所选跟踪统计信息的表。统计数据及其含义如下表所示。

* 平均值 - 样本的平均值(不包括老化的)。
* Stdev - 平均值的标准误差。这考虑了有效的样本大小，因此小的ESS会产生很大的标准误差。
* 中位数 - 样本的中值(不包括老化的)。
* 95% HPD 下限 - 最高后验概率密度(HPD)间隔的下限。HPD是包含95％采样值的最短间隔。
* 95% HPD 上限 - 最高后验概率密度(HPD)间隔的上限。
* 自相关时间(ACT) - MCMC链中的平均状态数，两个样本必须分开才能使它们不相关(如后验概率的独立样本)。ACT是根据迹线中的样本估算的(不包括老化的)。
* 有效样本大小(ESS) - 有效样本大小(ESS)是迹线等价的独立样本数。这被计算为链长(不包括老化的)除以ACT。

请注意，所有迹线的有效样本大小(ESSs)都很小(小于100的ESS在Tracer中以红色突出显示，>100但<200的为黄色)。这个结果并不好。较低的ESS意味着您的样本仅相当于少数不相关的样本追踪，这可能不能很好地代表后验分布。对这种情况最好的回应是运行更长链长。在此处，图中并没有明显的趋势表明MCMC没有达到收敛，并且没有在迹线中表明混合不良的大规模波动。

### 总结树
我们已经知道如何使用Tracer诊断我们的MCMC运行并生成我们模型参数的边际后验分布的估计。然而，BEAST还会与模型的其他参数同时对树进行采样(系统发育或系谱)。这些被单独写入一个名为'trees'的文件中。此文件是标准的NEXUS格式文件。因此，它可以很容易地加载到其他软件中，以检查它包含的树。一种可能性是将树加载到诸如PAUP*之类的程序中，并以类似的方式构建一致树以总结一组自展树。在这种情况下，一致树中报告的已解析节点的支持值将是这些进化枝的后验概率。

但是，在本教程中，我们将使用作为BEAST包的一部分工具来汇总我们的采样树中包含的信息。该工具称为TreeAnnotator，一旦运行，您将看到一个如下所示的窗口。

{% include image.html file="YFV_screenshot_19_TreeAnnotator.png" max-width="50%" align="center" prefix=root_url caption="" %}

TreeAnnotator采用单个'target'树，并使用来自整个树样本的汇总信息对其进行注释。总结信息包括平均节点年龄(以及HPD间隔)，后验概率支持值和每个分支上的平均进化速率(对于宽松分子钟模型，其可以变化)。程序为在指定的'target'树中观察到的每个节点或分支计算这些值。它有以下选项:

* Burnin-是MCMC运行链长的步数，Burnin(作为状态树)，或者树的数量，Burnin(作为树)，也就是应该从总结中排除的量。

* 后验概率限制-这是节点的最小后验概率，以便TreeAnnotator存储带注释的信息。每个节点的默认值为0.0，因此无论其支持什么，每个节点都将汇总信息。确保此值保持为0.0，因为每个节点都需要位置注释以进一步可视化。

* 目标树类型-有3个选项`Maximum clade credibility tree`或者`User target tree`。对于后一个选项，可以将NEXUS树文件指定为目标树文件，如下所示，选择第一个选项时，TreeAnnotator将检查输入树文件中的每个树，并选择具有其所有节点的后验概率的最高乘积的树。

* 节点高度-此选项指定应将哪个节点高度T(times)用于输出树。如果指定`Keep target heights`，节点高度将与目标树相同。节点高度也可以概括为树样本的平均值或中位值。节点的平均或中值高度实际上可能高于其父节点的平均或中值高度(因为与大量其他采样的树相比，MCC树中的特定祖先-后代关系可能会不同)。这将导致人为负分支长度，但可以通过`Common Ancestor heights`选项避免。让我们使用默认的中位数高度作为总结树。

* 目标树文件-如果`User target tree`选项被选择，接下来您可以使用`Choose File...` 来选择一个包含目标树的NEXUS文件。

输入树文件-使用`Choose File...` 按钮来选择输入树文件。该文件是BEAST生成的树文件。

* 输出文件-选择一个输出树文件的名字(比如，'YFV\_RRW\_cauchy\_MCC.tree')。

一旦选择以上所有选项好，点击`Run`按钮。TreeAnnotator将会分析输入树文件并将总结树写入您所指定的文件。该树为标准的NEXUS格式树，因此可以载入任何支持其格式的建树程序包。然而，它也包含额外的仅可以在FigTree中展示的信息。

### 查看注释树

运行FigTree，并从`File`菜单选择`Open...`命令。选择您在前部分用TreeAnnotator 生成的树文件。树将会在FigTree窗口展示出。窗口左侧是控制树如何展示的选项和设置。

{% include image.html file="YFV_screenshot_20_FigTree.png" prefix=root_url caption="" %}

在`Layout`面板选择复选框`Align Tip Labels`提高清晰度。我们也可以为该进化史绘制时间尺度轴(选择`Scale Axis`并取消`Scale bar`的选择)。

### 使用SpreaD3可视化贝叶斯系统地理学重建

SpreaD3，即使用数据驱动文档(D3)对进化动力学进行空间系统发育重建，是一种可视化贝叶斯系统地理学分析输出的软件，是一个用户友好的应用程序，用于分析和可视化由贝叶斯序列和特征进化过程推断产生的病原体动力学重建。SpreaD3允许在图上进行可视化，并生成HTML页面，以便在现代浏览器如Firefox，Safari以及Chrome中显示。SpreaD3的功能之一与先前进行的连续系统地理分析有关，包括可视化位置注释的MCC树。此特定步骤的详细教程在[此处](https://rega.kuleuven.be/cev/ecv/software/SpreaD3_tutorial#sectionFourThree)。

可在以下快速操作方法摘要中找到简要说明.
比较同质模型和RRW模型的动画版本:您注意到有什么区别吗?

## 简要操作概述

### 运行BEAUti

* 通过从`File`菜单选择`Import Data...` 选项导入NEXUS格式的比对文件。 选择文件YFV\_sequences.fasta'。

* 在`Tips`标签，选择`Use tip dates`选项，以及点击`Parse Dates`按钮。在新的窗口选择`Defined by a prefix and its order`，顺序为`last`。最后，设置`Prefix`为符号`|`并点击`OK`。数据将会出现在主窗口的合适列表。

* 在`Traits`标签，点击`Import trait`。浏览并加载'YFV\_coordinates.txt'制表符分隔文件。

* 加载文件后，在面板左侧选择'Lat'和'Long'特征，选择`Create partition from trait...`，为该分区输入名字'location'。

* 在`Sites`标签，保持默认的HKY替代模型，碱基频率为`Estimated`，‘Site Rate Heterogeneity’为'Gamma'。

* 在`Substitution model`窗口的左侧点击位置，保持默认的'Homogenous Brownian model'并选择`Bivariate trait represents latitude and longitude`，`Jitter` 设置为'0.01'。

* 在`Clocks`标签，选择`Lognormal relaxed molecular clock (Uncorrelated)`模型。

* 在`Trees`标签，选择`skygrid model`模型作为人口统计树的先验值(`Coalescent: Bayesian SkyGrid`)，设置为36个网格点(`number of parameters`) 并且设置`A time at last transition point`为`0.6948424`(定义网格点接近于系统发育持续时间跨越的流行病学周数)。

* 在`MCMC`标签，设置链长为500,000,000，取样频率为50,000。将主干文件名设置为'YFV\_RRW\_cauchy'并生成beast 文件('YFV\_RRW\_cauchy.xml')。

### 运行BEAST并加载xml文件。

### 用Tracer分析输出文件。分析运行较长链长的输出文件。

### 用treeAnnotator总结运行较长链长的树。

### FigTree可视化树。

### 运行SpreaD3。

首先我们将总结MCC树，然后总结整个树分布中的信息。

* 在`Data`面板选择输入类型: `MCC tree with CONTINUOUS traits`，加载MCC树文件。

* 选择‘location1’作为`Latitute attribute`的名字，‘location2’作为`Longitude attribute`的名字。

* 设置`most recent sampling date`为2017-04-22。

* 加载GeoJSON 'South_America.json'。

* 保持所有的默认设置，并点击`Output`生成JSON文件。

* 转到`Rendering`面板，保持D3 renderer作为默认的选项，并加载生成的JSON文件。

* 点击`Render to D3`生成HTML网页，一个浏览器窗口将自动打开。

这将MCC树可视化其节点位置的不确定性:

{% include image.html file="YFV_screenshot_23_spreaD3_3.png" prefix=root_url caption="" %}

## 总结和资源

本教程仅涉及使用BEAST进行浅层次的分析。希望能够对所有BEAST分析所共有的基本步骤提供一个大体的介绍，并为更具挑战性的研究提供基础。BEAST是一个持续的开发项目，定期添加新的模型和技术。BEAST网站提供了邮件列表的详细信息，用于公布新功能并讨论包的使用。该网站还包含一系列教程和题材，用于回答使用BEAST的特定进化问题，以及XML输入格式，常见问题和错误消息的描述。

## 参考文献

* [Faria, N. R., M. U. G. Kraemer, S. C. Hill, J. Goes de Jesus,  R. S. Aguiar, F. C. M. Iani, J. Xavier, J. Quick, L. du Plessis, S. Dellicour, J. Thézé, R. D. O. Carvalho, G. Baele, C.-H. Wu, P. P. Silveira, M. B. Arruda, M. A. Pereira, G. C. Pereira, J. Lourenço, U. Obolski, L. Abade, T. I. Vasylyeva, M. Giovanetti, D. Yi, D. J. Weiss, G. R. W. Wint, F. M.  Shearer, S. Funk, B. Nikolay, V. Fonseca, T. E. R. Adelino, M. A. A. Oliveira, M. V. F. Silva, L. Sacchetto, P. O. Figueiredo, I. M. Rezende, E. M. Mello, R. F. C. Said, D. A. Santos, M. L. Ferraz, M. G. Brito, L. F. Santana, M. T. Menezes, R. M. Brindeiro, A. Tanuri, F. C. P. dos Santos, M. S. Cunha, J. S. Nogueira, I. M. Rocco, A. C. da Costa, S. C. V. Komninakis, V. Azevedo, A. O. Chieppe, E. S. M. Araujo, M. C. L. Mendonça, C. C. dos Santos, C. D. dos Santos, A. M. Mares-Guia, R. M. R. Nogueira, P. C. Sequeira, R.G. Abreu, M. H. O. Garcia, A. L. Abreu, O. Okumoto, E. G. Kroon, C. F. C. de Albuquerque, K.  Lewandowski, S. T. Pullan, M. Carroll, T. de Oliveira, E. C. Sabino, R. P. Souza, M. A. Suchard, P. Lemey, G. S. Trindade, B. P. Drumond, A. M. B. Filippis, N. J. Loman, S. Cauchemez, L. C. J. Alcantara, and O. G. Pybus 2018. Genomic and epidemiological monitoring of yellow fever virus transmission potential. Science 361:894-899.](https://https://science.sciencemag.org/content/361/6405/894.long)

* [Lemey, P., A. Rambaut, J. J. Welch, and M. A. Suchard. 2010. Phylogeography takes a relaxed random walk in continuous space and time. Molecular biology and evolution 27:1877-1885.](https://academic.oup.com/mbe/article-lookup/doi/10.1093/molbev/msq067)

* [Pybus, O. G., M. A. Suchard, P. Lemey, F. J. Bernardin, A. Rambaut, F. W. Crawford, R. R. Gray, N. Arinaminpathy, S. L. Stramer, M. P. Busch, and E. L. Delwart. 2012. Unifying the spatial epidemiology and molecular evolution of emerging epidemics. Proceedings of the National Academy of Sciences of the United States of America 109:15066-15071.](http://www.pnas.org/content/109/37/15066.short)

* [Bielejec, F., G. Baele, B. Vrancken, M. A. Suchard, A. Rambaut and P. Lemey. 2016. SpreaD3: interactive visualisation of spatiotemporal history and trait evolutionary processes. Mol. Biol. Evol., 33(8): 2167–2169. doi: 10.1093/molbev/msw082](https://academic.oup.com/mbe/article-lookup/doi/10.1093/molbev/msw082)

{% include links.html %}
