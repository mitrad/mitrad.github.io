---
layout: post
title: "stringr package를 이용한 문자열 조작"
date: 2014-07-04
---



최근 [R for everyone](http://www.amazon.com/Everyone-Advanced-Analytics-Graphics-Addison-Wesley/dp/0321888030)이라는 책을 읽었다. 그 중 **stringr** 이라는 패키지를 이용한 문자열 처리 방법이 나와 있어 이번 기회에 정리해 보려 한다. 

R 표준 **base** 패키지에 포함된 함수군와 비슷한 기능을 하는 것으로 보이지만 더 합리적인 출력형식을 가지므로 사용하기 편리함. 

이 패키지의 몇몇 특징을 살펴보면

  * factor와 character를 같은 방식으로 처리
  
  * 일관성 있는 함수 이름과 인수 
  
  * 다른 함수의 입력값으로 사용하기 편리한 출력값. 
    * 길이 0인 입력값에 대해 길이 0인 결과를 돌려줌
    * 입력값 `NA`가 포함되어 있을 때는 그 부분의 결과를 `NA`로 돌려줌
    
  * 사용빈도가 떨어지는 문자열 조작 처리를 과감하게 제거하여 간략화시킴

<!--more-->

## Installation
**ggplot2**를 설치하면 자동으로 설치됨
혹은


{% highlight r %}
> install.packages("stringr")
{% endhighlight %}

## 함수

### Basic string operation
  * `str_length(string)`

      * 문자열의 길이를 계산. `base::nchar(x)`와 같은 기능을 하는 함수. 단, `NA` 에 대해서는 2가 아닌 `NA`를 돌려줌. 
    
    {% highlight r %}
    > library(stringr)
    > str_length(c("i", "like", "programming", NA))
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1]  1  4 11 NA
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > nchar(c("i", "like", "programming", NA))
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1]  1  4 11  2
    {% endhighlight %}
  * `str_sub(string, start=1, end=-1)`
      * 문자열을 부분적으로 참조, 변경. `base::substr()`와 같은 기능을 하는 함수. 음수를 사용하여 문자열의 끝으로 부터의 위치를 지정할 수 있음. 
    
    {% highlight r %}
    > hw <- "Hadley Wickham"
    > str_sub(hw, 1, 6)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "Hadley"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > substr(hw, 1, 6)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "Hadley"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_sub(hw, end = 6)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "Hadley"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_sub(hw, -7)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "Wickham"
    {% endhighlight %}
      
  * `str_c(..., sep='', collapse=NULL)`, `str_join(..., sep='', collapse=NULL)`
    * 문자열을 통합. 디폴트의 `sep`가 스페이스 공백이 아니므로 `base::paste0()`와 비슷함.
    
    {% highlight r %}
    > str_c(letters[-26], " comes before ", letters[-1])
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #>  [1] "a comes before b" "b comes before c"
    #>  [3] "c comes before d" "d comes before e"
    #>  [5] "e comes before f" "f comes before g"
    #>  [7] "g comes before h" "h comes before i"
    #>  [9] "i comes before j" "j comes before k"
    #> [11] "k comes before l" "l comes before m"
    #> [13] "m comes before n" "n comes before o"
    #> [15] "o comes before p" "p comes before q"
    #> [17] "q comes before r" "r comes before s"
    #> [19] "s comes before t" "t comes before u"
    #> [21] "u comes before v" "v comes before w"
    #> [23] "w comes before x" "x comes before y"
    #> [25] "y comes before z"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > paste0(letters[-26], " comes before ", letters[-1])
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #>  [1] "a comes before b" "b comes before c"
    #>  [3] "c comes before d" "d comes before e"
    #>  [5] "e comes before f" "f comes before g"
    #>  [7] "g comes before h" "h comes before i"
    #>  [9] "i comes before j" "j comes before k"
    #> [11] "k comes before l" "l comes before m"
    #> [13] "m comes before n" "n comes before o"
    #> [15] "o comes before p" "p comes before q"
    #> [17] "q comes before r" "r comes before s"
    #> [19] "s comes before t" "t comes before u"
    #> [21] "u comes before v" "v comes before w"
    #> [23] "w comes before x" "x comes before y"
    #> [25] "y comes before z"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_c(letters, collapse = ", ")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z"
    {% endhighlight %}
    
  * `str_split(string, pattern, n=Inf)`

    * 문자열을 분리. `base::strsplit(x, split)` 와 대응하는 함수. 최대 n 개의 분할을 지정할 수 있음. 결과값은 list. 문자열을 정해진 n 개로 나누는  `str_split_fixed()` 도 있음. 결과값은 matrix.  
    
    {% highlight r %}
    > fruits <- c(
    +   "apples and oranges and pears and bananas",
    +   "pineapples and mangos and guavas"
    + )
    > str_split(fruits, " and ")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [[1]]
    #> [1] "apples"  "oranges" "pears"   "bananas"
    #> 
    #> [[2]]
    #> [1] "pineapples" "mangos"     "guavas"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > strsplit(fruits, "and")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [[1]]
    #> [1] "apples "   " oranges " " pears "   " bananas" 
    #> 
    #> [[2]]
    #> [1] "pineapples " " mangos "    " guavas"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_split(fruits, " and ", n = 3)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [[1]]
    #> [1] "apples"            "oranges"          
    #> [3] "pears and bananas"
    #> 
    #> [[2]]
    #> [1] "pineapples" "mangos"     "guavas"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_split(fruits, " and ", n = 2)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [[1]]
    #> [1] "apples"                       
    #> [2] "oranges and pears and bananas"
    #> 
    #> [[2]]
    #> [1] "pineapples"        "mangos and guavas"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_split_fixed(fruits, " and ", 4)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #>      [,1]         [,2]      [,3]     [,4]     
    #> [1,] "apples"     "oranges" "pears"  "bananas"
    #> [2,] "pineapples" "mangos"  "guavas" ""
    {% endhighlight %}

