---
title: R에서 SAS의 영구파일 sas7bdat 이용하기
author: 양우성
layout: post
permalink: /2011/07/r%ec%97%90%ec%84%9c-sas%ec%9d%98-%ec%98%81%ea%b5%ac%ed%8c%8c%ec%9d%bc-sas7bdat-%ec%9d%b4%ec%9a%a9%ed%95%98%ea%b8%b0/
dsq_thread_id:
  - 369651193
short_url:
  - http://bit.ly/
tmac_last_id:
  - 274327333052223488
categories:
  - R-Tips
  - SAS
tags:
  - R package
  - R-Tips
  - SAS
  - sas7bdat
---
최근 R package가 통계 분석에 많이 사용된다고는 하지만, 기업에서는 SAS나 SPSS를 더 많이 사용하는 것으로 알고 있습니다. 저도 대학이나 연구기관의 의뢰에는 R를 사용하지만, 기업의 데이터 분석에는 SAS를 이용합니다.

간혹 클라이언트로부터 받은 데이터가 SAS의 영구 파일형식인 sas7bdat일 때가 있습니다. 분석할 때 아무래도 손에 익은 R을 선호하게 되는데 SAS를 사용할 수 있는 환경에 있으면 데이터를 일반 ASCII 파일로 변환하여 사용하면 되지만 SAS를 사용할 수 없는 환경에 있을 때도 있습니다.

물론 R에서 SAS 형식의 데이터를 불러오는 함수 read.ssd()가 있긴 하지만, 이도 시스템에 SAS가 설치되어 있어야만 이용할 수 있어서 이래저래 불편했었습니다. 그런데 최근 sas7bdat라는 패키지가 공개되어 간단하게 이 형식의 데이터를 R에 불러올 수 있게 되었습니다.  
<!--more-->

  
먼저 sas7bdat 패키지를 R에 인스톨합니다.

<div class="wp_codebox">
  <table>
    <tr id="p236854">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p2368code54">
        <pre class="rsplus" style="font-family:monospace;"><span style="color: #080;">&gt;</span> <span style="color: #0000FF; font-weight: bold;">install.<span style="">packages</span></span><span style="color: #080;">&#40;</span><span style="color: #ff0000;">"sas7bdat"</span><span style="color: #080;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

예를 들어 SAS에서 제공하는 예제 데이터 &#8220;cars.sas7bdat&#8221;를 R로 불러 오기 위해서는

<div class="wp_codebox">
  <table>
    <tr id="p236855">
      <td class="line_numbers">
        <pre>1
2
</pre>
      </td>
      
      <td class="code" id="p2368code55">
        <pre class="rsplus" style="font-family:monospace;"><span style="color: #080;">&gt;</span> <a href="http://astrostatistics.psu.edu/su07/R/html/graphics/html/library.html"><span style="color: #0000FF; font-weight: bold;">library</span></a><span style="color: #080;">&#40;</span>sas7bdat<span style="color: #080;">&#41;</span>
<span style="color: #080;">&gt;</span> <a href="http://astrostatistics.psu.edu/su07/R/html/stats/html/Normal.html"><span style="color: #CC9900; font-weight: bold;">cars</span></a> <span style="color: #080;">&lt;</span> <span style="color: #080;">-</span> read.<span style="">sas7bdat</span><span style="color: #080;">&#40;</span><span style="color: #ff0000;">"ftp://ftp.sas.com/edu/hec/cars.sas7bdat"</span><span style="color: #080;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

와 같이 함수 read.sas7bdat()를 이용하면 R의 데이터프레임 형식으로 변환시킬 수 있습니다.

