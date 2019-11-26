---
title: 分析连续性特征
keywords: beast, tutorial, continuous, traits
last_updated: July 22, 2017
tags: [tutorial]
summary: "如何联合估计分子进化树以及其形态学特征的共进化形式。"
sidebar: beast_sidebar
permalink: continuous_traits_chs.html
folder: beast
redirect_from: "/continuous-traits"
---

## 载入序列数据

为了载入NEXUS格式比对文件，可以通过`File`菜单选择`Import Data...`选项。
本教程可以选择BEAST软件包的examples/continuousTraits/DarwinsFinches.nex文件。
一旦加载数据面板将如图所示:

{% include image.html prefix="tutorials/continuous_traits/" file="image1.png" %}

##载入特征数据

接下来从examples/continuousTraits/DarwinsFinchesTraits.txt载入特征数据。
该文件为制表符文件，第一列为分类名(与序列数据相吻合)， 随后每一列为特征值。
第一行为特征名(第一列第一行将会被忽略，因此可以空着)。

数据如下所示:

{% include table.html content="
|  | wingL | tarsusL | culmenL | beakD | gonysW|
|--:|:--|
| Geospiza magnirostris | 4.4042 | 3.03895 | 2.724667 | 2.823767 | 2.675983 |
| Geospiza conirostris | 4.349867 | 2.9842 | 2.6544 | 2.5138 | 2.360167 |
| Geospiza difficilis | 4.224067 | 2.898917 | 2.277183 | 2.0111 | 1.929983 |
| Geospiza scandens | 4.261222 | 2.929033 | 2.621789 | 2.1447 | 2.036944 |
| Geospiza fortis | 4.244008 | 2.894717 | 2.407025 | 2.362658 | 2.221867 |
| Geospiza fuliginosa | 4.132957 | 2.806514 | 2.094971 | 1.941157 | 1.845379 |
| Cactospiza pallida | 4.265425 | 3.08945 | 2.43025 | 2.01635 | 1.949125 |
| Certhidea olivacea | 3.975393 | 2.936536 | 2.051843 | 1.191264 | 1.401186 |
| Camarhynchus parvulus | 4.1316 | 2.97306 | 1.97442 | 1.87354 | 1.81334 |
| Camarhynchus pauper | 4.2325 | 3.0359 | 2.187 | 2.0734 | 1.9621 |
| Pinaroloxias inornata | 4.1886 | 2.9802 | 2.3111 | 1.5475 | 1.6301 |
| Platyspiza crassirostris | 4.419686 | 3.270543 | 2.331471 | 2.347471 | 2.282443 |
| Camarhynchus psittacula | 4.23502 | 3.04912 | 2.25964 | 2.23004 | 2.07394 |
" caption="DarwinsFinchesTraits.txt" %}

实际文件看起来是这样：第一列为物种名，第一行为特征名。
每一个项目由制表符分隔:

```
	wingL	tarsusL	culmenL	beakD	gonysW
Geospiza magnirostris	4.4042	3.03895	2.724667	2.823767	2.675983
Geospiza conirostris	4.349867	2.9842	2.6544	2.5138	2.360167
Geospiza difficilis	4.224067	2.898917	2.277183	2.0111	1.929983
Geospiza scandens	4.261222	2.929033	2.621789	2.1447	2.036944
Geospiza fortis	4.244008	2.894717	2.407025	2.362658	2.221867
Geospiza fuliginosa	4.132957	2.806514	2.094971	1.941157	1.845379
Cactospiza pallida	4.265425	3.08945	2.43025	2.01635	1.949125
Certhidea olivacea	3.975393	2.936536	2.051843	1.191264	1.401186
Camarhynchus parvulus	4.1316	2.97306	1.97442	1.87354	1.81334
Camarhynchus pauper	4.2325	3.0359	2.187	2.0734	1.9621
Pinaroloxias inornata	4.1886	2.9802	2.3111	1.5475	1.6301
Platyspiza crassirostris	4.419686	3.270543	2.331471	2.347471	2.282443
Camarhynchus psittacula	4.23502	3.04912	2.25964	2.23004	2.07394
```

从文件菜单选择`Import Traits...`选项。
一旦加载特征面板如下所示:

{% include image.html prefix="tutorials/continuous_traits/" file="image2.png" %}

接下来需要将每个独立的特征合并为多元数据分区。
选择表格左侧的所有5个特征，点击`Create partition from trait...`按钮。
您将会被询问为此分区命名。
将其命名为'traits' (或者其它合适的):

{% include image.html prefix="tutorials/continuous_traits/" file="image3.png" %}

## 设置模型

切换到位点面板选择每个分区的进化模型。
应该会看到2个数据分区，'DarwinsFinches'(我们首次加载的核苷酸数据)，和'traits'（我们刚才从5个特征中创建的多元特征分区）(将会在数据面板的数据表格看到这2个分区)。
为序列选择合适的核苷酸模型:

{% include image.html prefix="tutorials/continuous_traits/" file="image4.png" %}

接下来选择'traits'分区，选择连续性特征进化模型(本例中为同质性布朗运动模型):

{% include image.html prefix="tutorials/continuous_traits/" file="image5.png" %}

然后选择模型的其它方面，诸如树先验模型(如Yule模型):

{% include image.html prefix="tutorials/continuous_traits/" file="image6.png" %}

为输出文件选择合适的名字根以及合适的链长:

{% include image.html prefix="tutorials/continuous_traits/" file="image7.png" %}

## 分析数据

然后生成XML文件，并将其加载入BEAST运行:

{% include image.html prefix="tutorials/continuous_traits/" file="image8.png" %}

可以通过Tracer和TreeAnnotator/FigTree的结合来分析结果。首先将log文件载入Tracer:

{% include image.html prefix="tutorials/continuous_traits/" file="image9.png" %}

如上所示，标记为colXY的迹线为特征X和Y的精到或共精度(以他们在BEAUti中的顺序出现)。
因此，对于鸟类数据，col11是一个特征'wingL'的精度。
选择所有的精度(如col11，col22 等) ， 我们可以看到在特征中有多少变量(精度是方差的倒数，数值越大，方差越小)。
查看所有的共精确度，很明显我们可以看到特征4和特征5(beakD和gonysW)的共精度为较强的负值。
这与较强的负相关性有关(方差/协方差矩阵是精度矩阵的倒数)。

您也可以通过TreeAnnotator来构建一个总的MCC树，然后通过FigTree可视化每个特征的各维度(如，为分支着色)。目前我们还没有可视化多元变量的软件，但是希望未来我们可以提供。

{% include links.html %}
