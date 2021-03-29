# 등분산성 검정(Homogeneity of Variance Test)

## 데이터 불러오기

```r
data(ToothGrowth)
data(PlantGrowth)
str(ToothGrowth)
```

```
## 'data.frame':	60 obs. of  3 variables:
##  $ len : num  4.2 11.5 7.3 5.8 6.4 10 11.2 11.2 5.2 7 ...
##  $ supp: Factor w/ 2 levels "OJ","VC": 2 2 2 2 2 2 2 2 2 2 ...
##  $ dose: num  0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 0.5 ...
```


```r
str(PlantGrowth)
```

```
## 'data.frame':	30 obs. of  2 variables:
##  $ weight: num  4.17 5.58 5.18 6.11 4.5 4.61 5.17 4.53 5.33 5.14 ...
##  $ group : Factor w/ 3 levels "ctrl","trt1",..: 1 1 1 1 1 1 1 1 1 1 ...
```


R에 있는 기본 데이터셋인 ToothGrowth를 불러온다.

3개의 변수와 60개의 관측치가 있다.

## 등분산성 검정이란
+ 등분산성인지 아닌지를 구분하는 방법이다.

+ 등분산성은 분산분석할때 기본적으로 만족해야되는 조건 중 한가지로, 분석하는 집단들의 분산이 같음을 의미한다.

+ 검정의 방법은 두 집단, 다 집단일때 달라진다. 또한 모수, 비모수에 따라 달라진다.


## 두 집단일때
### F test

반드시 정규성이 있어야 한다.

귀무가설 H0 : 등분산성이 있다(분산의 차이가 없다).

대립가설 H1 : 등분산성이 없다(분산의 차이가 있다).


```r
res.ftest <- var.test(len ~ supp, data = ToothGrowth, alternative = "two.sided")
res.ftest
```

```
## 
## 	F test to compare two variances
## 
## data:  len by supp
## F = 0.6386, num df = 29, denom df = 29, p-value = 0.2331
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  0.3039488 1.3416857
## sample estimates:
## ratio of variances 
##          0.6385951
```

두 변수 len과 supp의 등분산성을 검정했을때,

p-value 값이 유의수준(0.05)보다 큰 0.2331로 나왔다.

따라서 귀무가설을 채택하여 등분산성이 있다.

여기서 , alternative는 양측 검정할지 단측 검정할지를 설정해준다.


```r
res.ftest$estimate
```

```
## ratio of variances 
##          0.6385951
```

등분산 검정 함수에는 분산이 얼마나 같은지 알려주는 estimate가 있다.
여기서는 약 63.8% 분산이 같다.

## 다집단 일때
### Bartlett’s test

+ 반드시 정규성이 있어야 한다.

+ 귀무가설 H0 : 등분산성이 있다(분산의 차이가 없다).

+ 대립가설 H1 : 등분산성이 없다(분산의 차이가 있다).


```r
bartlett.test(weight ~ group, data = PlantGrowth)
```

```
## 
## 	Bartlett test of homogeneity of variances
## 
## data:  weight by group
## Bartlett's K-squared = 2.8786, df = 2, p-value = 0.2371
```

PlantGrowth의 group 변수에는 세 가지 요인이 있기 때문에 다 집단이다.

그리고 p-value 값이 유의수준(0.05)보다 큰 0.2371로 나왔다.

따라서 귀무가설을 채택하여 등분산성이 있다.



### Levene’s test

{car} 패키지에 있다.

정규성이 있어야 하는 전제 조건에 덜 민감하다.

귀무가설 H0 : 등분산성이 있다(분산의 차이가 없다).

대립가설 H1 : 등분산성이 없다(분산의 차이가 있다).


```r
library(car)
```

```
## Loading required package: carData
```

```r
leveneTest(weight ~ group, data = PlantGrowth)
```

```
## Levene's Test for Homogeneity of Variance (center = median)
##       Df F value Pr(>F)
## group  2  1.1192 0.3412
##       27
```

p-value 값이 유의수준(0.05)보다 큰 0.3412로 나왔다.

따라서 귀무가설을 채택하여 등분산성이 있다.



### Fligner-Killeen test

비모수적 방법이다.

따라서 표본의 크기, 확률 분포와 상관이 없다.

귀무가설 H0 : 등분산성이 있다(분산의 차이가 없다).

대립가설 H1 : 등분산성이 없다(분산의 차이가 있다).


```r
fligner.test(weight ~ group, data = PlantGrowth)
```

```
## 
## 	Fligner-Killeen test of homogeneity of variances
## 
## data:  weight by group
## Fligner-Killeen:med chi-squared = 2.3499, df = 2, p-value = 0.3088
```

PlantGrowth는 표본이 충분히 커서 모수적 방법을 쓰지만, 여기서 연습용으로 다룬다.

p-value 값이 유의수준(0.05)보다 큰 0.3088로 나왔다.

따라서 귀무가설을 채택하여 등분산성이 있다.

