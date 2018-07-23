---
layout: post
title: first post
date: 2018-07-22 04:00:00
tags: exploration R
author: aprilcs0411
---





   
<p>数据挖掘的简单流程包括以下部分：</p>
<ol>
<li>数据获取：爬虫等途径获取数据</li>
<li>数据预处理：数据清洗、数据变换、数据描述</li>
<li>特征工程：特征构建、特征选择、特征提取</li>
<li>数据建模</li>
<li>预测或分析</li>
</ol>
数据获取暂时略过，爬虫方面R和Python都有很多好用的包，尤其是Python的Beautiful Soap，很方便使用了。<br/>
数据预处理方面，很多dirty work要做，但是对最终的挖掘结果具有重要影响。预处理结束的数据，才是干净可用的。如果数据本身存在问题，那就是GIGO。<br/>
很多人所谓的数据挖掘，其实就是在做特征工程。由此可见，好的数据特征非常重要。<br/>
数据探索包含了预处理和特征工程的内容，从各个维度分析数据，是很有意思的内容。<br/>

<h3 >1.数据加载</h3>
快速读取数据的包 readr和readxl。read_table函数可替代read.table函数
<ol>
<li>数据格式未知的情况
如果数据量大，不方便打开文件，那么可读取数据前几行，了解数据格式，从而依据下一情况进行处理。R中读取文件的方法很多，包括：
<ul>
<li>read.table函数，nrows参数指定读取行数。不限文件类型。</li>
<li>readLines函数，n参数指定读取函数。不限文件类型。</li>
<li>scan函数，nlines参数指定读取行数。不限文件类型。(what=“”转化成字符型读取,na.string='NULL'等设置技巧)</li>
<li>read.xlsx函数，包openxlsx。startRow指定开始行,rows向量指定读取行。读取xlsx文件。</li>
</ul>
</li>
<li>数据格式已知的情况
<ul>
<li>读取指定列：<code>read.table("filename",col.names = c("id","v1","v2","v3"),colClasses = c('character','NULL','character','NULL'))</code></li>
</ul>
</li>
</ol>
<h3>2.数据查看与简单处理</h3>
<p>常用包：Hmisc、pastecs和psych查看数据统计信息；ggplot2画图。</p>
<ol>
<li>转化为因子类型，dummy coding
</li>
<li>查看数据（一列或多列）
<ul>
<li>summary(dataframe[vector_colnames])数值型</li>
<li>table(dataframe$colname)；prop.table(exist_table)分类型</li>
<li>library(Hmisc); describe(dataframe[vector_colnames])数值型</li>
<li>library(pastecs);stat.desc(dataframe[vector_colnames])数值型</li>
<li>library(gmodels);CrossTable(df$col1,df$col2)二维列联表</li>
<li>xtabs()多维列联表,margin.table(tablename,1)按行1计数，按列2计数，addmargins（tablename,1），将margin放入表中</li>
<li>boxplot(dataframe$colname,main="title",ylab="colname")数值型</li>
<li>hist(dataframe$colname,mian="title",xlab="colnames")数值型</li>
<li>发现离群点: car包中的outlierTest（)；学生化残差图</li>
<li>发现高杠杆点:1)残差-杠杆图,car包的crPlots（）;2)hat统计量hatvalues(fit_model)，2-3倍即可认为是高杠杆点</li>
<li>发现强影响点：car包中的influencePlot（fit_model）函数，将离群点、杠杆点、强影响点一起发现。</li>
</ul>
</li>
<li>查看数据间的关系
<ul>
<li>列联表,如上。</li>
<li>plot(x=df$col1,y=df$col2,main="title",xlab=colname1,ylab=colname2,)</li>
<li>使用by()函数分组计算描述性统计量。doBy包中的summaryBy函数也可以分组计算。</li>
<li>发现相关性
<ul>
<li>分类变量：</li>
卡方检验chisq.test(table)，数据量少时有可能不准确。对于四格表数据(二维列联表)，使用卡方检验的条件为样本量大于 40，且最小理论频数应大于 5。p越小，相关性越显著。<br/>
fisher检验fisher.test(table)，适用小样本量，行数>2。p越小，相关性越显著。
<li>数值变量：</li>
方差：var(x, y = NULL, na.rm = FALSE, use)<br/>
协方差：cov(x, y = NULL, use = "everything",    method = "pearson")，method还有 "kendall", "spearman"<br/>
相关系数：cor(x, y = NULL, use = "everything",    method = "pearson")，method还有 "kendall", "spearman"<br/>
偏相关：控制一个或多个定量变量时，分析另外两个定量变量间的关系。ggm包中的pcor函数。pcor(u,s)u<-vector(a1,a2,b1,b2,……)a1,a2是需要分析的两个变量下标，b系列是需要控制的变量下标。s为协方差矩阵s<-cov（dataframe）<br/>
相关显著性检验：cor.test()函数，p越小，越相关。psych包中的corr.test(dataframe,use="complete（缺失值行删除）"，method=“person”)函数可以同时进行多种相关性检测。<br/>
t检验：用于检验两个数据集的均值差异是否显著。t.test(x,y)（样本服从正态分布，两组数据总方差相等，适用于样本量较少的情况。p越小，差别越显著。如果数据量分别大于100个，不需要做方差齐性检验。否则需要检验，且方差不齐需用t'检验）多数据集表现是否一致，可用aov（y~x）x为分组变量。多重比较TukeyHSD（aov（y~x））两两分析。multcomp包中的glht函数也可以。不确定是否方差齐性，可用wilcox.test()。
F检验：方差齐性检验。F检验对于数据的正态性非常敏感，Levene检验, Bartlett检验或者Brown–Forsythe检验的稳健性都要优于F检验。
</ul>
</li>
<li>发现协同效应（交互作用）: effects包中的effect函数，plot(effect("interact_terms"，fit_model, list(one_term=vector_contorl_parameter)),multiline=TRUE)</li>
<li>发现共线性：car包中的vif函数，vif（fit_model）,vif值比4大，认为可能存在共线性。可删除变量，或者岭回归，或者将共线性变量合成一个变量</li>
</ul>
</li>
<amp-img src="{{ site.baseurl }}assets/images/methods.jpg" width="406" height="281" layout="responsive" alt="" class="mb3"></amp-img>
<li>查看缺失值
<ul>
<li>sum[is.na(frame$col)]，计算缺失值数目，mean(is.na(fame$col))，计算缺失值比例</li>
<li>dataframe[!complete.cases[dataframe],]，列出有缺失值的行，mean（！complete.cases(dataframe)）含缺失值的样本比例</li>
<li>表格展示缺失值格式，mice包中的md.pattern(dataframe)</li>
<li>图形展示缺失值格式，VIM包的aggr(dataframe),matrixplot(dataframe)</li>
<li>查看缺失值的相关性，x<-as.data.frame(abs(is.na(dataframe)))转换为0，1框, y<-x[which(apply(x,2,sum)>0)]，提取包含缺失值的属性列(2,按列)，cor(y)，属性缺失的相关系数</li>
</ul>
</li>
<li>缺失值处理
<ul>
<li>完整行删除：na.omit(dataframe); newdata<-oldframe[complete.cases(oldframe),]</li>
<li>多重插补，mice包利用链式方程的多元插补，返回5个完整数据集，with（）对每个数据集应用统计模型，pool()函数将5个数据集的分析结果整合为一个输出结果。<br/>
<pre>
library(mice)
imp<-mice(data,m)  # 默认m=5
fit<-with(imp,methods)  # methods 是分析方法，包括lm（），glm（）nbrm（）等, fit为列表，包含m个分析结果。
pooled<-pool（fit）
summary(pooled)
</pre>
mi和Amelia包也有其他函数支持。<br/>
其他专业插补方法包括：mvnmle，cat，seqknn，arrayImpute，arrayMissPattern，longitudinalData等……
</li>
</ul>
</li>
<li>简单插补，用均值、中位数或众数插补。但引入偏差，尽量避免。</li>
<li>数据变换：根据需要进行数据标准化和类型变换以及删除等。
<ul>
<li>发现错误数据，可以合理删除。但通常，离群点有可能意味着模型的不完善。</li>
<li>模型违反正态假设，可对响应变量进行变换。car包中的powerTransform()</li>
<li>根据残差分析，发现非线性关系，可对变量进行变换。boxTidwell()函数</li>
</ul>
</li>
<li>回归模型中的变量选择
<ul>
<li>anova()比较嵌套模型，判断变量参数是否必要</li>
<li>AIC()比较模型，AIC值更小更好，赤池信息准则</li>
<li>变量逐步选择，向前、向后、前后逐步，MASS包中的stepAIC(fit_model，direction="")</li>
<li>全子集回归，leaps包中的regsubsets（）慢……暴力比较。算是辅助手段吧。</li>
<ul>
</li>
<li>功效分析：实验结果效用or需要样本数量。pwr包中的各种函数</li>
</ol>


