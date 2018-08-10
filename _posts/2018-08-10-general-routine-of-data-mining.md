---
layout: post
title: General Routine of Data Mining
date: 2018-08-10 04:00:00
tags: basic routine python
author: aprilcs0411
---





   
<p>数据挖掘基本流程，全文参考&翻译 http://blog.kaggle.com/2016/07/21/approaching-almost-any-machine-learning-problem-abhishek-thakur/</p>
<h2 id="heading1"><a href="#basic">1.Data</a></h2>
<h2 id="heading2"><a href="#b2">2.Label Types</a></h2>
<h2 id="heading3"><a href="#b3">3.Evaluation Matrics</a></h2>
<h2 id="heading4"><a href="#b4">4.Libraries</a></h2>
<h2 id="heading5"><a href="#b5">5.Machine Learning Framework</a></h2>

<h3 id="basic" name="basic">1.Data</h3>
<p>数据处理流程包括：数据清理、转化等(也就是ETL)，变为带label的表格型数据。</p>

<h3 id="b2" name="b2">2.Label Types</h3>
<p>经过数据转化，label可以有多种形式：</p>
<ul>
<li>单列的二进制值，二分类问题，每个样本只属于唯一一类，只有两类。</li>
<li>单列的实数值，回归问题，预测一个实数值。</li>
<li>多列的二进制值，多类别分类问题，multi-class，每个样本只属于一个分类，但存在多个分类。</li>
<li>多列的实数值，回归问题，预测多个值。</li>
<li>多列的二进制值，多标签分类问题，multi-label，每个样本可属于多个分类。</li>
</ul>

<h3 id="b3" name="b3">3.Evaluation Matrics</h3>
<p>评价标准或者说优化目标。偏斜二元分类（skewed binary classification）主要追求ROC，AUC的面积最大化。多分类问题，追求分类熵最小化。回归问题可能追求最小残差。</p>

<h3 id="b4" name="b4">4.Libraries</h3>
<p>常用library</p>
<ol>
<li>数据查看与处理，pandas。</li>
<li>机器学习模型，scikit-learn。</li>
<li>gradient boost：xgboost</li>
<li>神经网络：keras。</li>
<li>查看进程：tqdm。</li>
</ol>


<h3 id="b5" name="b5">5.Machine Learning Framework</h3>
<p>原图非常经典，附在下面。</p>
<amp-img src="{{ site.baseurl }}assets/images/p1.png" width="800" height="600" layout="responsive" alt="" class="mb3"></amp-img>
<p>粉色线路是一个基本流程。首先确定问题是分类还是回归问题。随后将数据分为训练集和验证集(training set , validation set)。数据集的分割必须保证label的均衡，即分层抽样。python中scikit-learn包可用。注意训练集和验证集不能重合，避免过拟合。</p>
<p>下一步是变量处理。以titanic数据集为例。survival是最终需要判断的标签。上一步根据survival的值分割数据。剩下的变量中，类别型的有：passenger class，sex和embarked。数值型变量包括age，sibsp，parch，ticket，fare，cabin。name变量是text型。</p>
<p>先处理数值型数据。可进行归一化处理。normalization。</p>
<p>类别型数据处理方法有两种：1.labelencoder；2.one-hot encoding</p>
<p>文本型数据可用字符数或关键字数量进行量化。sklearn.feature_extraction.text中可用CountVectorizer和TfidVectorizer。tfid通常比count好用，作者给出了常用的参数。见下图。</p>
<amp-img src="{{ site.baseurl }}assets/images/p2.png" width="800" height="166" layout="responsive" alt="" class="mb3"></amp-img>
<p>下一步进入特征叠加，stacker。在这一步进行特征的combine。我理解，这一步主要完成特征分析转换，完成特征选择的一部分工作。根据特征稀疏与否，可以用numpy hstack，sparse stack。或者可以先用PCA和feature selection完成特征构建，再利用featureunion实现特征的combine。附图如下。</p>
<amp-img src="{{ site.baseurl }}assets/images/p3.png" width="800" height="228" layout="responsive" alt="" class="mb3"></amp-img>
<p>然后进行模型构建。如果数据未进行归一化处理，可使用各类树模型。归一化之后，可采用线性模型。归一化方法只适用于dense的特征，如果特征稀疏，通常归一化处理后也无法获得提升。如果此步得到较好的模型，可进一步优化超参。如果模型差强人意，需要进一步优化，例如，PCA，LDA，QDA和SVD方法再度回顾特征。</p>
<p>如果是高维数据，通常用PCA降维。图像处理通常从10-15开始逐步提高components的数目，直到性能提升不明显。其他类型数据，通常取50-60个成分。对于数值型数据，如果能避免pca，就尽量用原值进行分析（避免数值变形？原因是？）</p>
<p>文本型数据，先转化成sparse matrix，然后SVD分解。truncatedSVD在scikit-learn中包含。SVD的componets数目通常在120-200。</p>
<p>数值型数据需进行缩放处理，以适应线性模型。归一化或缩放之后，数据可以进入模型构建和特征选择部分。</p>
<p>特征选择有多种方法。(这一步的特征选择和特征combine步骤不同，上一步主要是变量的分析和变形，以便进入模型训练阶段。这一步是选择特征，以提高泛化能力。)最常见的是贪婪选择：前向后向特征选择。另外可以通过logit model 的coefficients和随机森林的特征来选择最优参数。这样的话，人工工作比较多。特征选择还可以用gradient boosting实现，作者推荐xgboost，更快更鲁棒。稀疏数据集的参数选择推荐用randomforestclassifier/randomforestregressor和xgboost结合。另外对于正值的系数矩阵，可以chi-2，在sklearn包里。</p>
<p>为避免过拟合，保持较少的estimator和较少的超参优化配置。</p>
<p>最后进入模型选择和超参优化。见下图。RS*为random search，值不太确定。</p>
<amp-img src="{{ site.baseurl }}assets/images/p4.png" width="785" height="800" layout="responsive" alt="" class="mb3"></amp-img>