# 이원분류 분산분석(Two way ANOVA)

## 데이터 불러오기


```r
data(ToothGrowth)
str(ToothGrowth)
```

```
## 'data.frame':	60 obs. of  3 variables:
##  $ len : num  4.2 11.5 7.3 5.8 6.4 10 11.2 11.2 5.2 7 ...
##  $ supp: Factor w/ 2 levels "OJ","VC": 2 2 2 2 2 2 2 2 2 2 ...
##  $ dose: num  0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 ...
```

```r
levels(ToothGrowth$supp)
```

```
## [1] "OJ" "VC"
```
R에 있는 기본 데이터셋인 ToothGrowth를 불러온다.

3개의 변수와 60개의 관측치가 있다.

supp(보충제 타입)에는 2가지 요인(OJ, VC)이 있다.


```r
ToothGrowth$dose <- factor(ToothGrowth$dose,    levels = c(0.5, 1, 2), 
   labels = c("D0.5", "D1", "D2"))
str(ToothGrowth)
```

```
## 'data.frame':	60 obs. of  3 variables:
##  $ len : num  4.2 11.5 7.3 5.8 6.4 10 11.2 11.2 5.2 7 ...
##  $ supp: Factor w/ 2 levels "OJ","VC": 2 2 2 2 2 2 2 2 2 2 ...
##  $ dose: Factor w/ 3 levels "D0.5","D1","D2": 1 1 1 1 1 1 1 1 1 1 ...
```

dose 변수(보충제 투어량)가 3가지 값으로 구성되어 있기 때문에 factor로 변경한다.


## 이원분류 분산분석이란
###분산분석
두 개의 집단의 평균의 비교할때, T-test를 사용했다.

분산분석은 3개 이상의 집단의 평균을 비교할때 사용한다.

여기서 말하는 집단은 독립변수의 요인 개수이다.

그리고 종속변수의 집단에 따라 분산분석이 나뉘어 진다.


### 이원분류 분산분석이란,

종속변수(두 개 집단) ~ 독립변수(3개 이상 집단) 일때, 평균을 비교하는 기법이다.



```r
boxplot(len ~ supp * dose, 
        data=ToothGrowth,
        frame = FALSE, 
        col = c("#00AFBB", "#E7B800"),
        ylab="Tooth Length")
```

![](ANOVA-TwoWay_files/figure-epub3/unnamed-chunk-3-1.png)<!-- -->

boxplot으로 나타내면 위와 같이 구성되어 있다.



```r
interaction.plot
```