### Pattern Matching

  * `str_detect(string, pattern)`
    * 매치하는 곳이 있는지 없는지를 logical 값으로 돌려줌. `base::grepl(pattern, x)`과 대응.
    
    {% highlight r %}
    > fruit <- c("apple", "banana", "pear", "pinapple")
    > str_detect(fruit, "a")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] TRUE TRUE TRUE TRUE
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_detect(fruit, "^a")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1]  TRUE FALSE FALSE FALSE
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_detect(fruit, "a$")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] FALSE  TRUE FALSE FALSE
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_detect(fruit, "b")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] FALSE  TRUE FALSE FALSE
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_detect(fruit, "[aeiou]")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] TRUE TRUE TRUE TRUE
    {% endhighlight %}

* `str_count(string, pattern)`
    * 매치하는 곳의 수를 돌려줌. 
    
    {% highlight r %}
    > str_count(fruit, c("a", "b", "p", "p"))
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] 1 1 1 3
    {% endhighlight %}

* `str_locate(string, pattern)`
      * 처음으로 매치되는 곳의 start, end 위치를 행렬로 돌려줌.
    
    {% highlight r %}
    > str_locate(fruit, "e")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #>      start end
    #> [1,]     5   5
    #> [2,]    NA  NA
    #> [3,]     2   2
    #> [4,]     8   8
    {% endhighlight %}

  * `str_extract(string, pattern)`, `str_extract_all(string, pattern)`
    * 매치된 부분 문자열을 추출. 매치되지 않은 요소는 `NA`. `base::grep(pattern, x, value=TRUE)`는 매치된 요소만 원래의 형태로 돌려줌.
    
    {% highlight r %}
    > shopping_list <- c("apples x4", "flour", "sugar", "milk x2")
    > str_extract(shopping_list, "\\d")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "4" NA  NA  "2"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > grep("\\d", shopping_list, value = TRUE)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "apples x4" "milk x2"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_extract(shopping_list, "[a-z]+")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "apples" "flour"  "sugar"  "milk"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > grep("[a-z]+", shopping_list, value = TRUE)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "apples x4" "flour"     "sugar"     "milk x2"
    {% endhighlight %}

