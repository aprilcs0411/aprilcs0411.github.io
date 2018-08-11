---
layout: post
title: pipeline operatorsdate: 2018-08-10 04:00:00
tags: basic R
author: aprilcs0411
---





<p>管道函数，参考https://www.cnblogs.com/nxld/p/6078812.html 和 https://blog.csdn.net/fairewell/article/details/72878107。<br/>
％>％是dplyr包的管道函数，直接将前一步的结果作为参数传给下一步的函数，省略赋值和中介，减少内存消耗。
例子：
x %>% f(y) 等同于 f(x, y)
y %>% f(x, ., z) 等同于 f(x, y, z )
默认作为函数的第一参数。或者用占位符“.”指定其位置。
假设有数据data如下：
</p>
<pre>
head(data)[1:2]
##          date hour min second event
## 1: 2016-11-01    7  30     29     u
## 2: 2016-11-02    9  43     36     a
</pre>
<p>可合成新的列，如下操作：</p>
<pre>
dataNew <- data %>%unite(datehour, date, hour, sep = ' ') %>%unite(datetime, datehour, min, second, sep = ':')
dataNew[1:2,]
##                datetime event
##  1:  2016-11-01 7:30:29     u
##  2:  2016-11-02 9:43:36     a
</pre>
<p>或者</p>
<pre>
a<-1:1000 %>% abs %>% `*`(50)  %>%   matrix(ncol=100)  %>%  rowMeans %>% round %>%  `%%`(7) %>% hist
</pre>

<p>magrittr包主要提供了4个管道函数，除了向右传值的%>%，还有%T>% tee操作符，expose操作符%$%传递数据和属性名，复合赋值操作符 %<>% 将右边最后的操作结果赋给左边。除了%>%， 分别举例如下。</p>
<pre>
#%T>%
a<-1:1000 %>% abs %>% `*`(50)  %>%   matrix(ncol=100)  %>%  rowMeans %>% round %>%  `%%`(7) %T>% hist%>%sum
</pre>
<p>插播一下我的理解：hist画图函数不返回内容，传到sum里的，是传给hist的值。</p>
<pre>
#%$%
data.frame(x=1:10,y=rnorm(10),z=letters[1:10]) %$% .[which(x>5),]#注意占位符的使用
</pre>
<p>还有复合赋值%<>%，左侧既作为初始传值参数，也作为最后返回值。</p>
<pre>
x <- rnorm(100)
x %<>% abs %>% sort
</pre>
