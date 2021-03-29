# .One Sample T-test
모집단에 대한 평균이 참인지를 알아보고 신뢰구간도 구할 수 있다.


## 데이터 만들기

```r
set.seed(1234)
my.data <- data.frame( name = paste0( rep("M_", 10), 1:10 ), weight = round( rnorm(40, 90, 2), 1 ) )

str(my.data)
```

```
## 'data.frame':	40 obs. of  2 variables:
##  $ name  : chr  "M_1" "M_2" "M_3" "M_4" ...
##  $ weight: num  87.6 90.6 92.2 85.3 90.9 91 88.9 88.9 88.9 88.2 ...
```

남자 10명에 대한 이름과 몸무게를 담은 데이터셋을 만든다.

## One sample T-test란
모집단에 대한 평균이 참인지를 알아보고 신뢰구간도 구할 수 있다.

예시) 

귀무가설: 강남구에 사는 사람들의 평균 소득이 500만원이다.

대립가설: 강남구에 사는 사람들의 평균 소득이 500만원이 아니다.


## 모수적 방법
모수적인 방법을 사용하기 위해서는 모집단이 정규성을 따르는지 확인해야 한다.

앞에서 만든 데이터(my.data)가 소표본이기 때문이다.


## 정규성 검정


```r
shapiro.test(my.data$weight)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  my.data$weight
## W = 0.96003, p-value = 0.1679
```

p-value값이 유의수준(0.05)보다 크기 때문에 귀무가설 채택.

따라서 정규성을 따른다.


## t.test


```r
summary(my.data$weight)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   85.30   88.17   89.00   89.17   90.15   94.80
```

summary함수를 이용하면 남자 10명의 평균 몸무게가 89.17kg라는 것을 알 수 있다.

만약 평균 몸무게를 모른다고 가정하고 가설을 잡는다.

남자 평균 몸무게가 95kg이다.(귀무가설)


```r
(res<-t.test(my.data$weight,mu = 95))
```

```
## 
## 	One Sample t-test
## 
## data:  my.data$weight
## t = -20.175, df = 39, p-value < 2.2e-16
## alternative hypothesis: true mean is not equal to 95
## 95 percent confidence interval:
##  88.58551 89.75449
## sample estimates:
## mean of x 
##     89.17
```

p-value값이 유의수준(0.05)보다 아주 작기 때문에 귀무가설 기각. 대립가설 채택.

따라서, 남자 평균 몸무게가 95kg이 아니다.


alternative를 사용하여 대립가설을 조정할 수 있다.


```r
t.test(my.data$weight, mu = 95, alternative = "less")
```

```
## 
## 	One Sample t-test
## 
## data:  my.data$weight
## t = -20.175, df = 39, p-value < 2.2e-16
## alternative hypothesis: true mean is less than 95
## 95 percent confidence interval:
##      -Inf 89.65688
## sample estimates:
## mean of x 
##     89.17
```

less인 경우, 남자 평균 몸무게가 95kg보다 작다.(대립가설) 대립가설 채택이기 때문에 대립가설이 참이다.


```r
t.test(my.data$weight, mu = 95, alternative = "greater")
```

```
## 
## 	One Sample t-test
## 
## data:  my.data$weight
## t = -20.175, df = 39, p-value = 1
## alternative hypothesis: true mean is greater than 95
## 95 percent confidence interval:
##  88.68312      Inf
## sample estimates:
## mean of x 
##     89.17
```

greater인 경우, 남자 평균 몸무게가 95kg보다 크다.(대립가설) 귀무가설 채택이기 때문에 대립가설이 거짓이다.


신뢰구간


```r
res$conf.int
```

```
## [1] 88.58551 89.75449
## attr(,"conf.level")
## [1] 0.95
```

값이 88.58551~89.75449 사이가 신뢰구간 0.95이내 이다.



## 비모수적 방법
소표본이고 정규성을 따르지 않을 때 사용하는 방법이다.


```r
set.seed(1234)
my.data2 <- data.frame( name = paste0( rep("M_", 10), 1:10 ), weight = round( rnorm(70, 90, 2), 1 ) )

str(my.data2)
```

```
## 'data.frame':	70 obs. of  2 variables:
##  $ name  : chr  "M_1" "M_2" "M_3" "M_4" ...
##  $ weight: num  87.6 90.6 92.2 85.3 90.9 91 88.9 88.9 88.9 88.2 ...
```

```r
shapiro.test(my.data2$weight)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  my.data2$weight
## W = 0.94492, p-value = 0.003967
```

my.data는 정규성을 따르기 때문에 살짝 조정해 준다.


### wilcox.test(윌콕슨 검정)


```r
wilcox.test(my.data2$weight,mu=95)
```

```
## 
## 	Wilcoxon signed rank test with continuity correction
## 
## data:  my.data2$weight
## V = 1, p-value = 3.761e-13
## alternative hypothesis: true location is not equal to 95
```

p-value값이 유의수준(0.05)보다 아주 작기 때문에 귀무가설 기각. 대립가설 채택.

따라서, 남자 평균 몸무게가 95kg이 아니다.


추가 옵션


```r
t.test(my.data2$weight, mu = 95, alternative = "less")
```

```
## 
## 	One Sample t-test
## 
## data:  my.data2$weight
## t = -22.665, df = 69, p-value < 2.2e-16
## alternative hypothesis: true mean is less than 95
## 95 percent confidence interval:
##      -Inf 89.85957
## sample estimates:
## mean of x 
##  89.45143
```


```r
t.test(my.data2$weight, mu = 95, alternative = "greater")
```

```
## 
## 	One Sample t-test
## 
## data:  my.data2$weight
## t = -22.665, df = 69, p-value = 1
## alternative hypothesis: true mean is greater than 95
## 95 percent confidence interval:
##  89.04328      Inf
## sample estimates:
## mean of x 
##  89.45143
```

모수적 방법과 똑같이 해석하면 된다.
