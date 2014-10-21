---
title: R语言学习记录-基本篇
layout: post
categories:
  - R
  - letter
tags:
  - R
---

1. prop.table(x, margin=NULL)

计算表格中各个数值在给定方向占得比例。1表示按行计算，2表示按列计算，默认按整个表格计算。

{% highlight R %}

> m <- matrix(1:4,2)
> m
[,1] [,2]
[1,] 1 3
[2,] 2 4
> prop.table(m)
[,1] [,2]
[1,] 0.1 0.3
[2,] 0.2 0.4
> prop.table(m,1)
[,1] [,2]
[1,] 0.2500000 0.7500000
[2,] 0.3333333 0.6666667
> prop.table(m,2)
[,1] [,2]
[1,] 0.3333333 0.4285714
[2,] 0.6666667 0.5714286
{% endhighlight %}

2.去除矩阵中包含特定元素的行【https://stat.ethz.ch/pipermail/r-help/2008-November/179333.html】

{% highlight R %}
d <- rbind(c(1,    0,    6,    4),
c(2,    5,   7,    5),
c(3,    6,    8,    6),
c(4,    0,    0,    0))
f <- as.matrix(d)
f[-which(rowSums(f==0)>0),]
rowSums(f==0):计算值为0的列数
which去除符合条件的值的坐标，-取反

data[rowSums(is.na(data))<ncol(data),]

去除和为0的行

data <- data[rowSums(data)!=0,]


{% endhighlight %}

3.矩阵转置

{% highlight R %}
t(x) #转置后输出时，row.names 和col.names也会变化

aggregate(x, by=list(1,1,2,2), FUN=mean)
{% endhighlight %}

4.aggregate

Splits the data into subsets, computes summary statistics for each, and returns the result in a convenient form.

5.修改行或列的名字，rownames, colnames, paste

{% highlight R %}
cluster.mean.colnames <- colnames(data2.cluster.mean)
cluster.mean.colnames[1] = paste('#',cluster.mean.colnames[1], sep='')
colnames(data2.cluster.mean) <- cluster.mean.colnames
{% endhighlight %}

6.R中字符串连接用paste

{% highlight R %}
paste('1', '2', sep='\t')
{% endhighlight %}

7.在R交互式环境中执行写好的脚本

{% highlight R %}
source('script.r')
{% endhighlight %}

8.read.table

{% highlight R %}
data <- read.table(file='file', sep='\t', header=T, row.names=1)

row.names = 1 #not true but first column

If the first line have one item less than the total number of columns, header=TRUE autonomously.

If header=TRUE, col.names is the values in that line, no need to specify again.

If row.name and col.name starts with a number and check.names=TRUE(default), an X will be added before each name. One can turn it off to avoid adding characters.
{% endhighlight %}

9.R中if-else的使用，else需与if的右括号位于同一行。

{% highlight R %}
if(is.na(result)[1]) {     print("NA") } else {     coef(result)[[1]] }

{% endhighlight %}

10.attach, 把数据表或列表的一列放入R的搜索空间，使其可以直接调用

{% highlight R %}
>>> data[2:5,]

Gene hmc expression
2 NM_001001144 0.1999845 0.768915
3 NM_001001152 0.0000000 -0.663424
4 NM_001001160 0.1203579 -0.636796
5 NM_001001176 0.0000000 0.249296

>>>attach(data)

>>>hmc[2:5]

[1] 0.1999846 0.0000000 0.1203579 0.0000000

>>>expression[2:5]

[1] 0.768915 -0.663424 -0.636796 0.249296
{% endhighlight %}

