---
title: 模型选择与检测
keywords: rates, dates, mcmc, tutorial
last_updated: August 29, 2017
tags: [tutorial, workshop]
summary: '本教程继续分析来自于教程(YFV)<a href="workshop_rates_and_dates">Estimating rates and dates from time-stamped sequences</a> 对黄热病毒(YFV)的分析。在此我们将使用BEAST的边际似然值估计对模型的选择和检测。我们将对较宽松分子钟和严格分子钟进行比较。'
sidebar: beast_sidebar
permalink: workshop_model_selection.html
folder: beast
---

{% capture root_url %}{{ site.tutorials_root_url }}/workshop_model_selection/{% endcapture %}

## 模型选择和比较

{% include note.html content='本教程源自于教程 <a href="workshop_rates_and_dates">Estimating rates and dates from time-stamped sequences</a> ，因此在开始之前应先完成它。' %}

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i> 数据文件为'<samp>YFV.nex</samp>'和<a href="{{ root_url }}files/YFV.nex">可以从此处下载</a>。</div>

## 评估速率变量(使用模型选择)

为了研究该数据文件的特定谱系的熟虑异质性，以及对分歧时间估计的影响，对于使用非相关对数正态分布的宽松分子钟进行分析的日志和树文件是可用的。

<div class="alert alert-success" role="alert"><i class="fa fa-download fa-lg"></i>
注意，您可以充分利用运行教程时间的BEAST的输出文件，该文件在之前的教程<a href="workshop_rates_and_dates"></a> (链长为20,000,000，每10,000次记录一次取样)。文件<a href="{{ site.tutorials_root_url }}/workshop_rates_and_dates/files/YFVLongRuns.zip"><samp>YFVLongRuns.zip</samp>可以从此处下载</a>。
</div>

除严格分子钟的日志文件外，还应将日志文件导入Tracer。
研究对数正态标准差的后验密度; 如果这个密度不包括零(=无速率变化)，表明可以排除严格分子钟，选择宽松分子钟。

更常规的测试可以使用边际似然估计来完成(MLE, Suchard et al., 2001, MBE 18: 1001-1013)，其使用混合的先验模型和后验取样(Newton and Raftery 1994)。
边际似然比定义贝叶斯因子，该因子根据手头的数据测量两种不同模型的相对拟合。然而，使用示Tracer无法准确估计边际可能性，已经多次证明了这一点(Baele et al., 2012, 2013, 2016)。

虽然调和平均数估变量(HME)仍然是获得边际似然估计的常用方法，这在很大程度上是因为它很容易计算，但它越来越被忽视为可靠的边际似然估计。比较人口统计和分子钟模型时，HME和sHME都被证明是不可靠的(Baele et al., 2012, 2013)。使用计算要求更高的方法可以获得更准确/可靠的MLE估计，例如:

路径采样(PS)
: 在20多年前的统计学文献中提出(有时也被称为'热力学整合')，这种方法已于2006年在植物遗传学中引入(Lartillot and Philippe 2006)。不是仅使用来自后验分布的样本，而是需要来自后验概率分布和先验之间的一系列加强后验的样本。
随着较强后验概率越接近先验，后验概率分布可用的数据越来越少，这可能导致在指定不正确的先验时出现不正确的结果。
因此，使用适当的先验对PS来说是最重要的(Baele et al., 2013, MBE, doi:10.1093/molbev/mss243)。

踏脚石采样 (SS)
: 基本上采用与PS相同的方法并使用来自一系列加强后验概率分布的相同样本集合，与PS相比，踩踏石采样(SS)产生更快收敛的结果。与PS的情况一样，使用适当的先验对SS来说至关重要。

广义踏脚石采样(GSS)
: GSS结合PS/SS的优势---它们可以产生相对可靠的边际似然值(log)---是HME/AICM方法的关键吸引人之处，例如，在探索后验概率时这些方法充分利用了采集的样本。
GSSGSS利用工作分布来缩短PS/SS整合的路径，并因此在后验和工作分布的乘积之间构建路径，从而避免探索先验和与此相关的数值问题(Baele et al., 2016, Syst. Biol., doi:10.1093/sysbio/syv083)。 <!-- 工作分布的解释? -->

上述PS，SS以及GSS 方法已经运用于BEAST(Baele at al., 2012, 2016)。
通常，在进行标准MCMC分析后执行PS/SS或GSS模型选择，然后从一系列加强后验概率分布中收集样本，之后可以从MCMC分析停止的地方开始(即，您应该已经运行MCMC分析足够长的时间以使其后验概率分布方向收敛)，从而消除了PS，SS和GSS首先向后验概率汇聚的需要。

