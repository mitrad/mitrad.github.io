---
title: R의 출력결과를 LaTeX 테이블로 변환하기
author: 양우성
layout: post
permalink: /2012/03/r%ec%9d%98-%ec%b6%9c%eb%a0%a5%ea%b2%b0%ea%b3%bc%eb%a5%bc-latex-%ed%85%8c%ec%9d%b4%eb%b8%94%eb%a1%9c-%eb%b3%80%ed%99%98%ed%95%98%ea%b8%b0/
dsq_thread_id:
  - 614110932
tmac_last_id:
  - 274327333052223488
categories:
  - R-Tips
tags:
  - LaTeX
  - R-Tips
---
통계분석 패키지인 R과 과학문서 작성에 많이 쓰이는 LaTeX는 궁합이 아주 잘 맞습니다. 특히 R의 Sweave라는 패키지를 이용하면 R 환경에서 훌륭한 LaTeX 문서를 만들 수 있습니다. 단지 Sweave를 이용하면 R의 소스코드가 좀 복잡해지기는 한데요. 그래서 저는 R에서 계산한 결과만을 LaTeX 테이블로 변환하는 방법을 즐겨 사용합니다. 이를 가능하게 해주는 것이 R의 xtable이라는 패키지입니다.  
<!--more-->

  
우선 xtable 패키지를 인스톨합니다.

<div class="wp_codebox">
  <table>
    <tr id="p242157">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p2421code57">
        <pre class="rsplus" style="font-family:monospace;"><span style="color: #080;">&gt;</span> <span style="color: #0000FF; font-weight: bold;">install.<span style="">packages</span></span><span style="color: #080;">&#40;</span><span style="color: #ff0000;">"xtable"</span><span style="color: #080;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

인스톨한 패키지를 불러오고, 필요한 계산 혹은 분석을 하고 그 결과를 LaTeX 테이블로 변환하기 위해서는 함수 xtable()를 사용합니다.

<div class="wp_codebox">
  <table>
    <tr id="p242158">
      <td class="line_numbers">
        <pre>1
2
3
4
5
</pre>
      </td>
      
      <td class="code" id="p2421code58">
        <pre class="rsplus" style="font-family:monospace;"><span style="color: #080;">&gt;</span> <a href="http://astrostatistics.psu.edu/su07/R/html/graphics/html/library.html"><span style="color: #0000FF; font-weight: bold;">library</span></a><span style="color: #080;">&#40;</span>xtable<span style="color: #080;">&#41;</span>
<span style="color: #080;">&gt;</span> <span style="color: #0000FF; font-weight: bold;">data</span><span style="color: #080;">&#40;</span>tli<span style="color: #080;">&#41;</span>
<span style="color: #080;">&gt;</span> fm1 <span style="color: #080;">&lt;</span> <span style="color: #080;">-</span> <span style="color: #0000FF; font-weight: bold;">aov</span><span style="color: #080;">&#40;</span>tlimth ~ sex <span style="color: #080;">+</span> ethnicty <span style="color: #080;">+</span> grade <span style="color: #080;">+</span> disadvg, <span style="color: #0000FF; font-weight: bold;">data</span><span style="color: #080;">=</span>tli<span style="color: #080;">&#41;</span>
<span style="color: #080;">&gt;</span> fm1.<span style="">table</span> <span style="color: #080;">&lt;</span> <span style="color: #080;">-</span> xtable<span style="color: #080;">&#40;</span>fm1<span style="color: #080;">&#41;</span>
<span style="color: #080;">&gt;</span> <a href="http://astrostatistics.psu.edu/su07/R/html/graphics/html/print.html"><span style="color: #0000FF; font-weight: bold;">print</span></a><span style="color: #080;">&#40;</span>fm1.<span style="">table</span><span style="color: #080;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

화면에 출력된 결과를 LaTeX 문서에 복사 &#038; 붙여 넣기를 하고 컴파일을 하면, 아래와 같은 테이블을 얻을 수 있습니다.

[<img src="http://i1.wp.com/wsyang.com/wp-content/uploads/2012/03/table.png?resize=383%2C109" alt="" title="table" class="aligncenter size-full wp-image-2423" data-recalc-dims="1" />][1]

테이블의 결과를 화면이 아닌 별도의 파일로 저장하기 위해서는

<div class="wp_codebox">
  <table>
    <tr id="p242159">
      <td class="line_numbers">
        <pre>1
</pre>
      </td>
      
      <td class="code" id="p2421code59">
        <pre class="rsplus" style="font-family:monospace;"><a href="http://astrostatistics.psu.edu/su07/R/html/graphics/html/print.html"><span style="color: #0000FF; font-weight: bold;">print</span></a><span style="color: #080;">&#40;</span>fm1.<span style="">table</span>, <a href="http://astrostatistics.psu.edu/su07/R/html/graphics/html/file.html"><span style="color: #0000FF; font-weight: bold;">file</span></a><span style="color: #080;">=</span><span style="color: #ff0000;">"fm1.txt"</span><span style="color: #080;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

더 많은 예제는 [The xtable gallery(PDF 파일)][2]을 확인하세요.

 [1]: http://i1.wp.com/wsyang.com/wp-content/uploads/2012/03/table.png
 [2]: http://cran.r-project.org/web/packages/xtable/vignettes/xtableGallery.pdf