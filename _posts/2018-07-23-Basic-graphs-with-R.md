---
layout: post
title: Basic graphs with R
date: 2018-07-23 04:00:00
tags: graph R
author: aprilcs0411
---





   
<p>我最初接触R，就是因为社交网络图画的太漂亮了~所以R画图的功能，必须仔细研究下。全文参考某书……笔记里没写书名……</p>
<h2 id="heading1"><a href="#basic">1.R基本画图</a></h2>
<h2 id="heading2"><a href="#chapter2">2.调整参数</a></h2>


<h3 id="basic" name="basic">1.R基本画图</h3>
<h4 id="c11" name="c11"> 1）基本函数plot() </h4>
<pre>
 plot (cars,
      type="p",
      main="title",
       sub="subtitle",
       xlab="x label",ylab="y label",
       xlim=c(2,26), ylim=c(0, 120), 
       xaxs="i",yaxs="i",
       col="blue",
       pch=19
 )
</pre>
<amp-img src="{{ site.baseurl }}assets/images/g1.jpg" width="320" height="200" layout="responsive" alt="" class="mb3"></amp-img>
<p>其中，type指定图的类型，参数有：</p>
<pre>
"p" for points,
"l" for lines,
"b" for both,
"c" for the lines part alone of "b",
"o" for both ‘overplotted’,
"h" for ‘histogram’ like (or ‘high-density’) vertical lines,
"s" for stair steps,
"S" for other steps, see ‘Details’ below,
"n" for no plotting.
</pre>
<p>还可继续在同一张图上添加点points()，线lines()</p>
<pre>
points(cars$dist~cars$speed,pch=1,cex=3)
</pre>
<amp-img src="{{ site.baseurl }}assets/images/g2.jpg" width="320" height="200" layout="responsive" alt="" class="mb3"></amp-img>

<h3 id="chapter2" name="chapter2">2.调整参数</h3>
<h4 id="c21" name="c21"> 1）颜色</h4>
<p>单个颜色可参考<a href="http://www.stat.columbia.edu/~tzheng/files/Rcolor.pdf">R颜色列表</a>。
一组颜色设置可利用调色板和包RColorBrewer。</p>
<ol>
<li>
自带调色板
<ul>
<li>col = rainbow(12),利用rainbow调色板产生12色，赋予col向量。alpha=1，不透明；0，透明</li>
<li>col = heat.colors(12),利用heat.colors调色板产生12色，赋予col向量。alpha同上。</li>
<li>col = terrain.colors(12),利用terrain.colors调色板产生12色，赋予col向量。alpha同上。</li>
<li>col = topo.colors(12),利用topo.colors调色板产生12色，赋予col向量。alpha同上。</li>
<li>col =  cm.colors(12),利用cm.colors调色板产生12色，赋予col向量。alpha同上。</li>
</ul>
</li>
效果和代码分别如下图所示：
<pre>
par(mfrow = c(1, 5))
pie(1:10, col = rainbow(10), main = "rainbow")
pie(1:10, col = heat.colors(10), main = "heat")


pie(1:10, col = terrain.colors(10), main = "terrain")


pie(1:10, col = topo.colors(10), main = "topo")


pie(1:10, col = cm.colors(10), main = "cm")


</pre>
<amp-img src="{{ site.baseurl }}assets/images/g3.jpg" width="555" height="131" layout="responsive" alt="" class="mb3"></amp-img>
<p>所以上图可更新为col=rainbow(50)，因为dim(cars)=(50,2)，所以50个颜色。<br/>
效果如下图：</p>
<amp-img src="{{ site.baseurl }}assets/images/g4.jpg" width="417" height="388" layout="responsive" alt="" class="mb3"></amp-img>
<li>
配置调色板<br/>
colorRamp()和colorRampPalette()都可以用来配置调色板。
</li>
<p>代码和效果如图所示</p>
<pre>
par(mfrow = c(1, 2))
p1 <- colorRampPalette(c("red", "blue"))
p2 <- colorRamp(c("red", "blue")) 
barplot(rep(1,10), col = p1(10), main = "colorRampPalette")
barplot(rep(1,10), col = p2(seq(0,1,len=10)), main = "colorRamp")
#p2配色用green，blue不行……
</pre>
<amp-img src="{{ site.baseurl }}assets/images/g5.jpg" width="389" height="231" layout="responsive" alt="" class="mb3"></amp-img>
<li>包RcolorBrewer<br/>
主要用到的函数是brewer.pal()；具体例子如下所示。<br/>
<pre>
require("RColorBrewer")
display.brewer.all(type = "seq") #序列型调色板，适用于从低到高排序明显的数据，浅色数字小，深色数字大。
display.brewer.all(type = "div") #离散型调色板，适合带“正、负”的，对极值和中间值比较注重的数据
display.brewer.all(type = "qual") #分类型颜色板比较适合区分分类型的数据。
plot(iris$Petal.Length, iris$Petal.With, col = brewer.pal(3, "Set2")[iris$Species], main = "iris")#set2为调色板的名称，从中选取3种颜色。
</pre>
</li>
</ol>

