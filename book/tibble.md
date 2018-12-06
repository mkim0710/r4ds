
# Tibble

## 들어가기

이 책 전체에서 우리는 전통적인 `data.frame`(데이터프레임) 대신 'tibble’로 작업한다. Tibble도 사실은 데이터프레임이지만, 좀 더 편리하게 사용할 수 있도록 오래된 동작들을 수정한 것이다. R은 오래된 언어이며, 10년 또는 20년 전에 유용했던 동작들이 이제는 그렇지 않은 경우가 있다. 기존 코드를 손상시키지 않으면서 베이스 R을 변경하기가 어렵기 때문에 대부분의 혁신은 패키지로 나타난다. 여기서 우리는 **tibble** 패키지에 대해서 설명할 것인데, 이는 tidyverse 작업을 약간 쉽게 만들어주는, 고집있는 데이터프레임을 제공한다. 대부분의 경우 tibble과 데이터프레임을 같은 의미로 사용할 것이지만, 특별히 R의 내장 데이터프레임에 대해 이야기할 때는 `data.frames`로 호칭할 것이다.

tibble에 대해 이 장의 내용보다 더 자세히 배우고 싶으면 `vignette("tibble")`를 이용하면 된다. 

### 준비하기

이 장에서는 tidyverse 패키지의 핵심 중 하나인 **tibble** 패키지를 살펴본다.  


```r
library(tidyverse)
```

## tibble 생성하기 {#tibbles}

이 책에서 사용하는 대부분의 함수는 tidyverse의 통합 특성 중 하나인 tibble을 생성한다. 대부분의 다른 R 패키지는 일반적인 데이터프레임을 사용하므로, 데이터프레임을 tibble로 강제 변환해야 할 경우가 있다. `as_tibble()`를 사용하면 된다.


```r
as_tibble(iris)
#> # A tibble: 150 x 5
#>   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#>          <dbl>       <dbl>        <dbl>       <dbl> <fct>  
#> 1          5.1         3.5          1.4         0.2 setosa 
#> 2          4.9         3            1.4         0.2 setosa 
#> 3          4.7         3.2          1.3         0.2 setosa 
#> 4          4.6         3.1          1.5         0.2 setosa 
#> 5          5           3.6          1.4         0.2 setosa 
#> 6          5.4         3.9          1.7         0.4 setosa 
#> # ... with 144 more rows
```

`tibble()`을 사용하여 개별 벡터로부터 새로운 tibble을 만들 수 있다. `tibble()`은 길이가 1인 입력을 자동으로 재사용하며, 여기에서 보이는 것처럼, 방금 만든 변수를 참조할 수도 있다.


```r
tibble(
  x = 1:5, 
  y = 1, 
  z = x ^ 2 + y
)
#> # A tibble: 5 x 3
#>       x     y     z
#>   <int> <dbl> <dbl>
#> 1     1     1     2
#> 2     2     1     5
#> 3     3     1    10
#> 4     4     1    17
#> 5     5     1    26
```

`data.frame()`에 이미 익숙하다면 `tibble()`은 동작의 규모가 훨씬 작다는 것에 주의해야 한다. 즉, 입력의 유형을 절대로 변경하지 않고(예를 들어, 문자열을 팩터형으로 변환하지 않는다!), 변수의 이름을 바꾸거나, 행 이름을 생성하지 않는다.

