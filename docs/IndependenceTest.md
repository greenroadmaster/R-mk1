# 독립성 검정(Test of Independence)

## 데이터 불러오기


```r
library(ggplot2)
data(mpg)
mpg <- as.data.frame(mpg)
str(mpg)
```

```
## 'data.frame':	234 obs. of  11 variables:
##  $ manufacturer: chr  "audi" "audi" "audi" "audi" ...
##  $ model       : chr  "a4" "a4" "a4" "a4" ...
##  $ displ       : num  1.8 1.8 2 2 2.8 2.8 3.1 1.8 1.8 2 ...
##  $ year        : int  1999 1999 2008 2008 1999 1999 2008 1999 1999 2008 ...
##  $ cyl         : int  4 4 4 4 6 6 6 4 4 4 ...
##  $ trans       : chr  "auto(l5)" "manual(m5)" "manual(m6)" "auto(av)" ...
##  $ drv         : chr  "f" "f" "f" "f" ...
##  $ cty         : int  18 21 20 21 16 18 18 18 16 20 ...
##  $ hwy         : int  29 29 31 30 26 26 27 26 25 28 ...
##  $ fl          : chr  "p" "p" "p" "p" ...
##  $ class       : chr  "compact" "compact" "compact" "compact" ...
```

ggplot2 패키지에 있는 mpg를 불러온다.

11개의 변수와 234개의 관측치가 있다.



## 교차표(cross tabulation) 생성


```r
library(prettyR)
(crosstab<-xtabs(formula = ~fl+drv, data = mpg))
```

```
##    drv
## fl   4  f  r
##   c  0  1  0
##   d  2  3  0
##   e  6  1  1
##   p 20 25  7
##   r 75 76 17
```


fl(연료타입)과 drv(구동방식)의 교차표를 만들어 살펴본다.

데이터셋의 분포를 알고, 상황에 맞는 함수를 사용한다.

## 독립성 검정이란
두 변수가 독립적인지, 즉 관계가 있는지를 알려주는 통계기법이다.

변수가 범주형인 경우 카이제곱 검정을 사용하고,

연속형인 경우 공분산을 사용한다.


## 범주형 변수간에 검정

### 카이제곱 검정이란

카이제곱 분포에 기초한 통계적 방법으로, 관찰된 빈도가 기대되는 빈도와 의미있게 다른지의 여부를 검증하기 위해 사용되는 검증방법이다.

*자료가 빈도로 주어졌을 때, 특히 명목척도 자료의 분석에 이용된다.

*쉽게 말하면, 변수간의 차이가 있는지 없는지를 검정해준다. (변수간의 독립성을 검정해준다)

귀무가설(H0): A, B는 서로 차이가 없다 (관계가 없다, 독립이다)

대립가설(H1): A, B는 서로 차이가 있다 (관계가 있다, 독립이 아니다)
Pearson’s Chi-squared Test(피어슨의 카이제곱 검정)

표본의 크기가 큰 경우에 사용한다.



```r
chisq.test(crosstab, correct = TRUE)
```

```
## Warning in chisq.test(crosstab, correct = TRUE): Chi-squared approximation may
## be incorrect
```

```
## 
## 	Pearson's Chi-squared test
## 
## data:  crosstab
## X-squared = 6.5618, df = 8, p-value = 0.5846
```
p-value 값이 유의수준(0.05)보다 크기 때문에 귀무가설 채택.

따라서, 연료타입과 구동방식은 관계가 없다.

### Fisher’s Exact Test(피셔의 검정)

+ 표본수가 적거나
+ 표본의 교차표를 보았을때, 특정 셀에 매우 치우치게 분포되어 있을 경우

카이제곱 검정 이후, 피셔의 정확한 검정을 해야 한다.

앞서 살펴본 교차표에서 연료타입 p,r에 치우쳐 있다.
따라서 피셔의 정확한 검정을 한다.


```r
fisher.test(crosstab, alternative = 'two.sided')
```

```
## 
## 	Fisher's Exact Test for Count Data
## 
## data:  crosstab
## p-value = 0.5121
## alternative hypothesis: two.sided
```

정확한 검정을 적용해도 같은 결과가 나온다.


## 연속형 변수간에 검정
### 공분산(covariance)

공분산이란

+ 두 확률변수가 얼마나 함께 변화하는지 측정
+ 양의 값이면 비례관계, 음의 값이면 반비례관계, 0이면 관계가 없다. 

++ 한 변수가 커질 때, 다른 변수가 함께 커지거나 한 변수가 작아질 때, 다른 변수가 함께 작아지는 것과 같이, 크기변화의 방향이 같다면, 공분산은 양의값을 가짐

++ 한 변수가 커질 때, 다른 변수가 작아지거나 한 변수가 작아질 때, 다른 변수가 커지는 것과 같이, 크기변화의 방향이 다르다면, 공분산은 음의값을 가짐

++ 만약 두 변수의 값이 서로 상관없이 변한다면, 공분산은 0 임


```r
cov(mpg$cty,mpg$hwy)
```

```
## [1] 24.22543
```

mpg 데이터셋에 있는 cty(도시연비)와 hwy(고속도로 연비)의 공분산 검정을 한다.

24.22로 양의 값이 나왔기 때문에 두 변수는 관계가 있다.

