경영통계S/W실습 기말 프로젝트
================
이은서
2021 06 09

<style type="text/css">
  body{
  font-size: 15pt;
  font-family: Calibri
}
</style>

## 프로젝트 주제

아라비카 원두의 산미/뒷맛(Acidity/Aftertaste)과 총 커핑 점수(Total Cup
Points)의 관계

## 프로젝트 동기 / 이유

나는 평소 커피를 즐겨 마시고 그 중 제일 즐겨 마시는 메뉴는
아메리카노이다. 나는 신 맛을 별로 좋아하지 않고 씁쓸한 맛의 커피를
좋아하기 때문에 보통은 여운이 깔끔하고 신맛이 적은 원두를 선택하여
마시는 편이다. 그렇지만 나와 반대로 커피의 신맛을 좋아해 산미가 있는
원두만을 찾는 사람들도 있다고 한다. 그래서 산미가 있는 원두는 어떠한
기준으로 평가에 영향을 끼치는지, 그리고 커핑에서 가장 중요한 평가 요소
중 하나인 뒷맛과 산미는 어떠한 상관관계에 있는지 알아보고자 한다.
마지막으로 산미와 뒷맛 총 커핑 점수와의 관계 또한 알아보고자 한다.

## 데이터 수집 계획

kaggle에서 [2018년 원두 품질
데이터](https://www.kaggle.com/volpatto/coffee-quality-database-from-cqi)를
수집한다.

## 데이터 기본 분석

### 데이터 명세

``` r
beans <- read.csv("arabica_data_cleaned.csv")
```

데이터 분석에 필요한 평가항목 변수들의 첫 10줄만 표시해보았다.

``` r
head(beans[c(2, 20:31)])
```

    ##   Species Processing.Method Aroma Flavor Aftertaste Acidity Body Balance
    ## 1 Arabica      Washed / Wet  8.67   8.83       8.67    8.75 8.50    8.42
    ## 2 Arabica      Washed / Wet  8.75   8.67       8.50    8.58 8.42    8.42
    ## 3 Arabica                    8.42   8.50       8.42    8.42 8.33    8.42
    ## 4 Arabica     Natural / Dry  8.17   8.58       8.42    8.42 8.50    8.25
    ## 5 Arabica      Washed / Wet  8.25   8.50       8.25    8.50 8.42    8.33
    ## 6 Arabica     Natural / Dry  8.58   8.42       8.42    8.50 8.25    8.33
    ##   Uniformity Clean.Cup Sweetness Cupper.Points Total.Cup.Points
    ## 1         10        10        10          8.75            90.58
    ## 2         10        10        10          8.58            89.92
    ## 3         10        10        10          9.25            89.75
    ## 4         10        10        10          8.67            89.00
    ## 5         10        10        10          8.58            88.83
    ## 6         10        10        10          8.33            88.83

### 데이터 기본 통계

다음은 데이터 기본 통계치이다.

``` r
summary(beans[c(2, 20:31)])
```

    ##    Species          Processing.Method      Aroma           Flavor     
    ##  Length:1311        Length:1311        Min.   :0.000   Min.   :0.000  
    ##  Class :character   Class :character   1st Qu.:7.420   1st Qu.:7.330  
    ##  Mode  :character   Mode  :character   Median :7.580   Median :7.580  
    ##                                        Mean   :7.564   Mean   :7.518  
    ##                                        3rd Qu.:7.750   3rd Qu.:7.750  
    ##                                        Max.   :8.750   Max.   :8.830  
    ##    Aftertaste       Acidity           Body          Balance     
    ##  Min.   :0.000   Min.   :0.000   Min.   :0.000   Min.   :0.000  
    ##  1st Qu.:7.250   1st Qu.:7.330   1st Qu.:7.330   1st Qu.:7.330  
    ##  Median :7.420   Median :7.500   Median :7.500   Median :7.500  
    ##  Mean   :7.398   Mean   :7.533   Mean   :7.518   Mean   :7.518  
    ##  3rd Qu.:7.580   3rd Qu.:7.750   3rd Qu.:7.670   3rd Qu.:7.750  
    ##  Max.   :8.670   Max.   :8.750   Max.   :8.580   Max.   :8.750  
    ##    Uniformity       Clean.Cup        Sweetness      Cupper.Points   
    ##  Min.   : 0.000   Min.   : 0.000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.:10.000   1st Qu.:10.000   1st Qu.:10.000   1st Qu.: 7.250  
    ##  Median :10.000   Median :10.000   Median :10.000   Median : 7.500  
    ##  Mean   : 9.833   Mean   : 9.833   Mean   : 9.903   Mean   : 7.498  
    ##  3rd Qu.:10.000   3rd Qu.:10.000   3rd Qu.:10.000   3rd Qu.: 7.750  
    ##  Max.   :10.000   Max.   :10.000   Max.   :10.000   Max.   :10.000  
    ##  Total.Cup.Points
    ##  Min.   : 0.00   
    ##  1st Qu.:81.17   
    ##  Median :82.50   
    ##  Mean   :82.12   
    ##  3rd Qu.:83.67   
    ##  Max.   :90.58

위 결과를 보면

1.  범주형 변수에 대해서는 요약이 제대로 안된 것을 볼 수 있다.
2.  변수마다 최소값 0을 갖고 있는 것을 볼 수 있다.

우선 제대로 통계요약이 될 수 있도록 범주형 변수에 레벨을 넣어주고 최소값
0을 제외한다.

그리고 다시 기본 통계를 실시한다.

``` r
summary(beans[c(2, 20:31)])
```

    ##     Species                     Processing.Method     Aroma      
    ##  Arabica:1004   Washed / Wet             :737     Min.   :5.080  
    ##                 Natural / Dry            :179     1st Qu.:7.420  
    ##                 Pulped natural / honey   : 10     Median :7.580  
    ##                 Semi-washed / Semi-pulped: 53     Mean   :7.567  
    ##                 Other                    : 25     3rd Qu.:7.750  
    ##                                                   Max.   :8.750  
    ##      Flavor        Aftertaste       Acidity           Body      
    ##  Min.   :6.170   Min.   :6.170   Min.   :5.250   Min.   :6.330  
    ##  1st Qu.:7.330   1st Qu.:7.170   1st Qu.:7.330   1st Qu.:7.330  
    ##  Median :7.500   Median :7.420   Median :7.500   Median :7.500  
    ##  Mean   :7.513   Mean   :7.385   Mean   :7.526   Mean   :7.504  
    ##  3rd Qu.:7.670   3rd Qu.:7.580   3rd Qu.:7.670   3rd Qu.:7.670  
    ##  Max.   :8.830   Max.   :8.670   Max.   :8.750   Max.   :8.500  
    ##     Balance        Uniformity      Clean.Cup        Sweetness     
    ##  Min.   :6.080   Min.   : 6.00   Min.   : 0.000   Min.   : 1.330  
    ##  1st Qu.:7.330   1st Qu.:10.00   1st Qu.:10.000   1st Qu.:10.000  
    ##  Median :7.500   Median :10.00   Median :10.000   Median :10.000  
    ##  Mean   :7.499   Mean   : 9.87   Mean   : 9.854   Mean   : 9.932  
    ##  3rd Qu.:7.670   3rd Qu.:10.00   3rd Qu.:10.000   3rd Qu.:10.000  
    ##  Max.   :8.580   Max.   :10.00   Max.   :10.000   Max.   :10.000  
    ##  Cupper.Points   Total.Cup.Points
    ##  Min.   :5.170   Min.   :59.83   
    ##  1st Qu.:7.250   1st Qu.:81.17   
    ##  Median :7.500   Median :82.42   
    ##  Mean   :7.473   Mean   :82.12   
    ##  3rd Qu.:7.670   3rd Qu.:83.58   
    ##  Max.   :8.750   Max.   :90.58

### 변수별 점수 분포

다음은 변수별 분포를 ggplot을 이용해 시각화 해보았다.

![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-2.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-3.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-4.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-5.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-6.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-7.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-8.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-9.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-10.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-11.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-12.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-6-13.png)<!-- -->

품종과 제조공법 그래프를 제외한 히스토그램들의 모양을 살펴보면
Uniformity일관성, Clean.Cup깔끔함, Sweetness단맛 변수를 제외한 모든
변수들이 종모양으로 점수 분포가 골고루 이루어져 있는 것을 알 수 있다.
Uniformity일관성, Clean.Cup깔끔함, Sweetness단맛 변수의 히스토그램은
점수가 거의 다 10점 쪽으로 몰려 있어 저런 모양의 분포로 이루어진 것을 알
수 있다.

가설검정에 이용할 변수인 Aftertaste뒷맛와 Acidity산미의 히스토그램을
살펴보면 10점 만점에 7-8점 사이에 점수 분포가 이루어져있는 것을 확인할
수 있다. 보통 Acidity는 8점을 기준으로 과일같은 산미를 가지고 있는지
(8점 이상) 아니면 시큼한 맛을 가지고 있는지(8점 미만)을 확인하는데 위
분포를 보면 8점 이상으로 평가된 원두가 8점 미만으로 평가된 원두보다 더
많은 것을 볼 수 있다.

## 가설검정 및 회귀분석 결과

위 변수들 중 Acidity산미와 Aftertaste여운의 상관관계와 이 둘 변수와
Total.Cup.Points총 커핑 점수와의 상관관계를 알아보기 위하여 가설검정과
회귀분석을 실시한다.

설정한 대립가설은 다음과 같다.

1.  Acidity산미와 Aftertaste뒷맛은 서로 양의 상관관계(+)에 있을 것이다.
2.  Total.Cup.Points총 커핑 점수에 Acidity산미와 Aftertaste뒷맛은 정의
    영향(+)을 끼칠 것이다.

### 가설검정

먼저 Acidity와 Aftertaste의 상관관계를 알아본다.

``` r
with(beans, cor.test(Acidity, Aftertaste))
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  Acidity and Aftertaste
    ## t = 30.193, df = 1002, p-value < 2.2e-16
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.6563627 0.7212730
    ## sample estimates:
    ##       cor 
    ## 0.6902038

``` r
ggplot(beans, aes(Acidity, Aftertaste)) + geom_point() + geom_smooth(method = "lm")
```

    ## `geom_smooth()` using formula 'y ~ x'

![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

cor.test() 함수와 산점도를 이용하여 Aftertaste와 Acidity의 상관계수를
구해보니

1.  0.6902038로 두 변수가 양의 상관관계에 놓여있음을 알 수 있다.

2.  산점도와 회귀추세선 또한 양의 방향으로 두 변수가 상관관계에
    놓여있음을 보여준다.

3.  p값 또한 0.05 미만으로 대립가설을 채택한다.

이는 “뒷맛이 좋은 원두에서 좋은 산미의 맛이 날 수 있음”을 뜻한다고 볼 수
있다. 8점 이상의 산미는 기분 좋은 산미를 맛 볼 수 있다는 뜻이고, 뒷맛
또한 커피를 마시고 난 후에 계속해서 기분 좋은 여운이 남아있음을 뜻하는
것이니 이 상관관계를 당연하다고 볼 수 도 있겠다.

그렇다면 Aftertaste, Acidity와 Total.Cup.Points의 상관관계는 어떻게
될까?

``` r
with(beans, cor.test(Aftertaste, Total.Cup.Points))
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  Aftertaste and Total.Cup.Points
    ## t = 48.815, df = 1002, p-value < 2.2e-16
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.8197179 0.8564460
    ## sample estimates:
    ##       cor 
    ## 0.8390352

``` r
with(beans, cor.test(Acidity, Total.Cup.Points))
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  Acidity and Total.Cup.Points
    ## t = 31.904, df = 1002, p-value < 2.2e-16
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.6777760 0.7392782
    ## sample estimates:
    ##       cor 
    ## 0.7098777

``` r
ggplot(beans, aes(Acidity, Total.Cup.Points)) + geom_point() + geom_smooth(method = "lm")
```

    ## `geom_smooth()` using formula 'y ~ x'

![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
ggplot(beans, aes(Aftertaste, Total.Cup.Points)) + geom_point() + geom_smooth(method = "lm")
```

    ## `geom_smooth()` using formula 'y ~ x'

![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->

마찬가지로 cor.test()와 산점도를 이용해 상관관계를 분석해보았다.

1.  Aftertaste와 Total.Cup.Points의 상관계수는 0.8390352, Acidity와
    Total.Cup.Points의 상관계수는 0.7098777로 두 변수 모두
    Total.Cup.Points와 양의 상관관계를 갖는 것을 알 수 있다. 다만
    Aftertaste와의 상관관계가 더 큰 것으로 보아 Aftertaste가 Acidity보다
    Total.Cup.Points에 더 센 영향을 끼치는 것을 볼 수 있다.

2.  산점도와 회귀추세선 또한 양의 방향으로 두 변수가 상관관계에
    놓여있음을 보여준다.

3.  p값 또한 0.05 미만으로 대립가설을 채택한다.

이를 바탕으로 뒷맛과 산미 둘 다 총 커핑점수에 정의 영향을(+)을 끼치는
것을 증명할 수 있다.

### 회귀분석

Acidity와 Aftertaste가 Total.Cup.Points에 정의 영향을(+) 끼친다는 것을
확인하기 위해 회귀분석을 실시한다. 이를 다중회귀모형으로 분석해본 결과
다음과 같은 결과를 얻을 수 있었다.

``` r
lm.a.a = lm(Total.Cup.Points ~ Acidity + Aftertaste, beans)
summary(lm.a.a)
```

    ## 
    ## Call:
    ## lm(formula = Total.Cup.Points ~ Acidity + Aftertaste, data = beans)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -18.8294  -0.3335   0.1519   0.6104   3.5529 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  27.1145     1.0906   24.86   <2e-16 ***
    ## Acidity       2.1350     0.1916   11.14   <2e-16 ***
    ## Aftertaste    5.2727     0.1773   29.74   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.351 on 1001 degrees of freedom
    ## Multiple R-squared:  0.7366, Adjusted R-squared:  0.7361 
    ## F-statistic:  1400 on 2 and 1001 DF,  p-value: < 2.2e-16

1.  우선 p값이 0.05 미만이고 R제곱값이 0.73으로 모형이 적합하고 변수
    설명이 가능함을 확인할 수 있다.

2.  Acidity산미가 1씩 증가할 때에는 Total.Cup.Points가 2.135만큼
    증가하지만 Aftertaste뒷맛이 1씩 증가할 때에는 Total.Cup.Points는
    5.2727씩 증가하는 것으로 보아 Aftertaste가 Acidity보다 총 점수에
    끼치는 영향이 더 셈을 알 수 있다.

위 회귀모형의 적합도를 검토하기 위해 plot()을 이용해 살펴본다.

``` r
plot(lm.a.a)
```

![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-10-2.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-10-3.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-10-4.png)<!-- -->

### 전체 평가요소와 Total.Cup.Points총 커핑 점수와의 상관관계 분석 회귀모형

그렇다면 Aftertaste와 Acidity 외에도 총 평가 점수에 영향을 크게 미치는
요소는 무엇일까? 데이터에 명시되어있는 평가요소 변수 중 Uniformity,
Clean.Cup, Sweetness를 제외한 6개를 가지고 다중회귀모형을 실시해
중요변수를 결정해보았다.

``` r
lm.all = lm(Total.Cup.Points ~ Aroma + Flavor + Aftertaste + Acidity + Body + Balance, beans)
summary(lm.all)
```

    ## 
    ## Call:
    ## lm(formula = Total.Cup.Points ~ Aroma + Flavor + Aftertaste + 
    ##     Acidity + Body + Balance, data = beans)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -17.1658  -0.1217   0.2101   0.5100   2.3932 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  22.3765     1.1622  19.254  < 2e-16 ***
    ## Aroma         0.5808     0.1880   3.089  0.00206 ** 
    ## Flavor        2.2923     0.2630   8.715  < 2e-16 ***
    ## Aftertaste    2.3844     0.2372  10.054  < 2e-16 ***
    ## Acidity       0.7226     0.1936   3.732  0.00020 ***
    ## Body          0.1736     0.2118   0.820  0.41256    
    ## Balance       1.8372     0.1906   9.640  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.198 on 997 degrees of freedom
    ## Multiple R-squared:  0.794,  Adjusted R-squared:  0.7928 
    ## F-statistic: 640.5 on 6 and 997 DF,  p-value: < 2.2e-16

6개 요소 중 Body의 p값이 0.41256으로 0.05보다 커 이 회귀모형에 적합한
요소가 아닌 것을 알 수 있고 나머지 요소의 p값은 0.05 미만으로 회귀모형의
적합성을 설명할 수 있다. 그 중 총 커핑점수에 큰 영향을 끼치는 요소는
앞에서 알아본 Aftertaste와 Flavor로 Aftertaste가 1씩 증가할 때 총 커핑
점수는 2.3844만큼 증가, Flavor가 1씩 증가할 때 총 커핑 점수는 2.2923
만큼 증가함을 확인할 수 있다.

``` r
step(lm.all, direction = "backward")
```

    ## Start:  AIC=368.94
    ## Total.Cup.Points ~ Aroma + Flavor + Aftertaste + Acidity + Body + 
    ##     Balance
    ## 
    ##              Df Sum of Sq    RSS    AIC
    ## - Body        1     0.964 1430.8 367.62
    ## <none>                    1429.8 368.94
    ## - Aroma       1    13.688 1443.5 376.51
    ## - Acidity     1    19.979 1449.8 380.88
    ## - Flavor      1   108.932 1538.7 440.66
    ## - Balance     1   133.273 1563.0 456.42
    ## - Aftertaste  1   144.975 1574.8 463.91
    ## 
    ## Step:  AIC=367.62
    ## Total.Cup.Points ~ Aroma + Flavor + Aftertaste + Acidity + Balance
    ## 
    ##              Df Sum of Sq    RSS    AIC
    ## <none>                    1430.8 367.62
    ## - Aroma       1    13.809 1444.5 375.26
    ## - Acidity     1    21.582 1452.3 380.65
    ## - Flavor      1   112.819 1543.6 441.82
    ## - Aftertaste  1   150.027 1580.8 465.74
    ## - Balance     1   155.711 1586.5 469.34

    ## 
    ## Call:
    ## lm(formula = Total.Cup.Points ~ Aroma + Flavor + Aftertaste + 
    ##     Acidity + Balance, data = beans)
    ## 
    ## Coefficients:
    ## (Intercept)        Aroma       Flavor   Aftertaste      Acidity      Balance  
    ##     22.7714       0.5833       2.3172       2.4079       0.7442       1.8861

중요변수를 결정하기 위해 step() 함수를 사용한 결과 Body를 제외한 요소를
종속변수로 가지는 회귀모형인 lm(formula = Total.Cup.Points \~ Aroma +
Flavor + Aftertaste + Acidity + Balance, data = beans)가 제일 적합한
모형으로 선정된 것을 볼 수 있다.

위 회귀모형의 적합도를 검토하기 위해 plot()을 이용해 살펴본다.

``` r
lm.afaab = lm(Total.Cup.Points ~ Aroma + Flavor + Aftertaste + Acidity + Balance, beans)
plot(lm.afaab)
```

![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-13-2.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-13-3.png)<!-- -->![](경통SW실습기말프로젝트_files/figure-gfm/unnamed-chunk-13-4.png)<!-- -->

## 시사점 도출

프로젝트 전에는 뒷맛보다는 산미가 커핑 점수에 있어 더 큰 영향을
끼칠것이고 뒷맛은 원두 평가에 있어 그렇게 중요하지 않다 생각하였다.
하지만 가설검정과 회귀분석 결과 산미와 뒷맛 둘 다 영향이 큰 평가
요소이지만 산미보다 뒷맛이 평가에 더 영향을 크게 끼치는 것을 확인할 수
있었다. 언제부턴가 산미가 있는 원두만이 맛있고 비싼 원두로 여겨졌었는데
산미만으로는 좋은 커피인지 그 맛을 결정하기는 힘들다는 말이다. 이 분석
결과가 앞으로 우리가 마실 커피에 있어 산미뿐만 아니라 여러가지 평가
요소로도 좋은 원두를 선택하여 맛있는 커피를 마실 수 있는 방법에 도움이
될 수 있으면 좋겠다.

Ende