```
## function (x.factor, trace.factor, response, fun = mean, type = c("l", 
##     "p", "b", "o", "c"), legend = TRUE, trace.label = deparse1(substitute(trace.factor)), 
##     fixed = FALSE, xlab = deparse1(substitute(x.factor)), ylab = ylabel, 
##     ylim = range(cells, na.rm = TRUE), lty = nc:1, col = 1, pch = c(1L:9, 
##         0, letters), xpd = NULL, leg.bg = par("bg"), leg.bty = "n", 
##     xtick = FALSE, xaxt = par("xaxt"), axes = TRUE, ...) 
## {
##     ylabel <- paste(deparse1(substitute(fun)), "of ", deparse1(substitute(response)))
##     type <- match.arg(type)
##     cells <- tapply(response, list(x.factor, trace.factor), fun)
##     nr <- nrow(cells)
##     nc <- ncol(cells)
##     xvals <- 1L:nr
##     if (is.ordered(x.factor)) {
##         wn <- getOption("warn")
##         options(warn = -1)
##         xnm <- as.numeric(levels(x.factor))
##         options(warn = wn)
##         if (!anyNA(xnm)) 
##             xvals <- xnm
##     }
##     xlabs <- rownames(cells)
##     ylabs <- colnames(cells)
##     nch <- max(sapply(ylabs, nchar, type = "width"))
##     if (is.null(xlabs)) 
##         xlabs <- as.character(xvals)
##     if (is.null(ylabs)) 
##         ylabs <- as.character(1L:nc)
##     xlim <- range(xvals)
##     xleg <- xlim[2L] + 0.05 * diff(xlim)
##     xlim <- xlim + c(-0.2/nr, if (legend) 0.2 + 0.02 * nch else 0.2/nr) * 
##         diff(xlim)
##     dev.hold()
##     on.exit(dev.flush())
##     matplot(xvals, cells, ..., type = type, xlim = xlim, ylim = ylim, 
##         xlab = xlab, ylab = ylab, axes = axes, xaxt = "n", col = col, 
##         lty = lty, pch = pch)
##     if (axes && xaxt != "n") {
##         axisInt <- function(x, main, sub, lwd, bg, log, asp, 
##             ...) axis(1, x, ...)
##         mgp. <- par("mgp")
##         if (!xtick) 
##             mgp.[2L] <- 0
##         axisInt(1, at = xvals, labels = xlabs, tick = xtick, 
##             mgp = mgp., xaxt = xaxt, ...)
##     }
##     if (legend) {
##         yrng <- diff(ylim)
##         yleg <- ylim[2L] - 0.1 * yrng
##         if (!is.null(xpd) || {
##             xpd. <- par("xpd")
##             !is.na(xpd.) && !xpd. && (xpd <- TRUE)
##         }) {
##             op <- par(xpd = xpd)
##             on.exit(par(op), add = TRUE)
##         }
##         text(xleg, ylim[2L] - 0.05 * yrng, paste("  ", trace.label), 
##             adj = 0)
##         if (!fixed) {
##             ord <- sort.list(cells[nr, ], decreasing = TRUE)
##             ylabs <- ylabs[ord]
##             lty <- lty[1 + (ord - 1)%%length(lty)]
##             col <- col[1 + (ord - 1)%%length(col)]
##             pch <- pch[ord]
##         }
##         legend(xleg, yleg, legend = ylabs, col = col, pch = if (type %in% 
##             c("p", "b")) 
##             pch, lty = if (type %in% c("l", "b")) 
##             lty, bty = leg.bty, bg = leg.bg)
##     }
##     invisible()
## }
## <bytecode: 0x0000000015b71be0>
## <environment: namespace:stats>
```
각 변수의 변화를 추적하는 함수이다.

따라서 변수의 추세를 볼 수 있다.

분산분석에서 중요한 함수이다.



```r
interaction.plot(x.factor = ToothGrowth$dose,
trace.factor = ToothGrowth$supp,
response = ToothGrowth$len,
fun = mean,
type = "b",
legend = TRUE,
xlab = "Dose",
ylab="Tooth Length",
pch=c(1,19),
col = c("#00AFBB", "#E7B800"))
```

![](ANOVA-TwoWay_files/figure-epub3/unnamed-chunk-5-1.png)<!-- -->

+ x.factor : 기준이 되는 factor
+ trace.factor: 추가 펙터
+ -response : 분석 변수

## 전제조건
분산분석을 사용하기 위해서는 독립성, 등분산성을 만족해야 한다.

### 정규성

표본이 충분하기 때문에(30개 이상) clt와 대수의 법칙에 따라 정규성이 있다.


### 등분산성


```r
library(car)
```

```
## Loading required package: carData
```

```r
leveneTest(len ~ supp*dose, data = ToothGrowth)
```

```
## Levene's Test for Homogeneity of Variance (center = median)
##       Df F value Pr(>F)
## group  5  1.7086 0.1484
##       54
```

모수적 방법을 이용한다. p값이 유의하지 않기 때문에 등분산성이 있다.

## Two-way ANOVA test
귀무가설 : 모든 평균들은 다 같다.

대체가설 : 평균들이 모두 같지는 않다. (’평균들이 모두 다르다’가 아니다.)


```r
tooth.aov <- aov(len ~ supp + dose, data = ToothGrowth)

summary(tooth.aov)
```

