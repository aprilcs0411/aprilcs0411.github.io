---
layout: post
title: unbalanced data

date: 2018-08-09 04:00:00
tags: graph R
author: aprilcs0411
---





   
<p>类别不平衡数据的处理方法，主要用library(caret)，采用过采样或欠采样方法。翻译https://shiring.github.io/machine_learning/2017/04/02/unbalanced</p>
<h2 id="heading1"><a href="#basic">1.why？</a></h2>
<h2 id="heading2"><a href="#b2">2.how？</a></h2>
<h2 id="heading3"><a href="#b3">3.other solutions</a></h2>
<h2 id="heading4"><a href="#l4">4.compare</a></h2>





<h3 id="basic" name="basic">1.why？</h3>
<p>不平衡数据的成因和后果。</p>
<p>分类问题中出现的不同类别的样本数量差异较大的现象，容易造成模型学习的分类错误。极端例子，90%的健康人和10%病人的分类问题，模型一律判断为健康，仍有90%的准确率。所以需要调整数据集或者调整错误代价。</p>

<h3 id="b2" name="b2">2.how？</h3>
<ul>
<li>欠采样方法：随机从较多样本的类别中选取较少的样本数，以匹配本来就只有少量样本的类别。缺点是可能丢失一些未选中的样本上的信息。</li>
<li>过采样方法：随机从较少样本的类别中重复选取更多的样本，以匹配原来样本数较多的类别。缺点是可能过拟合？（我理解缺点是可能放大某些参数的作用，导致模型不准确，的确是过拟合了训练数据集。）</li>
</ul>
<p>实际应用中，不能简单过采样或欠采样，需要在交叉验证的每一折上分别进行过采样或欠采样以评估性能。</p>

<h4 id="b4" name="b4">1)basic model</h4>
<p>首先划分数据。</p>
<pre>
set.seed(42)
index <- createDataPartition(bc_data$classes, p = 0.7, list = FALSE)#以class分层采样，训练数据70%。
train_data <- bc_data[index, ]
test_data  <- bc_data[-index, ]
</pre>
<p>模型训练。</p>
<pre>
set.seed(42)
model_rf <- caret::train(classes ~ .,
                         data = train_data,
                         method = "rf",#随机森林
                         preProcess = c("scale", "center"),
                         trControl = trainControl(method = "repeatedcv", 
                                                  number = 10, 
                                                  repeats = 10, #10次10折交叉验证
                                                  verboseIter = FALSE))
</pre>
<p>在测试集上测试模型。</p>
<pre>
final <- data.frame(actual = test_data$classes, predict(model_rf, newdata = test_data, type = "prob"))
final$predict <- ifelse(final$benign > 0.5, "benign", "malignant")
</pre>
<p>查看测试的混淆矩阵</p>
<pre>
cm_original <- confusionMatrix(final$predict, test_data$classes)
</pre>
<h4 id="b5" name="b5">2)under sampling</h4>
<p>欠采样方法与上述方法的区别在于traincontrol的参数配置。增加sampling=“down”。</p>
<pre>
ctrl <- trainControl(method = "repeatedcv", 
                     number = 10, 
                     repeats = 10, 
                     verboseIter = FALSE,
                     sampling = "down")

set.seed(42)
model_rf_under <- caret::train(classes ~ .,
                         data = train_data,
                         method = "rf",
                         preProcess = c("scale", "center"),
                         trControl = ctrl)
</pre>

<h4 id="b6" name="b6">3)over sampling</h4>
<p>过采样方法与其类似，将traincontrol的参数配置为sampling=“up”。</p>
<pre>
ctrl <- trainControl(method = "repeatedcv", 
                     number = 10, 
                     repeats = 10, 
                     verboseIter = FALSE,
                     sampling = "up")

set.seed(42)
model_rf_over<- caret::train(classes ~ .,
                         data = train_data,
                         method = "rf",
                         preProcess = c("scale", "center"),
                         trControl = ctrl)
</pre>

<h3 id="b3" name="b3">3.other solutions</h3>
<p>ROSE和SMOTE方法将欠采样和数据生成结合起来。</p>
<p>ROSE方法仍类似，将traincontrol的参数配置为sampling=“rose”。</p>
<pre>
ctrl <- trainControl(method = "repeatedcv", 
                     number = 10, 
                     repeats = 10, 
                     verboseIter = FALSE,
                     sampling = "rose")

set.seed(42)
model_rf_rose <- caret::train(classes ~ .,
                         data = train_data,
                         method = "rf",
                         preProcess = c("scale", "center"),
                         trControl = ctrl)
</pre>
<p>SMOTE方法将traincontrol的参数配置为sampling=“smote”。</p>
<pre>
ctrl <- trainControl(method = "repeatedcv", 
                     number = 10, 
                     repeats = 10, 
                     verboseIter = FALSE,
                     sampling = "smote")

set.seed(42)
model_rf_smote <- caret::train(classes ~ .,
                         data = train_data,
                         method = "rf",
                         preProcess = c("scale", "center"),
                         trControl = ctrl)
</pre>
<h3 id="l4" name="l4">4.compare</h3>
<p>比较以上四种平衡方法。</p>
<pre>
models <- list(original = model_rf,
                       under = model_rf_under,
                       over = model_rf_over,
                       smote = model_rf_smote,
                       rose = model_rf_rose)

resampling <- resamples(models)
bwplot(resampling)
</pre>
<p>更强大直观的比较。</p>
<pre>
library(dplyr)
comparison <- data.frame(model = names(models),
                         Sensitivity = rep(NA, length(models)),
                         Specificity = rep(NA, length(models)),
                         Precision = rep(NA, length(models)),
                         Recall = rep(NA, length(models)),
                         F1 = rep(NA, length(models)))

for (name in names(models)) {
  model <- get(paste0("cm_", name))
  
  comparison[comparison$model == name, ] <- filter(comparison, model == name) %>%
    mutate(Sensitivity = model$byClass["Sensitivity"],
           Specificity = model$byClass["Specificity"],
           Precision = model$byClass["Precision"],
           Recall = model$byClass["Recall"],
           F1 = model$byClass["F1"])
}

library(tidyr)
comparison %>%
  gather(x, y, Sensitivity:F1) %>%
  ggplot(aes(x = x, y = y, color = model)) +
    geom_jitter(width = 0.2, alpha = 0.5, size = 3)
</pre>