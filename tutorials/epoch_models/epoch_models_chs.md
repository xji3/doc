---
title: 世代模型教程
keywords: epoch, tutorial
last_updated: July 6, 2017
tags: [tutorial]
summary: "在BEAST中设置时间异质性世代替换模型。"
sidebar: beast_sidebar
permalink: epoch_models_chs.html
folder: beast
---

## 世代模型教程

本教程描述了如何拓展BEAST的XML文件(如BEAUti生成的)，将时间异质性(世代)模型包含其中(见Bielejec等人，2014)。

### 前言

本教程我们将展示如何设置，运行以及分析Bielejec等人(2014)描述的世代模型的数据。
我们将呈现人工合成的数据，并描述基本的步骤设置和运行实际数据。
本教程的例子可以从[此处](tutorials/epoch_models/files/epochExample.zip)下载。

### 先决条件

本教程假定使用者对BEAST的使用流程有基本的了解，知道如何生成XML输出文件，以及如何分析MCMC运行输出的基本信息。
可以在所有主要平台上运行的BEAST，在[此处](https://github.com/beast-dev/beast-mcmc)可以找到。
您将需要版本为1.5或更高的Java运行环境来运行BEAST。
由于世代模型的计算强度，需要安装BEAGLE库，用于此处描述的BEAST运行。
如何安装和使用BEAGLE库，详见[BEAGLE网站](https://github.com/beagle-dev/beagle-lib)。

### 数据

通过加载数据到BEAUTi并生成标准的XML文件开始。
为了简便，本例假设单个核苷酸分区，恒定种群大小模型以及严格的分子钟模型。

本教程中的数据是根据MCC树人工生成的，该MCC树是通过在不同流行季节采样的人类A型流感血凝素基因序列的贝叶斯系统发育分析而生成的(Drummond and Suchard, 2010)。
该树见图1。

我们将使用HKY核苷酸替代模型(Hasegawa et al., 1985)分析数据，集中于估计3个不同时期的&kappa; 参数(如转换-替换偏倚)。

{% include image.html file="epoch1.png" prefix="tutorials/epoch_models/" alt="Tree topology" caption="图1:用于时间异质性分析的拓扑树" %}

第一个时期一直持续到过渡时间T<sub>1</sub> 设置为7(即距最近采样日期的前7年)，其模型为HKY<sub>1</sub>。
第二个时期为过渡时间T<sub>1</sub>和T<sub>2</sub>之间的15年，其核苷酸替代模型为HKY<sub>2</sub>。
最后，T<sub>2</sub>到最后为第三个时期，其替代模型为HKY<sub>3</sub>。

### 转换BEAST XML为'世代'分析

打开之前在文本编辑器中生成的文件。
切换到定义到HKY模型的模块。
我们需要为每个时期设置3个独立的模型，分别为HKY<sub>1</sub>，HKY<sub>2</sub>和HKY<sub>3</sub>，因此删除此模块并粘贴到此处:

```xml
  <hkymodel id="hky.1">
    <frequencies>
      <frequencymodel idref="freqModel">
    </frequencymodel></frequencies>
    <kappa>
      <parameter id="kappa.1" lower="0.0" upper="100.0" value="1.0">
    </parameter></kappa>
  </hkymodel>

  <hkymodel id="hky.2">
    <frequencies>
      <frequencymodel idref="freqModel">
    </frequencymodel></frequencies>
    <kappa>
      <parameter id="kappa.2" lower="0.0" upper="100.0" value="1.0">
    </parameter></kappa>
  </hkymodel>

  <hkymodel id="hky.3">
    <frequencies>
      <frequencymodel idref="freqModel">
    </frequencymodel></frequencies>
    <kappa>
      <parameter id="kappa.3" lower="0.0" upper="100.0" value="1.0">
    </parameter></kappa>
  </hkymodel>
```

组成世代模型的每个模型现在都可用，因此我们现在可以设置世代模型本身。
以下为粘贴的3个模块:

```xml
 <epochBranchModel id="epochModel">
    <treeModel idref="treeModel"/>
    <epoch id="epoch1" transitionTime="10">
      <hkyModel idref="hky.1"/>
    </epoch>
    <epoch id="epoch2" transitionTime="15">
      <hkyModel idref="hky.2"/>
    </epoch>
    <hkyModel idref="hky.3"/>
 </epochBranchModel>
```

每个世代模块参考其相关的模型和与其符合的过渡时间，最后一个(如过去最远的)模型是没有范围的。
现在转换到定义的位点模型的XML行。
我们需要参考我们刚才定义的世代模型作为我们选择的替代模型。
改变对应行后看起来像这样:

```xml
 <siteModel id="siteModel">
    <branchSubstitutionModel>
      <beagleSubstitutionEpochModel idref="epochModel"/>
    </branchSubstitutionModel>
 </siteModel>
```

现在转到XML的treeLikelihood模块。该模块也需要参考世代模型。
改变相关行如下所示:

```xml
 <treeLikelihood id="treeLikelihood">
    <patterns idref="patterns"/>
    <treeModel idref="treeModel"/>
    <siteModel idref="siteModel"/>
    <beagleSubstitutionEpochModel idref="epochModel"/>
    <strictClockBranchRates idref="branchRates"/>
 </treeLikelihood>
```

由于我们有3个独立的κ参数，我们需要定义独立的转换内核和每个参数独立的先验分布。
转换到操作模块。
找到参考了κ(&kappa;)的参数模块。
删除该模块，粘贴到此处:

```xml
  <scaleOperator scaleFactor="0.75" weight="0.1">
    <parameter idref="kappa.1"/>
  </scaleOperator>

  <scaleOperator scaleFactor="0.75" weight="0.1">
    <parameter idref="kappa.2"/>
  </scaleOperator>

  <scaleOperator scaleFactor="0.75" weight="0.1">
    <parameter idref="kappa.3"/>
  </scaleOperator>
```

本分析的其它所有参数都是共有的，因此没有必要定义独立的转换内核。
现在查找先验模块，可以在mcmc模块中找到。
我们需要3个先验分布，每个&kappa;参数一个(在此示例中，我们假设对数正态分布的&kappa;参数):

```xml
  <logNormalPrior mean="1.0" stdev="2" offset="0.0" meanInRealSpace="true">
    <parameter idref="kappa.1"/>
  </logNormalPrior>

  <logNormalPrior mean="1.0" stdev="2" offset="0.0" meanInRealSpace="true">
    <parameter idref="kappa.2"/>
  </logNormalPrior>

  <logNormalPrior mean="1.0" stdev="2" offset="0.0" meanInRealSpace="true">
    <parameter idref="kappa.3"/>
  </logNormalPrior>
```

通常，我们想要记录世代模型的三个参数。为了监视分析，我们从屏幕日志开始。
查找以下行：

```xml
  <log id="screenLog" logEvery="5000">
```

找到引用了kappa参数的模块，并将这三个块粘贴到此处:

```xml
  <column label="kappa.1" sf="6" width="12">
    <parameter idref="kappa.1"/>
  </column>

  <column label="kappa.2" sf="6" width="12">
    <parameter idref="kappa.2"/>
  </column>

  <column label="kappa.3" sf="6" width="12">
    <parameter idref="kappa.3"/>
  </column>
```

我们仍然需要修改负责将世代模型的参数估计存储到文件中的行。
转到负责保存日志文件的日志模块的开始，如:

```xml
  <log id="fileLog" logEvery="1000">
```

找到引用k参数的行并将其粘贴到那里:

```xml
  <parameter idref="kappa.1"/>
  <parameter idref="kappa.2"/>
  <parameter idref="kappa.3"/>
```

我们完成了XML的编辑，可以继续执行分析。

### 进行MCMC推断

世代模型推断需要安装BEAGLE库，并对BEAST可见。
[该网站](https://github.com/beagle-dev/beagle-lib)介绍了不同平台BEAGLE库的安装和设置。

在UNIX/Linux环境，前部分编辑的XML文件可以通过以下命令行运行:

```bash
java -Djava.library.path=/usr/local/lib -jar beast.jar epochExample.xml
```

### 分析输出文件

分析结束后，可以将生成的日志文件导入选择的软件，如[Tracer](http://tree.bio.ed.ac.uk/software/tracer/) (分析MCMC运行生成的追踪文件的程序)。
图2展示了&kappa;参数的后验似然分布，这是我们此次分析主要感兴趣的。
我们清晰地看到的非重叠区，表明参数&kappa;<sub>1</sub>和&kappa;<sub>3</sub> 显著不同于参数&kappa;<sub>2</sub>。
第一个世代参数与第三个世代参数间可能没有明显地差异性。

{% include image.html file="epoch2.png" prefix="tutorials/epoch_models/" alt="Credibility intervals" caption="图2: &kappa;参数的95%置信区间。水平线表示真实值，黑色点表示 后验平均值。" %}

## 参考文献

F. Bielejec, P. Lemey, G. Baele, A. Rambaut, and M. A. Suchard (2014)) Inferring heterogeneous evolutionary processes through time: from sequence substitution to phylogeography. Syst. Biol. 63(4):493–504.

A. Drummond and M. Suchard (2010) Bayesian random local clocks, or one rate to rule them all. BMC Biology, 8(1):114.

M. Hasegawa, H. Kishino, and T. Yano (1985) Dating of the human-ape splitting by a molecular clock of mitochondrial DNA. J. Mol. Evol. 22:160–174.

{% include links.html %}