11.lowess, 局部曲线拟合, This function performs the computations for the \_LOWESS\_ smoother which uses locally-weighted polynomial regression.[ http://210.75.224.29/wordpress/wp-content/gallery/for\_article/scatter\_plot_lowess.png][1]

<a href="http://210.75.224.29/wordpress/wp-content/gallery/for_article/scatter_plot_lowess.png" title="" rel="lightbox[singlepic37]" > <img class="ngg-singlepic" src="http://210.75.224.29/wordpress/wp-content/gallery/cache/37__100x75_scatter_plot_lowess.png" alt="scatter_plot_lowess" title="scatter_plot_lowess" /> </a> {% highlight R %}
>attach(mtcars)

>plot(wt, mpg, main="Scatter Example", xlab="Car Weight", ylab="Miles Per Gallon", pch=19)

> abline(lm(mpg~wt), col="red")
> lines(lowess(wt,mpg),col="blue")
{% endhighlight %}

12.R中得到list的多列

{% highlight R %}
> mtcars
mpg cyl disp hp drat wt qsec vs am gear carb
Mazda RX4 21.0 6 160.0 110 3.90 2.620 16.46 0 1 4 4
Mazda RX4 Wag 21.0 6 160.0 110 3.90 2.875 17.02 0 1 4 4
Datsun 710 22.8 4 108.0 93 3.85 2.320 18.61 1 1 4 1
Hornet 4 Drive 21.4 6 258.0 110 3.08 3.215 19.44 1 0 3 1
Hornet Sportabout 18.7 8 360.0 175 3.15 3.440 17.02 0 0 3 2

> data <- mtcars(c(-2,-4,-7,-8))
> data <- mtcars(c(1,3,5,6))

> data
mpg disp drat wt
Mazda RX4 21.0 160.0 3.90 2.620
Mazda RX4 Wag 21.0 160.0 3.90 2.875
Datsun 710 22.8 108.0 3.85 2.320
Hornet 4 Drive 21.4 258.0 3.08 3.215
Hornet Sportabout 18.7 360.0 3.15 3.440
{% endhighlight %}

13.读取只含有一列的文件并把其转为向量

{% highlight R %}
vector <- as.vector(read.table(file="file",sep="\t", header=F)$V1 )

{% endhighlight %}

14.重复向量中每个元素

{% highlight R %}
rep(c(1,2,3), each=3)

rep(data$firstcolum, data$secondcolumn)
{% endhighlight %}

15. dist and cor

dist:  
This function computes and returns the distance matrix computed by using the specified distance measure to compute the distances between the rows of a data matrix.  
cor:  
If x and y are matrices then the covariances (or correlations) between the columns of x and the columns of y are computed.  
**dist** (and dist objects, which is what heatmap.2 is assuming it&#8217;s getting) assume that you&#8217;ve calculated the distance between **rows**, while using **cor** you are essentially calculating the distance between **columns**

16.grepl，类似于R的grep

{% highlight R %}
grepl(pattern, x, ignore.case = FALSE, perl = FALSE, fixed = FALSE, useBytes = FALSE)

matirx[, grepl("Treat", colnames(matrix)) | grepl("Control", colnames(matrix))]
{% endhighlight %}

17.substr, 去除矩阵的一部分

{% highlight R %}
substr(x, start, stop)
substring(text, first, last = 1000000L)
substr(x, start, stop) <- value
substring(text, first, last = 1000000L) <- value
{% endhighlight %}

18.优先级问题

{% highlight R %}
":"的优先级高于四则运算，所以 1:3+1的结果是2，3，4，而不是1，2，3，4
{% endhighlight %}

19.退出R

{% highlight R %}
quit()
{% endhighlight %}

20.write.table输出时列名字一行通常会少一列，解决办法

{% highlight R %}
write.table("filename.xls", sep="\t", col.names = NA, row.names = TRUE)
{% endhighlight %}

21.R中and操作使用&，而不是&&, or操作使用|而不是||。
{% highlight R %}
data[data$ok<3] | data$nok == ‘'yes',]
{% endhighlight %}

22.修改矩阵或数据框中某一列符合条件的值

{% highlight R %}
data$ok[data$ok<3] <- 3
{% endhighlight %}

23.取出满足某一列符合条件的行（注意<span style="color: #ff0000;"> <strong>，</strong></span>的使用）

{% highlight R %}
data_fdr <- data[data[,2]>=2,]
{% endhighlight %}

24.排序矩阵

{% highlight R %}
data <- data[order(data$col),]
T2 <- T2[order(T2$gene.index),]

{% endhighlight %}

25.去除重复

{% highlight R %}
data.frame <- data.frame[!duplicate(data.frame),]
data.frame <- unique(data.frame)

{% endhighlight %}

26.字符串分割和提取

{% highlight R %}
> countfiles
[1] "./treated1en.counts"   "./treated2en.counts"   "./treated3en.counts"
[4] "./untreated1en.counts" "./untreated2en.counts" "./untreated3en.counts"
[7] "./untreated4en.counts"

>strsplit(countfiles,'en')[[1]]
[1] "./treated1" ".counts"

[[2]]
[1] "./treated2" ".counts"

[[3]]
[1] "./treated3" ".counts"

[[4]]
[1] "./untreated1" ".counts"

[[5]]
[1] "./untreated2" ".counts"

[[6]]
[1] "./untreated3" ".counts"

[[7]]
[1] "./untreated4" ".counts"

> sapply(strsplit(countfiles,'en'),"[[",1)
[1] "./treated1"   "./treated2"   "./treated3"   "./untreated1" "./untreated2"
[6] "./untreated3" "./untreated4"
> strsplit(sapply(strsplit(countfiles,'en'),"[[",1),"\\/")
[[1]]
[1] "."        "treated1"

[[2]]
[1] "."        "treated2"

[[3]]
[1] "."        "treated3"

[[4]]
[1] "."          "untreated1"

[[5]]
[1] "."          "untreated2"

[[6]]
[1] "."          "untreated3"

[[7]]
[1] "."          "untreated4"

> sapply(strsplit(sapply(strsplit(countfiles,'en'),"[[",1),"\\/"),"[[",2)
[1] "treated1"   "treated2"   "treated3"   "untreated1" "untreated2"
[6] "untreated3" "untreated4"


{% endhighlight %}

27.翻转矩阵或数据框(turn matrix or data.frame upsiede-down)

{% highlight R %}
> a
   X1 X2 X3
ac  1  4  7
cb  2  5  8
bc  3  6  9
> a[rev(rownames(a)),]
   X1 X2 X3
bc  3  6  9
cb  2  5  8
ac  1  4  7

{% endhighlight %}

28.替换矩阵中符合特定要求的值

{% highlight R %}
> data <- matrix(c(1,2,3,4,0,1,2,3,4,5,6,0,0,2,0),nrow=3)
> data
     [,1] [,2] [,3] [,4] [,5]
[1,]    1    4    2    5    0
[2,]    2    0    3    6    2
[3,]    3    1    4    0    0
> log2(data)
         [,1] [,2]     [,3]     [,4] [,5]
[1,] 0.000000    2 1.000000 2.321928 -Inf
[2,] 1.000000 -Inf 1.584963 2.584963    1
[3,] 1.584963    0 2.000000     -Inf -Inf
> data_log <- log2(data)
> data_log[data_log==-Inf] = 0
> data_log
         [,1] [,2]     [,3]     [,4] [,5]
[1,] 0.000000    2 1.000000 2.321928    0
[2,] 1.000000    0 1.584963 2.584963    1
[3,] 1.584963    0 2.000000 0.000000    0

> data.m$value[data.m$value < $small] <- 0


{% endhighlight %}

29.矩阵分割和选取

{% highlight R %}
x[n,] # 取出第n行

x[,n] # 取出第n列

x[,c(1,3)] #取出第1和3列

x[-1,] #去除第一行

x['pattern'] #取出名为pattern的行


{% endhighlight %}

30.

100.

 [1]: http://210.75.224.29/wordpress/wp-content/gallery/for_article/scatter_plot_lowess.png