* `str_match(string, pattern)`, `str_match_all(string, pattern)`
    * 매치된 부분 문자열을 추출하고 참조를 행렬로 돌려줌。 `str_extract(string, pattern)`의 결과를 1열에 각 괄호에 매치된 이후의 결과가 2열 이후에 들어감. 예제를 보는 것이 이해에 도움이 됨.
    
    {% highlight r %}
    > strings <- c(" 219 733 8965", "329-293-8753 ", "banana", "595 794 7569",
    +   "387 287 6718", "apple", "233.398.9187  ", "482 952 3315",
    +   "239 923 8115", "842 566 4692", "Work: 579-499-7527", "$1000",
    +   "Home: 543.355.3679")
    > phone <- "([2-9][0-9]{2})[- .]([0-9]{3})[- .]([0-9]{4})"
    > 
    > str_extract(strings, phone)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #>  [1] "219 733 8965" "329-293-8753" NA            
    #>  [4] "595 794 7569" "387 287 6718" NA            
    #>  [7] "233.398.9187" "482 952 3315" "239 923 8115"
    #> [10] "842 566 4692" "579-499-7527" NA            
    #> [13] "543.355.3679"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_match(strings, phone)
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #>       [,1]           [,2]  [,3]  [,4]  
    #>  [1,] "219 733 8965" "219" "733" "8965"
    #>  [2,] "329-293-8753" "329" "293" "8753"
    #>  [3,] NA             NA    NA    NA    
    #>  [4,] "595 794 7569" "595" "794" "7569"
    #>  [5,] "387 287 6718" "387" "287" "6718"
    #>  [6,] NA             NA    NA    NA    
    #>  [7,] "233.398.9187" "233" "398" "9187"
    #>  [8,] "482 952 3315" "482" "952" "3315"
    #>  [9,] "239 923 8115" "239" "923" "8115"
    #> [10,] "842 566 4692" "842" "566" "4692"
    #> [11,] "579-499-7527" "579" "499" "7527"
    #> [12,] NA             NA    NA    NA    
    #> [13,] "543.355.3679" "543" "355" "3679"
    {% endhighlight %}

  * `str_replace(string, pattern, replacement)`
    * 매치되지 않은 부분은 그대로 매치된 부분만 치환함.  `base::sub(pattern, replacement, x)` 와 대응. `base::gsub()`과 같이 모든 매치부분을 치환하기 위해서는 `str_replace_all()`을 사용.
    
    
    {% highlight r %}
    > fruits <- c("one apple", "two pears", "three bananas")
    > str_replace(fruits, "[aeiou]", "-")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "-ne apple"     "tw- pears"     "thr-e bananas"
    {% endhighlight %}
    
    
    
    {% highlight r %}
    > str_replace_all(fruits, "[aeiou]", "-")
    {% endhighlight %}
    
    
    
    {% highlight text %}
    #> [1] "-n- -ppl-"     "tw- p--rs"     "thr-- b-n-n-s"
    {% endhighlight %}
---
위에서 나열한 함수에서 _pattern_은 디폴트로  POSIX 정규표현식으로 취급됨. 이하의 함수를 통해 인수값을 변경할 수 있음. 

  * `stringr::fixed(string)`
    * 입력값 그대로의 문자로 매칭 시킴
  * `stringr::ignore.case(string)`
    * 대문자 소문자를 무시하여 매치 시킴
  * `stringr::perl(string)`
    * Perl 정규표현식으로 취급

### Formatting
  * `str_trim(string, side="both")`
    * 공백문자를 제거.
    
  * `str_pad(string, width, side="left", pad=" ")`
    * 폭을 width 만큼 늘려서 side를 기준으로 공백을 pad에 지정된 문자로 채워넣음.
    
  * `str_wrap(string, width=80, indent=0, exdent=0)`
    * 지정한 폭으로 줄바꿈. indent는 선두행의 왼쪽 여백, exdent는 그 이외 행의 왼쪽여백. 
    
    
결국은 정규표현식을 잘써야 한다는 이야기!!

참고자료 : [stringr: mordern, consistent string processing](http://journal.r-project.org/archive/2010-2/RJournal_2010-2_Wickham.pdf)