<h4 id="c22" name="c22"> 2）颜色配置</h4>
<ul>
<li>图片背景色 par(bg='gray')</li>
<li>仅坐标范围背景色x<-par("usr");rect(x[1],x[3],x[2],x[4],col="gray")# 注意传值顺序</li>
<li>设置各个单项的颜色 plot(y~x,col.axis="red",col.lab="blue",col.main="green");或者par（col.axis="",col.lab="",col.main=""）#应用到所有子图</li>
<li>利用颜色调色板，保存颜色向量，以备使用mycolor<-brewer.pal(7,"YlOrRd") #"调色板的名字"</li>
</ul>
<h4 id="c23" name="c23"> 3）字的配置</h4>
<ul>
<li>设置图中字体字号par(family="serif",font=2) #应用到所有子图。font：1，普通，2，加粗，3，斜体，4，加粗斜体。family可用names（pdfFonts（））查看</li>
<li>设置pdf中字体字号 pdf（family=""）</li>
</ul>
<h4 id="c24" name="c24"> 4）点的配置</h4>
<ul>
<li>画图同时配置，plot(,,,pch=19,cex=2),pch共25中图标选择，还有其他ASCII码等，可查看points函数</li>
<li>画点同时配置，points（data, pch=19,col=""，cex=vector,lwd=）col指点的边沿的颜色，bg指21-25型的填充颜色，cex指点的大小，lwd，画点的线宽。</li>
</ul>
<h4 id="c25" name="c25">  5）线的配置</h4>
<ul>
<li>画图同时配置，plot(,,,lty=0,lwd=2),lty共0~6种选择，还可以用名称指明，可查看lines函数</li>
<li>画线同时配置，lines(data, lty=4.lwd=2）</li>
</ul>
<h4 id="c26" name="c26">  6）图例的配置</h4>
<ul>
<li>在图上添加，点型，legend（“position", lenged=Vector_texts, cex=n,pch=vector_type，ncol=num_col）</li>
<li>在图上添加，线型，legend（“position", lenged=Vector_texts, lwd=n,lty=vector_type，ncol=num_col）</li>
<li>在图上添加，混合型，legend(x=-1, y=1.9,  
                                        c("sin", "cos", "tan"), 
                                        col = c(3, 4, 6),  
                                       text.col = "green4", 
                                       lty = c(2, -1, 1),  
                                        pch = c(NA, 3, 4), 
                                      merge = TRUE, 
                                       bg = "gray90")
</li>
</ul>
<h4 id="c27" name="c27">7）坐标轴的配置</h4>
<ul>
<li>axis(side=1, at=1:10, labels=letters[1:10].col="blue",col.ticks="yellow") #at表明数目，labels是新的坐标轴标记。分别是坐标轴的颜色和上面的竖线的颜色……</li>
<li>plot(rnorm(100),xaxp=c(0,100,10),col.axis="red")#灵活性不足</li>
<li>对数坐标轴，plot(10^c(1:5),log="y",type="b")</li>
</ul>
<h4 id="c28"  name="c28"> 8）子图的配置</h4>
<ul>
<li>par（fin=c(6,6),pin=c(4,4)）</li>
<li>par(mai=c(1,1,1,1),omi=c(0.1,0.1,0.1,0.1))</li>
</ul>