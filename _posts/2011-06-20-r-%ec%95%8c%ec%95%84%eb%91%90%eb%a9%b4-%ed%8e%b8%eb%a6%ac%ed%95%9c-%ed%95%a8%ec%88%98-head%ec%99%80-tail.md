---
title: '[R] 알아두면 편리한 함수 head와 tail'
author: 양우성
layout: post
permalink: /2011/06/r-%ec%95%8c%ec%95%84%eb%91%90%eb%a9%b4-%ed%8e%b8%eb%a6%ac%ed%95%9c-%ed%95%a8%ec%88%98-head%ec%99%80-tail/
dsq_thread_id:
  - 337091326
short_url:
  - http://bit.ly/
tmac_last_id:
  - 274327333052223488
categories:
  - R-Tips
tags:
  - R 함수
  - R-Tips
---
R의 사용자 환경(UI)은 그다지 좋은 편이 못됩니다. R에서 데이터 파일(txt, csv 등)을 불러오면 데이터프레임 형식으로 작업공간에 저장됩니다. 데이터가 제대로 읽혔는지 확인하는 방법은 저장된 데이터프레임의 이름을 콘솔에 입력하면 됩니다만 데이터의 크기가 크면 한 화면에 다 보이지 않을뿐더러, 일정 수가 넘어가게 되면 아예 보여 주지도 않습니다. 또한, 계산 결과가 매우 많을 때도 같은 상황이 발생하게 됩니다.  
<!--more-->

  
예를 들어 SNP를 이용한 연관분석(association study)을 하게 되면 검정 통계량, 유의확률, SNP 빈도 등이 포함된 결과 파일을 가지고 작업을 하는데, 적게는 수천, 많게는 수십만 개의 결과를 확인해야 합니다. 그런데 정작 알고 싶은 건 유의확률이 아주 작은 몇 개 혹은 몇십 개의 결과일 때가 많습니다.  
이럴 때 편리하게 쓸 수 있는 함수로 head와 tail이 있습니다.

유명한 붓꽃 데이터를 예로 들어 보면

<div class="wp_codebox">
  <table>
    <tr id="p235349">
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
9
10
11
12
13
</pre>
      </td>
      
      <td class="code" id="p2353code49">
        <pre class="text" style="font-family:monospace;">&gt; iris
    Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
1            5.1         3.5          1.4         0.2     setosa
2            4.9         3.0          1.4         0.2     setosa
3            4.7         3.2          1.3         0.2     setosa
4            4.6         3.1          1.5         0.2     setosa
5            5.0         3.6          1.4         0.2     setosa
...
146          6.7         3.0          5.2         2.3  virginica
147          6.3         2.5          5.0         1.9  virginica
148          6.5         3.0          5.2         2.0  virginica
149          6.2         3.4          5.4         2.3  virginica
150          5.9         3.0          5.1         1.8  virginica</pre>
      </td>
    </tr>
  </table>
</div>

위와 같이 150개의 관측값이 들어 있는 데이터의 앞부분을 확인하고 싶을 때 함수 head를 이용하면 디폴트로 처음 6개의 관측값을 출력합니다.

<div class="wp_codebox">
  <table>
    <tr id="p235350">
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
</pre>
      </td>
      
      <td class="code" id="p2353code50">
        <pre class="text" style="font-family:monospace;">&gt; head(iris)
  Sepal.Length Sepal.Width Petal.Length Petal.Width Species
1          5.1         3.5          1.4         0.2  setosa
2          4.9         3.0          1.4         0.2  setosa
3          4.7         3.2          1.3         0.2  setosa
4          4.6         3.1          1.5         0.2  setosa
5          5.0         3.6          1.4         0.2  setosa
6          5.4         3.9          1.7         0.4  setosa</pre>
      </td>
    </tr>
  </table>
</div>

더 많은 관측값을 출력하고 싶으면 head(iris, n=10)와 같이 &#8220;n= 출력하고 싶은 수&#8221;를 지정하면 됩니다.

<div class="wp_codebox">
  <table>
    <tr id="p235351">
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
9
10
11
12
</pre>
      </td>
      
      <td class="code" id="p2353code51">
        <pre class="text" style="font-family:monospace;">&gt; head(iris, n=10)
   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
1           5.1         3.5          1.4         0.2  setosa
2           4.9         3.0          1.4         0.2  setosa
3           4.7         3.2          1.3         0.2  setosa
4           4.6         3.1          1.5         0.2  setosa
5           5.0         3.6          1.4         0.2  setosa
6           5.4         3.9          1.7         0.4  setosa
7           4.6         3.4          1.4         0.3  setosa
8           5.0         3.4          1.5         0.2  setosa
9           4.4         2.9          1.4         0.2  setosa
10          4.9         3.1          1.5         0.1  setosa</pre>
      </td>
    </tr>
  </table>
</div>

마지막 6개의 관측값을 출력하고 싶을 때에는 tail 함수를 이용하여

<div class="wp_codebox">
  <table>
    <tr id="p235352">
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
</pre>
      </td>
      
      <td class="code" id="p2353code52">
        <pre class="text" style="font-family:monospace;">&gt; tail(iris)
    Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
145          6.7         3.3          5.7         2.5 virginica
146          6.7         3.0          5.2         2.3 virginica
147          6.3         2.5          5.0         1.9 virginica
148          6.5         3.0          5.2         2.0 virginica
149          6.2         3.4          5.4         2.3 virginica
150          5.9         3.0          5.1         1.8 virginica</pre>
      </td>
    </tr>
  </table>
</div>

와 같이 사용합니다. head와 마찬가지로 &#8220;n=출력하고 싶은 수&#8221;를 지정하면 더 많은 값을 출력할 수 있습니다.  
물론

<div class="wp_codebox">
  <table>
    <tr id="p235353">
      <td class="line_numbers">
        <pre>1
2
</pre>
      </td>
      
      <td class="code" id="p2353code53">
        <pre class="rsplus" style="font-family:monospace;"><span style="color: #080;">&gt;</span> <a href="http://astrostatistics.psu.edu/su07/R/html/stats/html/Normal.html"><span style="color: #CC9900; font-weight: bold;">iris</span></a><span style="color: #080;">&#91;</span>,<span style="color: #ff0000;">1</span><span style="color: #080;">:</span><span style="color: #ff0000;">6</span><span style="color: #080;">&#93;</span>
<span style="color: #080;">&gt;</span> <a href="http://astrostatistics.psu.edu/su07/R/html/stats/html/Normal.html"><span style="color: #CC9900; font-weight: bold;">iris</span></a><span style="color: #080;">&#91;</span>,<span style="color: #ff0000;">145</span><span style="color: #080;">:</span><span style="color: #ff0000;">150</span><span style="color: #080;">&#93;</span></pre>
      </td>
    </tr>
  </table>
</div>

와 같이 인덱스를 사용하는 방법도 있지만, 관측값의 수가 많을 수록 실제로 사용해보면 head, tail 함수를 이용하는 것이 훨씬 편하더군요.