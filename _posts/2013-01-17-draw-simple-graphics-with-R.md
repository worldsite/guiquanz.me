--- 
layout: post
title: 用R绘制简单实用图
date: 2013-01-17
categories:
  - 技术
tags:
  - R
  - RStudio
  - Knitr
  - Markdown
---

## 引子

![](/img/article/r/2013-01-17-1.png)

本文将主要介绍使用[RStudio](http://www.rstudio.com/ide/docs/authoring/using_markdown) + [Knitr](http://yihui.name/knitr/) + [Markdown](http://daringfireball.net/projects/markdown) 环境，基于“可重复自动化报告”的思想，通过[R](www.r-project.org/)绘制简单实用的图形。主要参考文献，如下：

1.[Producing Simple Graphs with R](http://www.harding.edu/fmccown/r)

2.[自动化报告](https://github.com/yihui/r-ninja/blob/master/11-auto-report.md)

3.[knitr与可重复的统计研究（花絮篇）](http://cos.name/2012/06/reproducible-research-with-knitr)

4.[Knitr云端测试环境](http://public.opencpu.org/apps/knitr/)


## 线状图

1.绘制最简单的点

``` cpp 

# 定义拥有5个数值的向量
cars <- c(1, 3, 6, 4, 9)
# 用默认环境，绘制图
plot(cars)
```

2.增加标题，用线连接点，并进行部分着色

``` cpp 

# 定义拥有5个数值的向量
cars <- c(1, 3, 6, 4, 9)
# 用蓝色的线，串联各个点
plot(cars, type="o", col="blue")
# 增加标题
title(main="Autos", col.main="red", font.main=4)
```

3.增一条红线作为trucks线，并直接指定y轴，使其足够适合trucks数据

``` cpp 

# 定义拥有5个数值的向量
cars <- c(1, 3, 6, 4, 9)
trucks <- c(2, 5, 4, 5, 12)

# 用范围在0到12的y轴，绘制cars
plot(cars, type="o", col="blue", ylim=c(0, 12))
# 用红色线段及四方型的点绘制trucks
lines(trucks, type="o", pch=22, lty=2, col="red")
# 增加标题:红色，黑体（bold/italic）
title(main="Autos", col.main="red", font.main=4)
```

4.修改坐标轴的标签，用max函数计算y轴的值及说明，以自动适应数据的变化

``` cpp 

# 定义拥有5个数值的向量
cars <- c(1, 3, 6, 4, 9)
trucks <- c(2, 5, 4, 5, 12)

# 计算范围至：0到cars与trucks的最大值
g_range <- range(0, cars, trucks)
# 自动求y的范围，并绘制
plot(cars, type="o", col="blue", ylim=g_range, axes=FALSE, ann=FALSE)

# 绘制x轴坐标：用Mon-Fri标签
axis(1, at=1:5, lab=c("Mon", "Tue", "Wed", "Tus", "Fri"))
# 绘制y轴坐标：表示每4标记之间蜱（ticks）的数量
# 4*0:g_range[2]等效于c(0,4,8,12)
axis(2, las=1, at=4*0:g_range[2])
# 增加图例标识框
box()

# 用红色线段及四方型的点绘制trucks
lines(trucks, type="o", pch=22, lty=2, col="red")
# 增加标题:红色，黑体（bold/italic）
title(main="Autos", col.main="red", font.main=4)

# 设置x和y轴标签
title(xlab="Days", col.lab=rgb(0, 0.5, 0))
title(ylab="Total", col.lab=rgb(0, 0.5, 0))

# 创建说明
legend(1, g_range[2], c("cars", "trucks"), cex=0.8, col=c("blue", "red"), pch=21:22, lty=1:2)
```


5.从文件读取数据，生成png文件
其中，要读取的表格数据文件为auto.dat(suv数据)，内容如下：
<pre>
cars  trucks	suvs
1     2       4
3     5       4
6     4       6
4     5       6
9     12     16
</pre>


``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")
# 计算最大的y值
max_y <- max(autos_data)
#定义颜色
plot_colors <- c("blue", "red", "forestgreen")
# 定义拥有5个数值的向量

# 设置输出图的设备
png(filename="autos.png", height=200, width=210, bg="white")

# 自动求y的范围，并绘制
plot(autos_data$cars, type="o", col=plot_colors[1], ylim=c(0, max_y), axes=FALSE, ann=FALSE)

# 绘制x轴坐标：用Mon-Fri标签
axis(1, at=1:5, lab=c("Mon", "Tue", "Wed", "Tus", "Fri"))
# 绘制y轴坐标：表示每4标记之间蜱（ticks）的数量
# 4*0:max_y
axis(2, las=1, at=4*0:max_y)
# 增加图例标识框
box()

# 用红色线段及四方型的点绘制trucks
lines(autos_data$trucks, type="o", pch=22, lty=2, col=plot_colors[2])
# 用绿色点段及钻石点绘制suvs
lines(autos_data$suvs, type="o", pch=23, lty=3, col=plot_colors[3])
# 增加标题:红色，黑体（bold/italic）
title(main="Autos", col.main="red", font.main=4)

# 设置x和y轴标签
title(xlab="Days", col.lab=rgb(0, 0.5, 0))
title(ylab="Total", col.lab=rgb(0, 0.5, 0))

# 创建说明
legend(1, max_y, names(autos_data), cex=0.8, col=plot_colors, pch=21:23, lty=1:3)

# 关闭设备，打印图
dev.off()
```

6.从文件读取数据（同上），生成PDF文件,同时调整标签45度角，去除图形边界的空白


``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")
# 计算最大的y值
max_y <- max(autos_data)
#定义颜色
plot_colors <- c(rgb(r=0.0, g=0.0, b=0.9), "red", "forestgreen")
# 定义拥有5个数值的向量

# 设置输出图的设备
#pdf(filename="autos.pdf", height=3.5, width=5)

# 截掉边界空白（下，左，上，右）
par(mar=c(4.2, 3.8, 0.2, 0.2))

# 自动求y的范围，并绘制
plot(autos_data$cars, type="l", col=plot_colors[1], 
     ylim=range(autos_data), axes=F, ann=T, 
     xlab="Days", ylab="Total", cex.lab=0.8, lwd=2)

# 绘制x轴坐标：不带标签
axis(1, lab=F)
# 绘制x标签，并设置45度角
text(axTicks(1), par("usr")[3] - 2, srt=45, adj=1,
     labels=c("Mon", "Tue", "Wed", "Thu", "Fri"),
     xpd=T, cex=0.8)
# 绘制y轴坐标：表示每4标记之间蜱（ticks）的数量
# 4*0:g_range[2]等效于c(0,4,8,12)
axis(2, las=1, cex.axis=0.8)
# 增加图例标识框
box()

# 用红色线段及四方型的点绘制trucks
lines(autos_data$trucks, type="l", pch=22, lty=2, lwd=2,
      col=plot_colors[2])
# 用绿色点段及钻石点绘制suvs
lines(autos_data$suvs, type="l", pch=23, lty=3, lwd=2,
      col=plot_colors[3])

# 创建说明
legend("topleft", names(autos_data), cex=0.8, col=plot_colors, 
       lty=1:3, lwd=2, bty="n")

# 关闭设备，打印图
#dev.off()

# 恢复设置
par(mar=c(5,4,4,2)+0.1)
```


## 柱状图

1.绘制最简单的柱状图

``` cpp 

# 定义拥有5个数值的向量
cars <- c(1, 3, 6, 4, 9)
# 用默认环境，绘制图
barplot(cars)
```

2.从文件读数据，增加标签和蓝色边框及密度斜线

``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")
# 用默认环境，绘制图
barplot(autos_data$cars, main="Cars", xlab="Days", ylab="Total",
        names.arg=c("Mon", "Tue", "Wed", "Thu", "Fri"), 
        border="blue", density=c(10,20,30,40,50))
```

3.从文件读数据，着色（彩虹色），增加说明

``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")
# 用默认环境，绘制图
barplot(as.matrix(autos_data), main="Cars", 
        xlab="Days", ylab="Total",
        beside=TRUE, col=rainbow(5)) 
legend("topleft", c("Mon", "Tue", "Wed", "Thu", "Fri"),
       cex=0.9, bty="n", fill=rainbow(5))
```

4.从文件读数据，着色（彩虹色），将说明放在图外面

``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")

# 开辟说明位置区:放在图区右边
par(xpd=T, mar=par()$mar+c(0, 0, 0, 4))

# 用默认环境，绘制图
barplot(t(autos_data), main="Autos", col=heat.colors(3), 
        space=0.1, cex.axis=0.8, las=1,
        names.arg=c("Mon", "Tue", "Wed", "Thu", "Fri"),
        cex=0.8) 
legend(6, 30, names(autos_data),cex=0.9, fill=heat.colors(3))
# 恢复设置
par(mar=c(5,4,4,2)+0.1)
```


## 直方图

1.绘制最简的直方图

``` cpp 

# 定义拥有5个数值的向量
suvs <- c(4, 4, 6, 6, 16)
# 用默认环境，绘制图
hist(suvs)
```

2.从文件读取数据，绘制组合了cars、trucks和suvs的直方图

``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")

autos <- c(autos_data$cars,
           autos_data$trucks,
           autos_data$suvs)
# 绘制图
hist(autos, col="lightblue", ylim=c(0, 10))
```

3.从文件读取数据，不再分组数据，同时增加水平y轴

``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")

autos <- c(autos_data$cars,
           autos_data$trucks,
           autos_data$suvs)

max_num <- max(autos)

# 绘制图
hist(autos, col=heat.colors(max_num), breaks=max_num,
     xlim=c(0, max_num), right=F, main="Autos Histogram",
     las = 1)
```


4.从文件读取数据，不再分组数据，同时增加密度的非分割的图

``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")

autos <- c(autos_data$cars,
           autos_data$trucks,
           autos_data$suvs)

max_num <- max(autos)

brk <- c(0, 3, 4, 5, 6, 10, 16)
# 绘制图
hist(autos, col=heat.colors(length(brk)), breaks=brk,
     xlim=c(0, max_num), right=F, main="Probability Density",
     las = 1, cex.axis=0.8, freq=F)
```


5.通过1000个随机值，绘制对数正太分布图（log-normal distribution）

``` cpp 

r <- rlnorm(1000)
hist(r)
```

6.用plot函数显示对数正太分布图

``` cpp 

r <- rlnorm(1000)
h <- hist(r, plot=F, breaks=c(seq(0, max(r)+1, .1)))
plot(h$counts, log="xy", pch=20, col="blue",
     main="Log-normal distribution",
     xlab="Value", ylab="Frequency")
```


## 饼图

1.绘制最简的饼图

``` cpp 

# 定义拥有5个数值的向量
cars <- c(1, 3, 6, 4, 9)
# 用默认环境，绘制图
pie(cars)
```

2.增加头信息，修改颜色，定义标签

``` cpp 

# 定义拥有5个数值的向量
cars <- c(1, 3, 6, 4, 9)
# 绘制图
pie(cars, main="Cars", col=rainbow(length(cars)),
    labels=c("Mon", "Tue", "Wed", "Thu", "Fri"))
```

3.修改颜色，定义标签,增加说明

``` cpp 

# 定义拥有5个数值的向量
cars <- c(1, 3, 6, 4, 9)

colors <- c("white", "grey70", "grey80", "grey40", "black")
car_labels <- round(cars/sum(cars) * 100, 1)
car_labels <- paste(car_labels, "%", sep="")

# 绘制图
pie(cars, main="Cars", col=colors, labels=car_labels, cex=0.9)
legend(1.5, 0.5, c("Mon", "Tue", "Wed", "Thu", "Fri"), cex=0.9, 
       fill=colors)

```


## 点图

1.绘制最简的点图

``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")

dotchart(t(autos_data))
```

2.绘制附加着色的点图

``` cpp 

# 设置工作目录
setwd("~/R-projects")
# 读取数据
autos_data <- read.table("autos.dat", header=T, sep="\t")

dotchart(t(autos_data), color=c("red", "blue", "darkgreen"),
         main="Dochart for Autos", cex=0.9)
```


## 杂项

``` cpp 

plot(1, 1, xlim=c(1,5.5), ylim=c(0,7), type="n", ann=FALSE)

# Plot digits 0-4 with increasing size and color
text(1:5, rep(6,5), labels=c(0:4), cex=1:5, col=1:5)

# Plot symbols 0-4 with increasing size and color
points(1:5, rep(5,5), cex=1:5, col=1:5, pch=0:4)
text((1:5)+0.4, rep(5,5), cex=0.6, (0:4))

# Plot symbols 5-9 with labels
points(1:5, rep(4,5), cex=2, pch=(5:9))
text((1:5)+0.4, rep(4,5), cex=0.6, (5:9))

# Plot symbols 10-14 with labels
points(1:5, rep(3,5), cex=2, pch=(10:14))
text((1:5)+0.4, rep(3,5), cex=0.6, (10:14))

# Plot symbols 15-19 with labels
points(1:5, rep(2,5), cex=2, pch=(15:19))
text((1:5)+0.4, rep(2,5), cex=0.6, (15:19))

# Plot symbols 20-25 with labels
points((1:6)*0.8+0.2, rep(1,6), cex=2, pch=(20:25))
text((1:6)*0.8+0.5, rep(1,6), cex=0.6, (20:25))
```