正如本教程前面和贝叶斯模型选择理论中所讨论的那样，获得可靠的模型比较结果需要对所有被估计的参数使用适当的先验。
在进行模型比较时W，不正确的先验将导致不正确的贝叶斯因子。
此外，对于PS/SS程序，我们需要从系列加强后验概率分布结束时的对先验采样，如果没有适当的先验将会出现问题并且导致数值不稳定(Baele at al., 2013, MBE, doi:10.1093/molbev/mss243)。

### 设置PS/SS分析

要设置分析，请遵循在[Estimating rates and dates from time-stamped sequences](workshop_rates_and_dates) 教程中的BEAUti步骤，直到设置`MCMC`面板选项为止。
指定链长为<samp>1,000,000</samp>日志参数为每<samp>1,000</samp>步一次。

为了设置PS/SS分析，在`MCMC`面板选择`Marginal likelihood estimation (MLE):` `path sampling / stepping-stone sampling'作为我们将用于执行边际似然估计的技术:

{% include image.html file="selectPSSS.png" prefix=root_url width="90%" alt="select ps ss for marginal likelihood estimation" caption="" %}

点击`Settings`指定PS/SS设置。

由于时间限制，我们将收集来自11个加强后验后验概率分布的样本(例如，在1.0 和0.0之间的10个路径步骤)，因此设置`Number of path steps:`为<samp>10</samp>。加强后验概率分布链的长度可以与标准MCMC链的长度不同，但我们将其设置为<samp>100,000</samp>。

{% include image.html file="settingsPSSS.png" prefix=root_url width="90%" alt="ps ss marginal likelihood estimation settings" caption="" %}

不同加强后验分布的的强度用Beta分布的平均分位数定义(\\( \alpha \\) ,1.0)， 此处的\\( \alpha \\)等于0.30，正如踏脚石采样文章中(Xie et al. 2011)表明的那样，此方法优于路径采样文章中的均匀传播(Lartillot and Philippe 2006)。

注意，在MLE面板中还有另一个选项: `Print operator analysis`。
选中后，此选项将在屏幕上显示的每个加强后验概率分布的采样模块分析，然后可用于发现在整个从后验到先验的路径中采样模块性能方面的潜在问题。
当使用高度复杂的模型以及获得了不可能的结果时，此选项很有用。

设置好PS/SS和适当的先验后，我们可以写入xml并在BEAST中运行分析。
对严格分子钟和不相关的宽松分子钟使用相同的设置，进行运行分析。

{% include question.html content="对于严格分子钟模型的log边际似然值是什么? 以及宽松分子钟模型呢?这些如何与您同伴的结果相比较呢?从这些(非常)短的PS/SS分析中能得出什么?" %}

**重要:** 为了使用PS/SS获得对边际似然的可靠估计，我们需要使用更加苛刻的计算设置来重新运行这些分析。
例如，通过将路径步数设置为50，并将每个加强后验概率率的MCMC链的长度设置为500,000(记录频率也可以增加)。
初始标准MCMC链的长度也应增加，以确保在开始PS/SS计算之前朝后验概率收敛。
此处起始链长5,000,000代已经足够。
请注意，使用这些设置，边际似然估计将大约花费完成此数据的25,000,000代标准MCMC运行所需的时间(初始链为+ 5,000,000 代)。
由于时间限制，我们现在步运行全长分析(虽然在空闲的计算机时间或在夜间运行期间可以尝试更苛刻的计算设置)。

{% include important.html content="将PS/SS/GSS分析的输出文件加载到Tracer中是没有意义的，因为这些文件包含一系列MCMC分析(即一系列后验概率强度)的输出结果。" %}

**注意:** 早期PS/SS/GSS在BEAST的应用中，估计的边际似然值日志不会保存到磁盘。
从技术上讲，当实际的MLE丢失但日志文件仍然可用时，可以使用简单的XML文件(与重新运行整个分析相比，这是更好的选择)。
然而，最新版本的BEAST将最终结果保存到指定的文件名，因此即使关闭终端或不再提供标准输出，也可以轻松访问此结果。

在完整的严格分子钟分析中，对于分别使用PS和SS的对数边际似然值分析，得出-5676.04和-5676.10。
对于非相关的宽松分子钟分析，同样的估计参数得出值分别为-5656.8和-5658.14。

{% include question.html content="这些MLE与使用PS/SS获得的MLE相比如何，以及由这些不同估计量得出的贝叶斯因子如何?在PS估计和SS估计的log边际似然值中观察到了哪些差异 (以及与运行较短链长相比)?" %}


### 设置GSS分析

要设置GSS分析，我们可以返回BEAUti中的MCMC面板，然后选择'generalized stepping-stone sampling'作为在MCMC面板中执行的'Marginal likelihood estimation (MLE)'。

{% include image.html file="selectGSS.png" prefix=root_url width="90%" alt="select gss for marginal likelihood estimation" caption="" %}

点击'settings'指定GSS设置。
由于时间的限制，我们将标准MCMC链的长度设置为1,000,000，并从11个后验收集样本(即介于1.0和0.0之间的10个路径步骤)。
用于后验的链的长度可以与标准MCMC链的长度不同，但是我们在此处也将其设置为100,000。
我们再次使用Beta(0.3,1.0)分布的平均分位数来定义不同后验概率的强度，因为事实证明，该设置优于对于一般的垫脚石采样而言的一致性扩散(Baele et al., 2016)。

{% include image.html file="settingsGSS.png" prefix=root_url width="90%" alt="gss marginal likelihood estimation settings" caption="" %}

模型参数的工作先验/分布会根据其域自动生成，但是在选择树形拓扑的工作先验时，我们需要做出选择。
对于参数化聚合模型，例如恒定人口规模模型，最快的方法是使用'matching coalescent model'；而对于非参数化聚合模型，例如贝叶斯Skygrid模型，通用的'product of exponential distributions'是唯一合适的选择。
此外，对于一些物种模型(通常在使用同时/等时序列时时)，'matching speciation model' 是唯一合适的选择。

{% include question.html content="对于严格的分子钟分析，我们使用GSS达到-5696.32，而对于不相关的宽松分子钟分析，我们使用GSS得到-5680.06。这些MLE与使用PS/SS获得的MLE相比如何，以及由这些不同估计量得出的贝叶斯因子如何?" %}

所需的后验强度以及其链长是实现可靠估计(log)边际似然值的重要设置。
然而，这些设置取决于要分析的数据集，因此在执行模型比较时需要进行不同的PS/SS/GSS分析(具有不同的计算设置)。
强烈建议使用PS/SS/GSS并通过增加计算设置来估计MLE，直到这些值不再显着变化为止，以确保收敛到正确的MLE。

如前所述，本教程中使用的模型拟合估计量不限于可以比较的特定类型的模型。
因此，除了将不同的时钟模型相互比较之外，我们可以使用相同的方法将不同的进化替代模型相互比较，或者将不同的人口统计学模型进行比较，...
例如，尝试比较核苷酸替代的HKY模型的模型拟合，迄今为止，我们一直在使用GTR核苷酸取代模型。

## 参考文献

Baele G., Lemey P., Bedford T., Rambaut A., Suchard M.A., Alekseyenko A.V. (2012) Improving the accuracy of demographic and molecular clock model comparison while accommodating phylogenetic uncertainty. Mol. Biol. Evol. 29:2157-2167.

Baele G., Li W.L.S., Drummond A.J., Suchard M.A., Lemey P. (2013) Accurate model selection of relaxed molecular clocks in Bayesian phylogenetics. Mol. Biol. Evol. 30:239-243.

Baele, G., Lemey, P., Suchard, M. A. (2016) Genealogical working distributions for Bayesian model testing with phylogenetic uncertainty. Syst. Biol. 65(2), 250-264.

Lartillot N., Philippe H. (2006) Computing Bayes factors using thermodynamic integration. Syst. Biol. 55:195-207.

Newton M.A., Raftery A.E. (1994) Approximating Bayesian inference with the weighted likelihood bootstrap. J. R. Stat. Soc. B. 56:3-48.

Suchard M.A., Weiss R.E., Sinsheimer J.S. (2001) Bayesian selection of continuous-time Markov chain evolutionary models. Mol. Biol. Evol. 18:1001-1013.

Xie W., Lewis P.O., Fan Y., Kuo L., Chen M.H. (2011) Improving marginal likelihood estimation for Bayesian phylogenetic model selection. Syst. Biol. 60:150-160.


## 帮助文档

BEAST 网址: [http://beast.community](http://beast.community)

教程: [http://beast.community/tutorials](http://beast.community/tutorials)

常见问题: [http://beast.community/faq](http://beast.community/faq)


{% include links.html %}
