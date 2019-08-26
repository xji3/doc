---
title: 在离散空间中的系统地理学扩散
keywords: phylogeography, rabies, bats, tutorial
last_updated: August 9, 2017
tags: [tutorial]
summary: '本章讲述的是基于一组372条N基因序列(核苷酸位置: 594–1353)集来重建北美蝙蝠种群中狂犬病病毒(RABV)的空间扩散及跨种传播动力学的逐步分析教程。数据集为在1997-2006年期间在美国14个洲采集的17个蝙蝠物种的样本
(<a href="http://science.sciencemag.org/content/329/5992/676.long">Streicker et al., Science, 2010, 329, 676-679</a>). 继Faria 等人之后<a href="http://rstb.royalsocietypublishing.org/content/368/1614/20120196.long"> (Phil. Trans. Roy. Soc. B, 2013)</a>, 由于可用序列的有限性，从原始序列中排除了2个其他的物种，<i>Myotis austroriparius</i> (Ma) 和<i>Parastrellus hesperus</i> (Ph)，也包括在这里。我们也将没有采样日期的毒株序列也包含其中(accession no. TX5275，采样于Texas<i>Lasiurus borealis</i>)，这将完全适应我们的推断。本教程的目的是用贝叶斯离散的系统地理学方法估计病毒的起源地点，同时用同样的模型方法推断宿主跳跃史。通过拓展离散扩散模型，我们将试图推断影响宿主转换动力学的因素。'

sidebar: beast_sidebar
permalink: workshop_discrete_diffusion_chs.html
folder: beast
---

{% capture root_url %}{{ site.tutorials_root_url }}/workshop_discrete_diffusion/{% endcapture %}

## 前言

第一步是将比对好的fasta格式的文件转换为BEAST XML输入文件。该操作通过程序BEAUti(t代表着贝叶斯进化分析实用程序)来完成。这是一个用户友好的程序，用于设置MCMC分析的进化模型和选项。第二步是使用包含数据，模型和设置的输入文件实际运行BEAST。最后一步是探索BEAST的输出，以便诊断问题并总结结果。

要执行本教程，您需要您的计算机系统兼容的三个软件包 (这三个软件包均适用于Mac OS X，Windows and Linux/UNIX 操作系统):

{% include beast_callout.md %}

{% include beagle_callout.md %}

{% include tracer_callout.md %}

{% include figtree_callout.md %}

{% include spread3_callout.md %}

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 本教程需要的所有文件<a href="{{ root_url }}files/discreteTutorialFiles.zip"> 可以从此处下载</a>.
如果您已经下载了压缩文件夹，没有必要再下载本教程链接的其他文件或文件夹。
</div>
<!--
<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 本教程所需所有文件压缩文件夹<a href="{{ root_url }}discreteTutorialFiles.zip">可以从此处下载</a>。(如果您下载了该压缩文件，没有必要再下载链接中的其他文件) </div>
-->

## 练习1: 祖先的宿主和起源地的重建

### Running BEAUti

{% include icon-callout.html file='icons/beauti-icon.png' content='<a href="beauti">BEAUti</a>双击其图标运行BEAUti' %}

#### 载入序列数据文件