```
##             Df Sum Sq Mean Sq F value   Pr(>F)    
## supp         1  205.4   205.4   14.02 0.000429 ***
## dose         2 2426.4  1213.2   82.81  < 2e-16 ***
## Residuals   56  820.4    14.7                     
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

supp(보충제 타입)과 dose(보충제 투어량)의 p값이 유의수준보다 작기 때문에 통계적으로 유의하다.

즉, 보충제 타입에 따라 치아의 길이가 차이가 있고, 또한 투어량에 따라 치아의 길이 차가 있다.

만약, 두 변수(supp, dose)가 독립적이지 않다고 생각하면

더하기를 곱하기로 바꾸어 상호작용한다는 가정을 두어야 한다.


```r
tooth.aov2 <- aov(len ~ supp*dose, data = ToothGrowth)

summary(tooth.aov2)
```

```
##             Df Sum Sq Mean Sq F value   Pr(>F)    
## supp         1  205.4   205.4  15.572 0.000231 ***
## dose         2 2426.4  1213.2  92.000  < 2e-16 ***
## supp:dose    2  108.3    54.2   4.107 0.021860 *  
## Residuals   54  712.1    13.2                     
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

분산분석 결과 supp:dose(상호작용)는 유의수준이 0.05일때 통계적으로 유의하다.

어떤 모델을 사용할지는 분석가의 판단에 맞긴다.



## 다중비교
분산분석에서는 어떤 집단이 다르며, 어떠한 차이가 있는지 알 수 없었다.

그 내용을 다중비교를 통해 알 수 있다.(3가지 방법)

### TukeyHSD


```r
TukeyHSD(tooth.aov2, which="dose")
```

```
##   Tukey multiple comparisons of means
##     95% family-wise confidence level
## 
## Fit: aov(formula = len ~ supp * dose, data = ToothGrowth)
## 
## $dose
##           diff       lwr       upr   p adj
## D1-D0.5  9.130  6.362488 11.897512 0.0e+00
## D2-D0.5 15.495 12.727488 18.262512 0.0e+00
## D2-D1    6.365  3.597488  9.132512 2.7e-06
```

TukeyHSD 함수는 두 개씩 짝을 지어 비교를 해준다.

투어량에 따른 차이를 볼때, which로 설정해준다.(충전제로 하려면 supp로 바꾼다.)

p adj를 보면 모든 쌍에서 유의수준보다 작아 통계적으로 유의하다.

따라서, 투어량을 바꿀때마다 치아의 길이 차이가 있다고 해석한다.



-diff: 두 집단의 평균 차이
-lwr, upr: 95% 신뢰구간에서 하한값과 상한값
-p adj: 조정된 p값


### glht

glht() : 일반화된 선형 가설 검정에 쓰임

glht(model, lincft)
linfct : linear, 함수가 지정되어야 함


```r
library(multcomp)
```

```
## Loading required package: mvtnorm
```

```
## Loading required package: survival
```

```
## Loading required package: TH.data
```

```
## Loading required package: MASS
```

```
## 
## Attaching package: 'TH.data'
```

```
## The following object is masked from 'package:MASS':
## 
##     geyser
```

```r
summary(glht(tooth.aov, linfct = mcp(dose="Tukey")))
```

```
## 
## 	 Simultaneous Tests for General Linear Hypotheses
## 
## Multiple Comparisons of Means: Tukey Contrasts
## 
## 
## Fit: aov(formula = len ~ supp + dose, data = ToothGrowth)
## 
## Linear Hypotheses:
##                Estimate Std. Error t value Pr(>|t|)    
## D1 - D0.5 == 0    9.130      1.210   7.543  < 1e-06 ***
## D2 - D0.5 == 0   15.495      1.210  12.802  < 1e-06 ***
## D2 - D1 == 0      6.365      1.210   5.259 5.76e-06 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## (Adjusted p values reported -- single-step method)
```

TukeyHSD와 같은 결과이다.


### pairwise.t.test


```r
pairwise.t.test(ToothGrowth$len, ToothGrowth$dose, p.adjust.method = "BH")
```

```
## 
## 	Pairwise comparisons using t tests with pooled SD 
## 
## data:  ToothGrowth$len and ToothGrowth$dose 
## 
##    D0.5    D1     
## D1 1.0e-08 -      
## D2 4.4e-16 1.4e-05
## 
## P value adjustment method: BH
```

결과를 교차표로 만들어 준다.