tibble은 R 변수명으로는 유효하지 않은 이름(비구문론적 이름)도 열 이름으로 가질 수 있다. 예를 들어, 문자로 시작하지 않거나 공백과 같은 비정상적인 문자가 포함될 수 있다. 이 변수들을 참조하려면 역따옴표(backticks, `` ` ``)로 감싸야 한다. 


```r
tb <- tibble(
  `:)` = "smile", 
  ` ` = "space",
  `2000` = "number"
)
tb
#> # A tibble: 1 x 3
#>   `:)`  ` `   `2000`
#>   <chr> <chr> <chr> 
#> 1 smile space number
```
 
ggplot2, dplyr 및 tidyr 과 같은 다른 패키지에서 이러한 변수로 작업할 때도 역따옴표가 필요하다.  

tibble을 만드는 또 다른 방법은 `tribble()` (전치된(**tr**ansposed) tibble의 줄임말)을 사용하는 것이다. `tribble()`은 코드로 데이터 입력을 하기 위해 고안되었다. 열 헤더는 공식으로 정의되고(즉, `~`로 시작), 입력은 쉼표로 구분된다. 이렇게 하면 적은 양의 데이터를 읽기 쉬운 형태로 배치할 수 있다.


```r
tribble(
  ~x, ~y, ~z,
  #--|--|----
  "a", 2, 3.6,
  "b", 1, 8.5
)
#> # A tibble: 2 x 3
#>   x         y     z
#>   <chr> <dbl> <dbl>
#> 1 a         2   3.6
#> 2 b         1   8.5
```

나는 헤더가 있는 곳을 매우 명확하게 하기 위해 종종 주석(`#`으로 시작하는 줄)을 추가한다.

## Tibble vs. data.frame

tibble과 전통적인 `data.frame`의 용법에는 두 가지 주요 차이점이 있는데, 화면출력과 서브셋하기가 그것이다.

### 화면출력

tibble에는 처음 10 개의 행과, 화면에 들어가는 열 모두를 보여주는 정교한 화면출력 방법이 있다. 이를 이용하면 대용량 데이터 작업을 훨씬 쉽게 할 수 있다. 또한 `str()`에서 가져온 기능으로 각 열의 유형을 열이름과 더불어 표시한다.


```r
tibble(
  a = lubridate::now() + runif(1e3) * 86400,
  b = lubridate::today() + runif(1e3) * 30,
  c = 1:1e3,
  d = runif(1e3),
  e = sample(letters, 1e3, replace = TRUE)
)
#> # A tibble: 1,000 x 5
#>   a                   b              c     d e    
#>   <dttm>              <date>     <int> <dbl> <chr>
#> 1 2018-12-04 19:30:32 2018-12-11     1 0.368 h    
#> 2 2018-12-05 13:35:41 2018-12-16     2 0.612 n    
#> 3 2018-12-05 07:59:20 2018-12-26     3 0.415 l    
#> 4 2018-12-04 21:20:38 2018-12-25     4 0.212 x    
#> 5 2018-12-04 17:44:54 2018-12-22     5 0.733 a    
#> 6 2018-12-05 04:45:51 2018-12-18     6 0.460 v    
#> # ... with 994 more rows
```

tibble은 큰 데이터프레임을 화면출력할 때 실수로 콘솔을 넘어가지 않도록 설계되었다. 그러나 때로는 기본 디스플레이보다 더 많은 출력이 필요하곤 한다. 도움이 될 수 있는 몇 가지 옵션이 있다.

먼저 데이터프레임을 명시적으로 `print()`로 화면출력하고 디스플레이의 행 수(`n`)와 너비(`width`)를 제어할 수 있다. `width = Inf`를 하면 모든 열을 표시한다. 


```r
nycflights13::flights %>% 
  print(n = 10, width = Inf)
```

options를 설정하여 기본 출력 동작을 제어할 수도 있다. 

* `options(tibble.print_max = n, tibble.print_min = m)`: `m` 행 이상인 경우, `n`행만 출력한다. 모든 행을 항상 표시하려면 `option(dplyr.print_min = Inf)`을 사용하라. 

* `option(tibble.width = Inf)` 을 사용하면 화면 너비와 상관없이 항상 모든 열을 출력한다.

`package?tibble`로 패키지 도움말을 찾아보면 옵션의 전체 목록을 볼 수 있다.

마지막 방법은 전체 데이터셋을 스크롤하여 볼 수 있도록 RStudio의 내장 뷰어를 사용하는 것이다. 긴 데이터 조작 연쇄 마지막에도 이 방법은 유용하다.


```r
nycflights13::flights %>% 
  View()
```

### 서브셋하기

지금까지 배운 모든 도구를 완전한 데이터프레임에 사용했었다. 변수 하나를 추출하려면 새로운 도구인 `$` 및 `[[`이 필요하다. `[[`는 이름이나 위치로 추출할 수 있다. `$`는 이름으로만 추출할 수 있지만 타이핑을 조금 덜 해도 된다.  


```r
df <- tibble(
  x = runif(5),
  y = rnorm(5)
)

# Extract by name
df$x
#> [1] 0.434 0.395 0.548 0.762 0.254
df[["x"]]
#> [1] 0.434 0.395 0.548 0.762 0.254

# Extract by position
df[[1]]
#> [1] 0.434 0.395 0.548 0.762 0.254
```

파이프에서 이것들을 사용하려면 특별한 플레이스홀더(placeholder)인 `.`를 사용해야 한다.


```r
df %>% .$x
#> [1] 0.434 0.395 0.548 0.762 0.254
df %>% .[["x"]]
#> [1] 0.434 0.395 0.548 0.762 0.254
```

tibble은 `data.frame`보다 좀 더 엄격하다. 절대로 부분 매칭을 사용하지 않으며, 접근하려는 열이 존재하지 않는 경우에는 경고를 생성한다. 

## 이전 코드와 상호작용

일부 오래된 함수는 tibble에서 동작하지 않는다. 이러한 함수를 사용하려면 `as.data.frame()`를 사용하여 tibble을 `data.frame`으로 되돌려야 한다. 


```r
class(as.data.frame(tb))
#> [1] "data.frame"
```

오래된 함수 중 일부가 tibble에서 작동하지 않는 주된 이유는 `[` 함수 때문이다. 이 책에서는 `[`를 별로 사용하지 않는데, `dplyr::filter()`와 `dplyr::select()`가 같은 문제를 더 명확한 코드로 해결할 수 있기 때문이다 (단, [벡터 서브셋하기](#vector-subsetting)에서 좀 더 자세히 알 수 있다). 베이스 R의 데이터프레임을 사용하면 `[`는 어떨 때는 데이터프레임을 반환하고, 또 어떨 때는 벡터를 반환한다. tibble에서 `[`는 항상 다른 tibble을 반환한다.  

## 연습문제

1. 어떤 객체가 tibble인지 알 수 있는 방법은 무엇인가? (힌트: 일반 데이터프레임인 `mtcars`를 화면출력해보라.)

1. `data.frame`과 이에 해당하는 tibble에서 다음 연산들을 비교하고 차이를 밝혀보라. 차이점은 무엇인가? 데이터프레임의 기본 동작이 혼란스러운 점은 무엇인가? 

    
    ```r
    df <- data.frame(abc = 1, xyz = "a")
    df$x
    df[, "xyz"]
    df[, c("abc", "xyz")]
    ```

1. 객체에 변수 이름을 저장하고 있는 경우(예: `var <- "mpg"`), tibble에서 이 참조 변수를 어떻게 추출할 수 있는가?

1. 다음의 데이터프레임에서 비구문론적 이름을 참조하는 방법을 연습해보라. 

  1. 1 이라는 이름의 변수를 추출하기.

  1. 1 대 2의 산점도를 플롯팅 하기.

  1. 열 2를 열 1로 나누어, 3이라는 새로운 열을 생성하기.

  1. 열의 이름을 one, two, three 로 변경하기. 
  
    
    ```r
    annoying <- tibble(
      `1` = 1:10,
      `2` = `1` * 2 + rnorm(length(`1`))
    )
    ```

1. `tibble::enframe()`은 어떤 동작을 하는가? 언제 사용하겠는가?

1. tibble의 바닥글(footer)에 화면출력되는 열 이름의 개수를 제어하는 옵션은 무엇인가? 