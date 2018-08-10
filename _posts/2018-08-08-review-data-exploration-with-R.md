---
layout: post
title: review data exploration with R

date: 2018-08-08 04:00:00
tags: graph R
author: aprilcs0411
---





   
<p>跟着dataming with R这本书再复习一下数据查看的常用操作。</p>
<h2 id="heading1"><a href="#basic">1.basic dataset information</a></h2>
<h2 id="heading2"><a href="#b2">2.explore individual variables</a></h2>
<h2 id="heading3"><a href="#b3">3.explore multiple variables</a></h2>
<h2 id="heading4"><a href="#b4">4.Libraries</a></h2>






<h3 id="basic" name="basic">1.basic dataset information</h3>
<p>查看数据维度和变量名，以及基本描述信息等。</p>
<ul>
<li>dim(df)，维度。</li>
<li>names(df)，列名，即变量名。</li>
<li>str(df)，查看数据及样例。</li>
<li>attributes(df)，基本信息。</li>
<li>head(df)，tail(df)查看头尾数据。</li>
</ul>

<h3 id="b2" name="b2">2.explore individual variables</h3>
<p>查看数据的分布。</p>
<ul>
<li>summary(df)，数值型的最大、最小、中位、平均、四分值。quantile(df)类似。</li>
<li>hist(df$col)，plot(density(df$col))，数值型分布的频率直方图和密度图。</li>
<li>table(df$col)类别型的频率分布，可以pie(table())或者barplot(table())画出来。</li>
</ul>

<h3 id="b3" name="b3">3.explore multiple variables</h3>
<p>探索两变量之间的关系。</p>
<ul>
<li>cov(df$cols)，数值型的协方差。</li>
<li>cor(df$cols)，数值型的相关系数。</li>
<li>boxplot(df$numcol~df$factorcol)，不同类别的数值分布。</li>
</ul>



<h3 id="b4" name="b4">4.more explorations</h3>
<p>其他探索。</p>
<ul>
<li>pairs(df)，两两关系图。</li>
<li>scatterplot3d(x,y,z)，3d图，可额外添加参数设置颜色、点的类型等等，使用更多变量（变量太多也没有意义）。</li>
<li>parcoord(df$numcols,color=df$factorcol),MASS包。parallelplot(~df$numcols/df$factorcol),lattice包。平行参数可视化。</li>
<li>ggplot2包的多种图。</li>
</ul>