그 후엔 원하는 분석을 진행하면 되겠지요. </pre> <div class="wp_codebox">
  <table>
    <tr id="p236856">
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
14
15
16
17
18
19
20
</pre>
      </td>
      
      <td class="code" id="p2368code56">
        <pre class="rsplus" style="font-family:monospace;"><span style="color: #080;">&gt;</span> <a href="http://astrostatistics.psu.edu/su07/R/html/graphics/html/summary.html"><span style="color: #0000FF; font-weight: bold;">summary</span></a><span style="color: #080;">&#40;</span><a href="http://astrostatistics.psu.edu/su07/R/html/stats/html/Normal.html"><span style="color: #CC9900; font-weight: bold;">cars</span></a><span style="color: #080;">&#41;</span>
            Model      Country        Type        Weight     TurningRadius
 Acura Integra  <span style="color: #080;">:</span>  <span style="color: #ff0000;">1</span>   Japan<span style="color: #080;">:</span><span style="color: #ff0000;">30</span>   Compact<span style="color: #080;">:</span><span style="color: #ff0000;">22</span>   Min.   <span style="color: #080;">:</span><span style="color: #ff0000;">1695</span>   Min.   <span style="color: #080;">:</span><span style="color: #ff0000;">32.00</span>
 Acura Legend V6<span style="color: #080;">:</span>  <span style="color: #ff0000;">1</span>   Other<span style="color: #080;">:</span><span style="color: #ff0000;">37</span>   Large  <span style="color: #080;">:</span><span style="color: #ff0000;">17</span>   1st Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">2624</span>   1st Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">36.00</span>
 Audi <span style="color: #ff0000;">100</span>       <span style="color: #080;">:</span>  <span style="color: #ff0000;">1</span>   USA  <span style="color: #080;">:</span><span style="color: #ff0000;">49</span>   Medium <span style="color: #080;">:</span><span style="color: #ff0000;">30</span>   Median <span style="color: #080;">:</span><span style="color: #ff0000;">2920</span>   Median <span style="color: #080;">:</span><span style="color: #ff0000;">39.00</span>
 Audi <span style="color: #ff0000;">80</span>        <span style="color: #080;">:</span>  <span style="color: #ff0000;">1</span>              Small  <span style="color: #080;">:</span><span style="color: #ff0000;">22</span>   Mean   <span style="color: #080;">:</span><span style="color: #ff0000;">2958</span>   Mean   <span style="color: #080;">:</span><span style="color: #ff0000;">38.59</span>
 Audi <span style="color: #ff0000;">90</span>        <span style="color: #080;">:</span>  <span style="color: #ff0000;">1</span>              Sporty <span style="color: #080;">:</span><span style="color: #ff0000;">25</span>   3rd Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">3331</span>   3rd Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">41.00</span>
 BMW 325i       <span style="color: #080;">:</span>  <span style="color: #ff0000;">1</span>                           Max.   <span style="color: #080;">:</span><span style="color: #ff0000;">4285</span>   Max.   <span style="color: #080;">:</span><span style="color: #ff0000;">47.00</span>
 <span style="color: #080;">&#40;</span>Other<span style="color: #080;">&#41;</span>        <span style="color: #080;">:</span><span style="color: #ff0000;">110</span>
  Displacement     Horsepower       GasTank
 Min.   <span style="color: #080;">:</span> <span style="color: #ff0000;">61.0</span>   Min.   <span style="color: #080;">:</span> <span style="color: #ff0000;">55.0</span>   Min.   <span style="color: #080;">:</span> <span style="color: #ff0000;">9.20</span>
 1st Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">115.5</span>   1st Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">100.0</span>   1st Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">14.15</span>
 Median <span style="color: #080;">:</span><span style="color: #ff0000;">143.0</span>   Median <span style="color: #080;">:</span><span style="color: #ff0000;">129.0</span>   Median <span style="color: #080;">:</span><span style="color: #ff0000;">15.90</span>
 Mean   <span style="color: #080;">:</span><span style="color: #ff0000;">158.3</span>   Mean   <span style="color: #080;">:</span><span style="color: #ff0000;">130.2</span>   Mean   <span style="color: #080;">:</span><span style="color: #ff0000;">16.24</span>
 3rd Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">181.0</span>   3rd Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">150.0</span>   3rd Qu.<span style="color: #080;">:</span><span style="color: #ff0000;">18.00</span>
 Max.   <span style="color: #080;">:</span><span style="color: #ff0000;">350.0</span>   Max.   <span style="color: #080;">:</span><span style="color: #ff0000;">278.0</span>   Max.   <span style="color: #080;">:</span><span style="color: #ff0000;">27.00</span>
&nbsp;
<span style="color: #080;">&gt;</span> <a href="http://astrostatistics.psu.edu/su07/R/html/graphics/html/with.html"><span style="color: #0000FF; font-weight: bold;">with</span></a><span style="color: #080;">&#40;</span><a href="http://astrostatistics.psu.edu/su07/R/html/stats/html/Normal.html"><span style="color: #CC9900; font-weight: bold;">cars</span></a>, <a href="http://astrostatistics.psu.edu/su07/R/html/graphics/html/summary.html"><span style="color: #0000FF; font-weight: bold;">summary</span></a><span style="color: #080;">&#40;</span>Weight<span style="color: #080;">&#41;</span><span style="color: #080;">&#41;</span>
   Min. 1st Qu.  <span style="">Median</span>    Mean 3rd Qu.    <span style="">Max</span>.
   <span style="color: #ff0000;">1695</span>    <span style="color: #ff0000;">2624</span>    <span style="color: #ff0000;">2920</span>    <span style="color: #ff0000;">2958</span>    <span style="color: #ff0000;">3331</span>    <span style="color: #ff0000;">4285</span></pre>
      </td>
    </tr>
  </table>
</div>

아직 대용량 데이터를 대상으로 써보지는 않았지만, SAS가 없어도 직접 sas7bdat 형식의 파일을 R에서 이용할 수 있다는 점에서 유용하게 사용할 수 있을듣 합니다.