本教程的输入文件 <samp>batRABV.fas</samp>可以[从此处下载({{ root_url }}files/batRABV.fas)。该fasta格式文件为372条蝙蝠狂犬病毒核酸蛋白基因序列的比对文件，长度为1353个核苷酸。

为了加载比对文件，仅需要从`File`菜单选择`Import Data...`选项。选择<samp>batRABV.fas</samp> 文件。一旦载入，序列数据将会在`Partitions` 下展示如图所示:

{% include image.html file="02_sequencePartition.png" prefix=root_url %}

<!--
双击表格行 (而不是Partition 的名字) 可以展示比对序列:

{% include image.html file="sequenceAlignment.png" prefix=root_url %}
-->

#### 指定采样日期信息

默认情况下假设所有的分类单元的采样日期为0(即假设序列同时采样).但是，RABV 序列采样于不同年份，并且可以追溯到1997年。通过主窗口的选项卡切换到`Tips` 面板来设置日期。

选择`Use tip dates`框。RABV序列的采样日期编码其标签中，因此请使用面板`Tips`顶部的`Parse Dates`按钮。单击此按钮将显示一个对话框:

{% include image.html file="03_guessDates.png" prefix=root_url width="80%" align="center" %}

此操作尝试从分类单名称中包含的信息中猜测日期。它的工作原理是尝试在每个名称中找到一个数字字段。如果分类单元名称包含多个数字字段，则可以指定如何找到与采样日期对应的数字字段。[有关在此面板中设置日期的各种选项的详细信息，请参阅此页面](tip_dates)。对于RABV序列，您可以保留默认值`Defined just by its order`和`Order: last`然后点击`OK`。日期将显示在主窗口的相应列中。

{% include image.html file="04_datesSpecified.png" prefix=root_url %}

现在可以检查这些并根据需要手动编辑它们。在窗口的顶部，您可以设置日期的单位(年，月，日)以及它们是否相对于过去的某一时间点(比如本教程中的2005年)或者从现在往后的时间点 (如放射性碳的年龄)。

`Height` 列出了相对于时间0的提示年龄(本离中是2005.5)。`Uncertainty`允许您指定已知采样时间的不确定性。例如，如果仅仅知道采样年份，则可以设置1年的不确定性，并且可以使用`Tips`面板左下角的`Tip date sampling` 在1年的时间间隔内对该提示的年龄进行积分。可以为此RABV数据集指定1年的不确定性，但这种不确定性相对于该进化历史的时间尺度而言较小，因此我们不会将其用于此分析。

在我们的数据集中，一个特定序列的采样日期是未知的(<samp>TX5275_2002.5</samp>，‘2002.5’只是一个随机日期，将用作起始值)。为了适当地适应该节点年龄的不确定性，我们将指示BEAST在特定的采样时间间隔内整合该节点年龄。

首先，返回到我们跳过的`Taxa`选项卡，并仅为该特定序列设置分类。点击面板左下角的`plus`按钮；将会创建一个新的分类集。通过双击出现的条目重命名它(起初称为untitled1)。命名其为TX5275，并保持默认设置。将TX5275_2002.5从`Excluded Taxa` 窗口移动到`Included Taxa`窗口:

{% include image.html file="05_taxonSet.png" prefix=root_url %}

返回到`Tips`选项卡，在其左下角选择`Sampling with individual priors`作为`Tip date sampling`选项。将该设置应用到 <samp>TX5275</samp> 分类集，而不是默认`All taxa`选项。当我们在`Priors`选项卡时，我们将为它的年龄设置先验值。

{% include image.html file="06_tipdateSampling.png" prefix=root_url %}

{% include tip.html content='<a href="tip_date_sampling"> 有关采样提示日期的详细信息，请参阅此页面</a>.' %}

#### 指定特征信息

接下来要做的是单击主窗口顶部的`Traits`选项卡。trait可以是特定分类群固有的任何特征，如地理位置或者宿主。基于在<samp>batRABV_hostLocation.txt</samp>文件中每条序列的指定特征，此步骤将为每个分类单元指定特定的宿主和地理位置，[从此处下载](files/batRABV_hostLocation.txt)。要将序列与特征相关联，我们需要在`Traits`选项卡下添加一个新的特征(点击`Add trait`)。将会打开一个新的窗口来创建或导入特征:

{% include image.html file="07_importTrait.png" width="80%" prefix=root_url %}

选择`Import trait(s) from a mapping file` (可以显示此类文件的格式)。浏览并加载batRABV_hostLocation.txt 制表符分隔文件。请注意，使用双字符缩写宿主名称(如Ef代表*Eptesicus fuscus*，Lbl表示三个字符)，如此文件的片段所示:

```
	traits	host	state
	AZ4030_2005.5	Ap	Arizona
	AZ1968_2004.5	Ef	Arizona
	AZ7590_2005.5	Ef	Arizona
	CA237_2002.5	Ef	California
	CA29_2002.5	Ef	California
	CA9242_2002.5	Ef	California
	CAO120_2002.5	Ef	California
	CA0253_2003.5	Ef	California
	CA148_2004.5	Ef	California
	CA6860_2004.5	Ef	California
	CA0100_2005.5	Ef	California
	GA31940_2004.5	Ef	Georgia
		...
	TX3545_2004.5	Tb	Texas
```

点击`OK`后，选择宿主特征并点击`create partition from trait..`。此新分区将在 `Partitions`选项卡下显示。对位置特征(状态)执行相同操作，从而在 `Partitions`选项卡中生成三个分区:

{% include image.html file="08_traitPartitions.png" prefix=root_url %}

#### 设置序列和特征进化模型

接下来要做的是单击主窗口顶部的`Sites`选项卡。这将揭示BEAST的进化模型设置。对于本教程中的核苷酸模型，保留默认的HKY替换模型，将碱基频率设置为Empirical，并在位点之间使用Gamma分布的速率变化(4个离散分类):

{% include image.html file="09_substModel.png" prefix=root_url %}

点击`Substitution model`窗口下的'host'，保持`Discrete Trait Substitution Model`为`Symmetric substitution model`，选择BSSVS选项 (`Infer social network with BSSVS`)。对称替换模型使用标准连续时间马尔可夫链(CTMC)指定离散状态祖先重建，其中位置之间的转换速率是可逆的。非对称替代模型使用不可逆的CTMC指定离散状态祖先重建。选择选项BSSVS可启用贝叶斯随机搜索变量选择过程。该过程将尝试将速率的数量(至少k-1，k是状态的数量)限制为仅充分解释系统发育扩散过程的速率。

{% include image.html file="10_hostModel.png" prefix=root_url %}<br /><br />

将相同的离散扩散模型设置应用于地理位置特征<samp>state</samp>。

#### 设置‘分子钟’模型

`Clocks`面板下的‘Molecular Clock Model’选项在严格和宽松（不相关的对数正态或不相关指数）的分子钟之间进行选择。我们将使用默认的Strict时钟模型执行运行:

{% include image.html file="11_clockModel.png" prefix=root_url %}

在<samp>host</samp>和地理转换过程中<samp>state</samp>可以保持默认的整体速率缩减。

现在切换到`Trees`面板。

#### 设置树的先验分布

此面板包含有关树的设置。首先，将起始树指定为`Random starting tree`。此处的另一个主要设置是指定`Tree prior`，描述的是根据聚结模型描述人口规模如何随时间变化。默认树先前设置为恒定大小的聚结先验分布。本教程，将保持默认设置。

{% include image.html file="12_treePrior.png" prefix=root_url %}

#### 祖先状态设置

在`States`面板，检查是否为宿主和状态分区选择了重建所有祖先状态的选项(默认)。

{% include image.html file="13_statesPanel.png" prefix=root_url %}

#### 设置先验

现在切换到`Priors`选项。该面板有一个表格，显示当前所选模型的每个参数以及每个参数的先前分布。较强的先验分布允许用户通过选择具有小方差的特定分布来‘推断’分析。或者，我们也可以选择弱的(离散的) 先验分布，从而最小化对分析的影响。请注意，必须为每个参数指定先前分布，尽管BEAUti提供默认选项，这些选项不一定适合于要分析的问题和数据。

进化速率的默认先验(clock.rate)是条件参考先验的近似值(Approx. Reference Prior) (Ferreira and Suchard, 2008)。这同样适用于离散的宿主和位置状态速率。对于TX5275的年龄(age(TX5275_2002.5) )还存在默认的统一先验规范。我们将假设此采样节点的的采样时间受此数据集所有类群的采样时间范围的限制，这意味着它在1997.5和2005.5之间进行采样。单击当前统一先前设置，设置年龄的`Upper`为8年(反映1997.5的边界)并点击`OK`。

{% include image.html file="14_uniformPrior.png" width="80%" prefix=root_url %}

生成的先验表格如图所示s:

{% include image.html file="14_priors.png" prefix=root_url %}

#### 设置采样模块

模型中的每个参数都有一个或多个“采样模块”(被其他MCMC软件包如MrBayes和LAMARC称为移动，建议或转换内核)。采样模块指定的是参数是如何随着MCMC的运行而改变的。 BEAUti中的`Operators`选项列出了参数，其相关采样模块以及采样模块的调整设置:

{% include image.html file="15_operators.png" prefix=root_url %}

在当前分析中我们可以保持采样模块的默认设置。

#### 设置MCMC选项

BEAUti中的`MCMC` 选项提供了控制MCMC链和生成log文件的设置。

{% include image.html file="16_mcmc.png" prefix=root_url %}

对于本数据集我们起初设置链长为<samp>100,000</samp>取样频率为 <samp>100</samp>。`File name stem:` 应该设置为 <samp>batRABV</samp> ，但是也可以对其调整(可以添加更多关于分析的指示).

我们现在准备创建BEAST XML文件。从`File`菜单(或者主窗口底部按钮)选择`Generate XML...`。BEAUti将要求您在保存文件之前再次查看先前的设置(并指示是否有任何不正确的设置)。继续并选择文件的名称 --- 它将在MCMC面板中提供您给出的名称，我们通常以'.xml'结束文件名(尽管在Windows系统上，您可能希望为文件提供扩展名'.xml.txt')。

{% include tip.html content="为方便起见，请保持BEAUti窗口处于打开状态，以便您可以根据本教程后面的要求更改值并重新生成BEAST文件。" %}

### 运行BEAST

一旦创建BEAST XML文件后，可以使用BEAST执行分析本身。

{% include icon-callout.html file='icons/beast-icon.png' content='<a href="beast">BEAST</a> 双击BEAST按钮运行' %}

BEAST启动后，将出现一个对话框，您可以在其中选择XML文件:

{% include image.html file="17_BEASTgui.png" prefix=root_url  width="80%" align="center" %}

选择`Choose File...` 按钮，选择刚才创建的XML文件，然后 `Run`。然后将执行分析，其中包含有关正在写入屏幕的运行进度的详细信息。完成后，将在与XML文件相同的位置创建log文件和trees文件。

[有关BEAST对话框中其他选项的更多信息，请参阅此页面](beast).

### 分析BEAST的输出文件

为了分析运行BEAST的结果，我们将使用Tracer程序。运行Tracer的确切说明因您使用的计算机而异。双击Tracer图标; 一旦运行，Tracer将看起来类似，无论它运行在哪个计算机系统上。

从`File`菜单选择`Import Trace File...`选项。如果可用，请选择您在上一节中创建的log文件(<samp>batRABV.log</samp>)。 或者，将log文件拖放到Tracer窗口中。该文件将加载，您将看到一个类似于下面的窗口。请记住，MCMC是一种随机算法，因此实际数字不会完全相同。

{% include image.html file="18_tracerShort.png" prefix=root_url %}

左侧是加载的log文件的名称及其包含的跟踪。有后验概率的追踪(这是似然树和先验概率的乘积的对数)和连续参数。选择左侧的追踪会根据所选的选项卡在右侧显示此追踪的分析。首次打开时，会选择'posterior'追踪，并在`Estimates`选项卡下显示此跟踪的各种统计信息。

{% include callout.html content='有关上面显示的各种汇总统计信息的说明，请参阅<a href="workshop_rates_and_dates#analysing-the-beast-output">see this section of the \'Estimating rates and dates from time-stamped sequences\' tutorial</a>.' %}

请注意，所有追踪的有效样本量(ESSs)都很小。从窗口的顶部选择`Trace`面板，并检查各种参数的轨迹。您将看到链仍处于老化阶段(后验值在整个链中仍在增加)，并且此运行不允许我们总结参数的边际后验概率分布。

对这种情况的简单回应是我们需要更长时间地运行链长。我们提供了很长时间的结果---2亿步，每50,000步采样一次，产生4,000个样品。在这种情况下，MCMC运行已达到平稳性，并且几乎所有参数迹线仍显示令人满意的ESS。

<!--
<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 在分享的文件夹中可以找到运行较长的结果:<br />
<div style="margin: 16px"><code>Tutorials\Tutorial 4 - Discrete Phylogeography\longRuns\BSSVS</code></div>
</div>
-->

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> The log and trees files  运行更长的RABV离散地理学的log和tree文件<a href="{{ root_url }}files/RABV_longRuns.zip">can be downloaded from here</a>.</div>

您可以导入运行更长时间的log 文件(<samp>batRABV.log</samp>)到同样的Tracer窗口与较短时间的进行比较。给出:

{% include image.html file="19_tracerLong.png" prefix=root_url %}

我们可以继续总结BSSVS推断的注释的系统地理树，并估计最重要的扩散速率。如果您只想从BSSVS分析中总结Bayes Factor率而不是总结运行中的树，请跳到本教程的最后一部分，标题为 [使用SpreadD3可视化树和计算贝叶因子支持率](#visualizing-mcc-trees-and-calculating-bayes-factor-support-for-rates-using-spread3).  

### 总结和可视化树

此时，您可以使用[TreeAnnotator](treeannotator)汇总采样树，然后使用[FigTree](figtree)可视化生成的树。

{% include callout.html content='前面的 <a href="workshop_rates_and_dates#analysing-the-beast-output">\'Estimating rates and dates from time-stamped sequences\' tutorial</a> 介绍了如何执行此操作的详细说明.' %}

### 使用SpreaD3可视化MCC树并计算速率的贝叶斯因子支持值

SpreaD3，即使用数据驱动文档(D3)对进化动力学的空间系统发育进行重建，是一种可视化贝叶斯系统地理学分析输出的用户友好的应用程序，用于分析和可视化对序列和进化特征进行贝叶斯推断产生的重建。preaD3允许在自定义地图上显示空间重建，并生成HTML页面，以便在现代浏览器如 Firefox，Safari以及Chrome中显示。

与离散系统地理分析相关的一些功能包括使用贝叶斯因子测试可视化位置注释的MCC树和识别良好支持的速率。后一个选项将速率矩阵文件作为输入 (<samp>batRABV.state.rates.log</samp>对于地点状态，<samp>batRABV.host.rates.log</samp> 对于宿主状态)在贝叶斯随机搜索变量选择(BSSVS)过程中生成。该测试旨在识别频繁调用的速率以解释扩散过程，并以地点为例，将它们可视化在圆圈和地球仪或地图上，这需要提供给SpreaD3。

{% include callout.html content='此处提供该特定步骤的详细教程 <a href="https://rega.kuleuven.be/cev/ecv/software/SpreaD3_tutorial#sectionFourTwo"></a>。我们还提供了整个SpreaD3教程的PDF版本供下载<a href="files/SpreaD3Tutorial.pdf"></a>.' %}

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 分析中所需要的数据文件可以在分享的文件夹中找到:<br /><div style="margin: 16px"><code>Tutorials\Tutorial 4 - Discrete Phylogeography\</code></div>
</div>

为了可视化MCC树，通过双击jar文件启动SpreaD3，在`Data`面板选择`MCC tree with DISCRETE traits`。加载MCC树并将地点属性设置为‘state’。 然后选择`Setup location attribute coordinates`加载状态及其坐标'<samp>locationStates.txt</samp>’文件， 应该是这样的:

```
	Arizona		33.7712	-111.3877
	California	36.17	-119.7462
	Georgia		32.9866	-83.6487
	Iowa		42.0046	-93.214
	Michigan	43.3504	-84.5603
	NewJersey	40.314  -74.5089
	Virginia	37.43157    -78.656895
	Washington	47.3917	-121.5708
	Florida		27.8333	-81.717
	Tennessee	35.7449	-86.7489
	Texas		31.106	-97.6475
	Idaho		44.2394	-114.5103
	Indiana		39.8647	-86.2604
	Mississippi	32.7673	-89.6812
```

坐标[可以从此处下载]({{ root_url }}files/locationStates.txt).

这将加载地点及其纬度/经度坐标。上传地点及其坐标后单击完成。将最近的采样日期设置为2005.5并以GeoJSON格式加载美国地图。这样的地图是在数据文件中提供的--- <samp>gz_2010_us_040_00_500k.json</samp>。

转到`Generate Output`并选择要写入的JSON文件的文件名。最后，转到SpreaD3中的Rendering面板并加载刚刚保存的JSON文件。 单击 `Render` 到D3并选择一个目录名称，该目录名称将包含将在浏览器中自动加载的HMTL页面(如下所示)。请注意，需要使用特定的本地文件访问权限启动Google Chrome才能显示生成的可视化文件(Firefox和Safari应该可以正常使用默认设置)。

{% include image.html file="22_spread3MCC.png" prefix=root_url %}

要总结贝叶斯因子对速率的支持，在`Data`面板选择`Log file from BSSVS analysis`。设置适当的老化级别并用`Load log file` 载包含空间速率和速率指示的输出BEAST文件(batRABV.state.rates.log)。然后用`Setup location attribute coordinates` 在北美地图上可视化贝叶斯因子。选择`Load` 并获取地点文件(<samp>locationStates.txt</samp>)。点击`Done`.

在同一个`Data`面板中，您还可以指定泊松先验均值和偏移值，在本例中不需要更改。加载GeoJSON格式的美国地图。完成此操作后，转到`Generate Output`并选择要写入的JSON文件的文件名。请注意，还将创建一个纯文本文件，其中包含额外的'.txt'扩展名，其中包含实际的贝叶斯因子值。最后，转到SpreaD3的`Rendering`面板，并加载刚刚保存的JSON文件。单击`Render to D3`，并选择目录名称。可以在下面找到示例可视化。请注意，在视觉方面可以修改表示速率的线条的视觉方面 也可以通过cut-off值过滤线条(在`Lines cut-off`下)。

{% include image.html file="23_spread3stateRates.png" prefix=root_url %}

我们也可以获得宿主转换率的类似结果。由于这些不能在地图上绘制，我们会将它们组织在一个圆上。加载包含宿主速率和速率指示的文件 (<samp>batRABV.host.rates.log</samp>)。在设置位置时，选择`Generate`，并输入独特宿主状态的数量(本例子为'17')。如果您想绘制位置的名称而不是location1，location2，…，请输入17个位置(Ap, Ef, Lb, Lbl, Lc, Li, Ln, Ls, Lx, Ma, Mc, Ml, My, Nh, Ph, Ps, Tb)。输入所有信息后单击完成，然后在`Generate Output`下单击输出，并选择要写入的JSON文件的文件名。最后，转到SpreaD3的`Rendering`面板，加载刚刚保存的JSON文件，然后单击`Render to D3`。

{% include image.html file="24_spread3hostRates.png" prefix=root_url %}

{% include question.html content='哪些速率的贝叶斯因子支持率最高？' %}

## 练习2：识别宿主转换过程的预测变量

### 背景

本练习以之前的分析为基础，旨在推测推动北美蝙蝠狂犬病毒宿主转变过程的因素。最初的分析采用了人口遗传方法和事后统计程序来检验这些预测因子(Streicker et al., 2010)； 这里我们采用Faria等人应用的离散扩散模型的扩展(2013)。该扩展将CTMC矩阵参数化为广义线性模型(GLM)，其中log CTMC速率是几个潜在预测因子的对数线性函数(模型的大部分细节可以在Lemey et al., 2014的研究中找到)。我们使用Streicker et al. (2010)最初提出的预测变量: 宿主系统发育距离(基于宿主的线粒体DNA)，地理范围重叠，栖息结构重叠，并且使用三种形态测量来近似搜索生态位重叠: 机翼纵横比，机翼载荷和体长，这与蝙蝠的觅食行为有关。由于'捐献'和'接受'宿主作为额外的预测因子，因此我们还考虑序列样本大小，这可以能对对祖先重建产生偏差(参见Lemey et al., 2014).

### GLM扩散模型规范

重复第一个BEAUti步骤，设置序列和进化特征模型。如果上一次练习中的BEAUti框尚未关闭，只需返`Sites`面板。对于`Substitution Model`下的'host'特征，选择 `Generalized Linear Model`:

{% include image.html file="25_GLMinSites.png" prefix=root_url %}

点击`Setup GLM`，一个新的窗口将会出现:

{% include image.html file="26_GLMemptySetup.png" prefix=root_url %}

此窗口允许通过`Import Predictors...`导入来指定一组GLM预测变量或协变量。所有预测变量都可以以压缩文件下载[此处](files/predictors.zip)也可以在下面链接的单个文件下载。
首先根据线粒体基因距离加载蝙蝠狂犬病宿主之间的距离矩阵([hostDistances.csv](files/predictors/hostDistances.csv))。这是一个包含以下内容的csv文件(双字符标签代表蝙蝠种类):

```
	,Ap,Ef,Lb,Lbl,Lc,Li,Ln,Ls,Lx,Ma,Mc,Ml,My,Nh,Ph,Ps,Tb
	Ap,0,0.745,0.878,0.8,0.71,0.86,0.711,0.916,0.788,0.794,0.717,0.736,0.78,0.74,0.596,0.67,0.923
	Ef,0.745,0,0.971,0.893,0.803,0.953,0.586,1.009,0.881,0.887,0.81,0.829,0.873,0.615,0.689,0.763,1.016
	Lb,0.878,0.971,0,0.316,0.546,0.696,0.937,0.118,0.624,0.944,0.867,0.886,0.93,0.966,0.746,0.82,0.839
	Lbl,0.8,0.893,0.316,0,0.468,0.618,0.859,0.354,0.546,0.866,0.789,0.808,0.852,0.888,0.668,0.742,0.761
	Lc,0.71,0.803,0.546,0.468,0,0.65,0.769,0.584,0.578,0.776,0.699,0.718,0.762,0.798,0.578,0.652,0.671
	Li,0.86,0.953,0.696,0.618,0.65,0,0.919,0.734,0.357,0.926,0.849,0.868,0.912,0.948,0.728,0.802,0.821
	Ln,0.711,0.586,0.937,0.859,0.769,0.919,0,0.975,0.847,0.853,0.776,0.795,0.839,0.531,0.655,0.729,0.982
	Ls,0.916,1.009,0.118,0.354,0.584,0.734,0.975,0,0.662,0.982,0.905,0.924,0.968,1.004,0.784,0.858,0.877
	Lx,0.788,0.881,0.624,0.546,0.578,0.357,0.847,0.662,0,0.854,0.777,0.796,0.84,0.876,0.656,0.73,0.749
	Ma,0.794,0.887,0.944,0.866,0.776,0.926,0.853,0.982,0.854,0,0.273,0.292,0.196,0.882,0.576,0.65,0.989
	Mc,0.717,0.81,0.867,0.789,0.699,0.849,0.776,0.905,0.777,0.273,0,0.139,0.259,0.805,0.499,0.573,0.912
	Ml,0.736,0.829,0.886,0.808,0.718,0.868,0.795,0.924,0.796,0.292,0.139,0,0.278,0.824,0.518,0.592,0.931
	My,0.78,0.873,0.93,0.852,0.762,0.912,0.839,0.968,0.84,0.196,0.259,0.278,0,0.868,0.562,0.636,0.975
	Nh,0.74,0.615,0.966,0.888,0.798,0.948,0.531,1.004,0.876,0.882,0.805,0.824,0.868,0,0.684,0.758,1.011
	Ph,0.596,0.689,0.746,0.668,0.578,0.728,0.655,0.784,0.656,0.576,0.499,0.518,0.562,0.684,0,0.434,0.791
	Ps,0.67,0.763,0.82,0.742,0.652,0.802,0.729,0.858,0.73,0.65,0.573,0.592,0.636,0.758,0.434,0,0.865
	Tb,0.923,1.016,0.839,0.761,0.671,0.821,0.982,0.877,0.749,0.989,0.912,0.931,0.975,1.011,0.791,0.865,0
```

请注意，默认情况下，距离矩阵中的值被选择进行对数转换和标准化。这是因为GLM扩散模型将CTMC速率的对数参数化为预测因子的对数线性函数，并且我们给先验预测因子赋予相同的方差。重复此过程为范围重叠([rangeOverlap.csv](files/predictors/rangeOverlap.csv))，栖结构重叠([roostOverlap.csv](files/predictors/roostOverlap.csv))，翼宽比 ([wingAspectRatio.csv](files/predictors/wingAspectRatio.csv))，翼载荷差异([wingLoading.csv](files/predictors/wingLoading.csv))体征大小差异 ([bodySize.csv](files/predictors/bodySize.csv))。

注意，栖息地结构重叠值是'1'或'0'，表示两种蝙蝠物种是否共享栖息地结构。我们不会对这些值进行对数转换和标准化(通过取消选择这两个选项)， 因此'1'对共享栖息结构的物种的对数转换率的额外影响。估计这些比率将高于或低于不共享栖息地结构的物种的比率，这取决于相关的GLM系数在对数空间中是分别估计为正数还是负数。加载所有预测变量并取消选择栖息地结构重叠的默认变换后，`GLM setting for host`窗口应如下所示:

{% include image.html file="27_GLM6PSetup.png" prefix=root_url %}

继续执行上一练习中的后续步骤。请注意，在`Priors` 面板中，在log GLM系数('host.coefficients')上指定了平均值为0且标准差为2的常规先验。我们可以再次设置一个简短的测试运行(如100,000 MCMC迭代)，但是用运行更长链长的来诊断和总结。您可以下载MCMC分析的输出，该分析每50,000代采样一次，运行1亿代[此处下载](files/longRuns/GLM/batRABV_6Pglm.host.glm.log)。

### 分析GLM扩散模型的输出结果

该分析中感兴趣的参数是与预测变量相关的指标 ('host.coefIndicators1' 到'host.coefIndicators6')以及相关系数参数或者效应大小 ('host.coefficients1'到'host.coefficients6')。 当加载了log文件('batRABV_6Pglm.host.glm.log')时，指标的平均值提供该指标的包含概率的估计。本例子中，'host.coefficients1'是平均指示符为1(表示宿主之间的遗传距离)，表示此预测变量始终包含在模型中。第二高的包含概率与范围重叠('host.coefficients2')，而其余的预测因子都与非常低的包含概率相关联。

{% include image.html file="28_GLM6P_indicators.png" prefix=root_url %}

为了评估数据为预测变量包含提供的证据，我们需要考虑先验包含的概率。默认情况下，BEAUti指定这些指标为Bernoulli先验概率分布，每个预测变量包含的先验概率较小，即在未包含预测变量的情况下，先验概率为50％，在这种情况下:

{% include image.html file="29_indicator_prior.png" prefix=root_url %}

基于先验和后验包含概率，我们可以以贝叶斯因子的形式计算常规包含支持，因为这些可以表示为后验概率与预测因子包含的先验概率之比。对于包含概率为1，贝叶斯因子估计为正无穷大。在这种情况下，最好将贝叶斯因子表示为大于X，其中如果一个样本具有且指标= 1，则X将是贝叶斯因子值。对于重叠范围，后验概率比值比为0.612/(1-0.612) = 1.577，然而先验值比值比为0.109/(1-0.109) = 0.122；这将会产生贝叶斯因子大约为13。所有其他预测因子的后验包含概率小于其先前的包含概率，因此它们将具有贝叶斯因子<1。

为了评估预测变量贡献的大小，我们可以使用log空间中系数的估计值。但是，重要的是要记住估计值严格依赖于相应的指标值。如果指标为1，则预测变量包含在模型中，系数将通过数据得出(预测变量和离散状态)。如果指标为0，则不包括预测变量，系数值将从先前的变量中采样。这就是为什么具有非常小的包含概率的系数的后验估计将类似于先验分布(以0为中心且标准偏差为2的正态分布)，如同栖息地结构重叠('host.coefIndicators3')，翼长宽比差异('host.coefIndicators4')，翼载差异('host.coefIndicators5')以及体征大小差异('host.coefIndicators6')。Tracer中这些系数的小提琴图表明了这一点 :

{% include image.html file="30_coefficients_violin.png" prefix=root_url %}

这使得具有中间包含概率的预测变量的系数估计值解释复杂化(如重叠范围的'host.coefIndicators2')。这就是为什么应用程序采用报告条件效果大小，即当模型中包括的预测变量时的效果大小(indicator = 1)。换句话说，这可以通过仅根据相应指标值为1的样本汇总系数估计值来获取。或者，可以汇总所有样本的系数和指标的系数和指标的预测变量(这些参数记录为统计信息: host.coefficientsTimesIndicators1 到 host.coefficientsTimesIndicators6)。在 Tracer 中绘制这些统计数据的小提琴图如下所示:

{% include image.html file="31_coefTimesInd_violin.png" prefix=root_url %}

对于宿主遗传距离('host.coefficientsTimesIndicators1')，由于对于这些预测因子的相关指标宗伟1，因此这些参数的后验密度与其实际相关系数一致('host.coefIndicators1')。负系数为更密切相关的宿主物种之间的宿主转换强度提供了更高的证据。对于范围重叠的统计的后验分布('host.coefficientsTimesIndicators2')显示了正数的相关密度，但也显示了如预期其包含概率一样在0处存在不可忽略的密度。因此，尽管预测值并没有对宿主遗传举例产生较大的支持值，但它的确表明在宿主重叠范围间存在较强的转换关系。


### 评估取样大小的影响

取样大小在离散祖先重建中对速率的估计影响很大，因此也影响了参数化这下速率的GLM参数。 为了评估预测变量对样本大小的敏感性程度，我们可以将样本大小作为预测变量。然而，样本大小只是与单个离散状态相关的测量而不是状态之间的测量。因此，我们将样本大小作为'贡献'和 '接受' 宿主的测量。因此，对于每对蝙蝠宿主，我们将考虑此对中供体和受体的样本大小可以预测宿主转换的强度。这里的目的不是展示样本大小的预测能力，而是评估其他预测变量的包含概率是否对包含/排除样本大小敏感。 要设置包含样本大小预测变量的GLM模型，返回到GLM窗口导入 [sampleSize.tsv](files/predictors/sampleSize.tsv)作为预测变量。注意默认情况下，'Origin'和'Destination'会被选择:

{% include image.html file="32_GLM8PSetup.png" prefix=root_url %}

{% include question.html content='基于提供的运行更长链长的估计([此处](files/longRuns/GLM batRABV_8Pglm.host.glm.log))，在本例中，取样大小是否会影响GLM 参数的估计？' %}



## 参考文献
* [Streicker, D. G., A. S. Turmelle, M. J. Vonhof, I. V. Kuzmin, G. F. McCracken, and C. E. Rupprecht. 2010. Host phylogeny constrains cross-species emergence and establishment of rabies virus in bats. Science 329:676-679.](http://science.sciencemag.org/content/329/5992/676.long)
* [Faria, N. R., M. A. Suchard, A. Rambaut, D. G. Streicker, and P. Lemey. 2013. Simultaneously reconstructing viral cross-species transmission history and identifying the underlying constraints. Philosophical transactions of the Royal Society of London. Series B, Biological sciences 368:20120196.](http://rstb.royalsocietypublishing.org/content/368/1614/20120196.long)
* [Ferreira, M. A. R. and M. A. Suchard. 2008. Bayesian analysis of elapsed times in continuous-time Markov chains. Can J Statistics, 36: 355–368. doi: 10.1002/cjs.5550360302](http://onlinelibrary.wiley.com/doi/10.1002/cjs.5550360302/abstract)
* [Lemey, P., A. Rambaut, A. J. Drummond, and M. A. Suchard. 2009. Bayesian phylogeography finds its roots. PLoS computational biology 5:e1000520.](http://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1000520)
* [Lemey, P., A. Rambaut, T. Bedford, N. Faria, F. Bielejec, G. Baele, C. A. Russell, D. J. Smith, O. G. Pybus, D. Brockmann, and M. A. Suchard. 2014. Unifying Viral Genetics and Human Transportation Data to Predict the Global Transmission Dynamics of Human Influenza H3N2. PLoS pathogens 10:e1003932.](http://journals.plos.org/plospathogens/article?id=10.1371/journal.ppat.1003932)
* [Bloomquist, E. W., P. Lemey, and M. A. Suchard. 2010. Three roads diverged? Routes to phylogeographic inference. Trends Ecol Evol 25:626-632.## Help and documentation.](https://www.ncbi.nlm.nih.gov/pubmed/20863591)

## 帮助文档

BEAST网站: [http://beast.community](http://beast.community)

教程: [http://beast.community/tutorials](http://beast.community/tutorials)

常见问题: [http://beast.community/faq](http://beast.community/faq)


{% include links.html %}
