---
title: '소득 상ㆍ하위층 삶의 만족도 격차 &#8211; 꼴찌에서 두 번째'
author: 양우성
layout: post
permalink: /2013/07/life-satisfaction-bli2003/
dsq_thread_id:
  - 1502055798
al2fb_facebook_exclude:
  - 
al2fb_facebook_exclude_video:
  - 
al2fb_facebook_link_id:
  - 127258130686247_500145316730858
al2fb_facebook_link_time:
  - 2013-07-15T23:02:34+00:00
al2fb_facebook_link_picture:
  - post=http://farm4.staticflickr.com/3774/9290807627_de41838291_z.jpg
categories:
  - R-Tips
  - 생활속의 통계
tags:
  - Better Life Index
  - data visualization
  - R-Tips
  - 삶의 만족도
comments: true
---
우리나라의 소득 상위 10%와 하위 10%의 삶의 만족도에 대한 격차가 OECD Better Life Index 2013 조사 대상 36개 국가 중 끝에서 두 번째인 35위네요. 

Better Life Index는 전반적인 삶의 질을 0~10점으로 평가한 일종의 웰빙지수입니다. 한국 언론에서는 행복지수라 소개하고 있더군요. Better Life Index에는 11가지 평가항목이 있는데요. 이번 포스팅에서는 평가항목 중 인생 전반적인 생활 및 환경에 관한 만족도를 평가한 삶의 만족도, 특히 소득에 따른 만족도 데이터를 이용해 우리나라의 위치와 격차의 정도를 다른 조사대상국과 비교해 보도록 하겠습니다.  
<!--more-->

## 데이터 설명

지난 5월 말 OECD에서 발표한 Better Life Index 2013의 [세부 데이터][1]에는 각 평가항목에 대해 소득 상위 10%와 하위 10%의 점수 및 남녀별 점수가 포함되어있습니다. 이중 삶의 만족도 항목(Life Satisfaction)을 사용하겠습니다. 

## 36개 조사대상국 삶의 만족도

조사대상 36개 국가의 전체 점수 및 소득수준 상위 10%, 하위 10%의 만족도를 그래프로 그려보면 다음과 같습니다. 

<a href="http://i1.wp.com/farm4.staticflickr.com/3774/9290807627_de41838291_b.jpg" title="BLI 2013 삶의 만족도" rel="lightbox"><img src="http://i0.wp.com/farm4.staticflickr.com/3774/9290807627_de41838291_z.jpg?resize=448%2C640" alt="BLI 2013 삶의 만족도" title="BLI 2013 삶의 만족도" class="aligncenter" data-recalc-dims="1" /></a>

삶의 만족도의 전체 점수를 보면 가장 점수가 높은 나라는 스위스, 스웨덴, 노르웨이 순으로 잘 알려진 복지국가들입니다. 반면 점수가 가장 낮은 나라는 헝가리, 포르투갈, 그리스 순이며 경제위기로 뉴스에 자주 나오는 나라들이네요. 우리나라는 일본과 같이 6점으로 OECD 평균 6.6점에 못 미치고 있습니다. 특히 소득수준에 따른 만족도의 격차가 눈에 띄게 크다는 것을 확인할 수 있습니다. 우리나라의 소득 수준 하위 10%의 삶에 대한 만족도는 OECD 평균 6.2점보다 한참 낮은 4.6점으로 조사 대상국 중 끝에서 네 번째입니다. 한편 소득수준 상위 10%의 만족도는 6.5점으로 역시 최하위권에 있기는 하지만 OECD 평균 7.1점과의 차이는 크게 줄어드는군요. 

## 소득 수준에 따른 격차

그럼 각국의 소득 수준에 따른 삶의 만족도의 격차에 순위를 정하면 어떻게 될까요?  
다음 그래프는 소득수준 상위 10%와 하위 10%의 삶에 대한 만족도 격차를 그려본 것입니다. 

<a href="http://i2.wp.com/farm3.staticflickr.com/2882/9290807537_920f7b43f3_c.jpg" title="Fig2" rel="lightbox"><img src="http://i2.wp.com/farm3.staticflickr.com/2882/9290807537_920f7b43f3.jpg?resize=500%2C375" alt="Fig2" title="삶의 만족도 격차" class="aligncenter" data-recalc-dims="1" /></a>

우리나라는 만족도의 격차가 1.9점으로 포르투갈의 2.4점 다음으로 조사대상국 중 두 번째로 격차가 큰 것을 알 수 있습니다. 세 번째로 큰 격차를 보이고 있는 나라는 그리스네요. 재밌게도 소득 수준 하위 10%의 만족도가 상위 10%의 만족도 보다 높은 나라가 있습니다. 아일랜드, 스웨덴, 뉴질랜드가 그렇군요. 그 밖에도 캐나다, 아이슬란드, 노르웨이, 호주가 소득 수준에 따른 만족도의 차이가 작은 나라이며 모두 전체적인 만족도의 점수가 상위에 랭크된 나라들임을 알 수 있습니다. 

## 맺음말

소득 수준에 따른 우리나라 삶의 만족도가 이렇게 큰 격차를 보이는 건 왜일까요?  
Better Life Index 2013에 따르면 우리나라는 상위 20%의 소득이 하위 20% 소득의 5.7배에 달해 OECD 회원국 가운데 소득불균형지수가 7번째로 높은 나라입니다. [기사][2]를 보면 상ㆍ하위 10%의 소득 차는 10.5배에 이른다고 하면서 최상ㆍ하위층 간 심각한 소득격차로 말미암은 상대적 박탈감이 큰 것으로 언급하고 있죠. 이 밖에도 일하는 시간은 길지만, 인생을 즐기는 시간은 짧아서 오는 상실감도 원인이 될 수 있겠네요. 

그런데 소득불균형의 심화는 자유경제를 도입한 국가에서 공통으로 나타나는 문제점입니다. 상ㆍ하위 20%의 소득불균형지수가 우리나라와 같은 호주는 왜 삶의 만족도 점수뿐만 아니라 종합 행복지수의 최상위권에 있을까요?  
그 밖에도 아일랜드의 상ㆍ하위 20% 소득불균형지수는 5.4배, 캐나다는 5.3배입니다. 모두 행복지수의 상위에 있는 나라네요. 한편 삶의 만족도 격차가 가장 큰 포르투갈도 5.6배, 세 번째인 그리스는 5.6배입니다. 이로 미루어 볼 때 단순히 소득격차에 따른 상대적 박탈감뿐만은 아닐 거라 추측할 수 있습니다. 저는 '부패', '유전무죄 무전유죄'라는 말들이 먼저 떠오르는군요. 소득격차에 따른 박탈감이 아니라 있는 자들의 행태를 보면서 오는 박탈감이 아닐까 생각해봅니다. [‘한국은 아시아 선진국 중 최악의 부패국’][3]이라는 국제 조사 결과가 아니 땐 굴뚝에서 연기 난건 아니겠죠. 

<div class="arconix-box arconix-box-green">
  <i class='fa fa-2x pull-left fa-download'></i><div class="arconix-box-content">
    이번 포스팅에 사용한 데이터는 <a href="http://stats.oecd.org/Index.aspx?DataSetCode=BLI">여기</a>서, R 코드는 <a href="https://gist.github.com/mitrad/bf5d139f9324a2a5e3e8">여기</a>서 받을 수 있습니다.
  </div>
</div>

 [1]: http://stats.oecd.org/Index.aspx?DataSetCode=BLI
 [2]: http://news.mk.co.kr/newsRead.php?no=414001&year=2013
 [3]: http://www.segye.com/Articles/News/Economy/Article.asp?aid=20130714022483&ctg1=01&ctg2=&subctg1=01&subctg2=&cid=0101030100000