---
title: 한국 일본 항공노선 데이터의 시각화
author: 양우성
layout: post
permalink: /2013/06/visualizing-of-airplane-paths/
dsq_thread_id:
  - 1413941084
categories:
  - R-Tips
  - 생활속의 통계
tags:
  - data visualization
  - ggmap
  - OpenFlights
  - R
  - 데이터 시각화
---
요즘 비행기로 여행 많이 하시죠? 만일 여러분이 비행기를 이용한 기록을 정리하고 지도에 표시해주는 서비스가 있다면 어떨까요? 

[openflights.org][1] 라는 프로젝트가 이러한 서비스를 제공하고 있습니다. OpenFlights는 개인의 항공 로그를 정리하여 여러 통계치를 보여주고 남들과 공유할 수 있는 프로젝트로 비행기로 여행을 자주 하시는 분들은 재미있게 이용할 수 있는 프로젝트의 하나입니다. 

또한, 이 사이트에서는 전 세계 공항, 항공노선, 항공사에 대한 정보를 공개하고 있는데 지도를 이용한 데이터 시각화의 예제로 즐겨 사용되기도 합니다. 그래서 저도 이 데이터를 가지고 한국-한국, 일본-일본, 한국-일본 노선만을 추출하여 R 언어를 이용해 시각화해보겠습니다. 

<!--more-->

먼저, 지도를 이용한 시각화이니 지도 데이터가 필요하겠죠? 여기서는 [ggmap][2] 패키지를 이용하겠습니다. `ggmap` 패키지는 Google Maps, OpenStreetMaps, Stamen Maps, CloudMade Maps 등에서 제공하는 지도를 R에서 이용할 수 있도록 해주는 패키지로 `ggplot2` 패키지를 기반으로 작동합니다. 얼마 전 [위치 정보가 포함된 트윗을 지도상에 표시한 그림][3]이 화제가 된 적이 있는데 그 도시 시각화에 사용된 패키지이기도 합니다. [20줄 정도의 R 코드][4]라고 하는군요. 

OpenFlights 프로젝트에서 제공하는 공항과 항공노선 데이터에서 한국, 일본에 해당하는 부분만 추출하여 지도 위에 표시하면 다음과 같습니다. 

<img src="http://i2.wp.com/farm3.staticflickr.com/2845/9085732328_24f7b85ee0_o.png?w=550" alt="한일 항공노선" data-recalc-dims="1" />&ldquo; 

위 그림은 선의 색이 진할수록 다수의 항공사가 같은 노선에 취항하고 있다는 것을 의미하고, 회색 원의 크기가 클수록 착륙하는 항공편이 많다는 것을 의미합니다.  
생각보다는 많은 노선이 있네요. 위 지도는 구글맵을 이용했습니다. 아직(?) 구글맵은 동해를 일본해(Sea of Japan)으로 표기하고 있어서 제가 임의로 그림에 동해(East Sea)를 집어넣었습니다. <img src="http://i0.wp.com/wsyang.com/wp-includes/images/smilies/icon_smile.gif?w=550" alt=":)" class="wp-smiley" data-recalc-dims="1" />

그럼, 한일 양국에 한정 지어 보았을 때 가장 많은 항공사가 취항한 노선은 무엇일까요?

| 노선              | 도시                  | 항공사 수 |
| --------------- | ------------------- | ----- |
| CJU &#8211; GMP | 제주 &#8211; 서울(김포)   | 7     |
| FUK &#8211; ICN | 후쿠오카 &#8211; 서울(인천) | 7     |
| ICN &#8211; KIX | 서울(인천) &#8211; 오사카  | 7     |
| ICN &#8211; NRT | 서울(인천) &#8211; 나리타  | 7     |
| ICN &#8211; NGO | 서울(인천) &#8211; 나고야  | 7     |

김포-제주 노선에 이렇게 많은 항공사가 취항하고 있는지 몰랐네요. 그 외에는 서울(인천)과 일본의 주요 도시를 연결하는 노선에 많은 항공사가 취항하고 있는 것을 알 수 있습니다. 

다음으로 한일 양국의 국내, 국제선에 한정했을 때 가장 많은 항공편의 도착공항을 보면 일본의 하네다(HND, 도쿄), 인천(ICN, 서울), 신치토세(CTS, 삿포로), 후쿠오카(FUK), 칸사이(KIX, 오사카) 공항 순임을 알 수 있습니다. 

[<img src="http://i2.wp.com/farm8.staticflickr.com/7351/9090870270_3e53f16231_o.png?resize=550%2C321" alt="Fig2" data-recalc-dims="1" />][5]

하지만 실제로 공항에는 세계 각국을 오가는 비행기들이 이착륙하기 때문에 전 세계 모든 항공편 중 한일 양국의 공항에 착륙하는 수를 구해보면 우리나라의 인천 국제공항(ICN)에 가장 많은 노선이 취항하고 있음을 알 수 있었습니다. 그다음은 일본의 나리타(NRT, 도쿄), 칸사이(KIX, 오사카), 하네다(HND, 도쿄), 후쿠오카(FUK) 공항 순이네요.

[<img src="http://i0.wp.com/farm8.staticflickr.com/7432/9090870244_5d6cdfb574_o.png?resize=550%2C321" alt="Fig3" data-recalc-dims="1" />][6]

이 밖에도 국제항공운송협회(IATA)의 공항식별 코드가 붙어있는 공항의 수는 한국 22개, 일본 88개이며, 그중 OpenFlights에 노선정보가 없는 공항 수는 한국 8개, 일본 24개입니다. 노선정보가 등록되지 않은 공항들은 어떻게 유지 관리가 되고있는지 궁금하군요. 전부 세금 들여서 만들었을 텐데 말이죠. 

처음에는 시각화의 연습 삼아 이 데이터를 들여다보기 시작했는데 이것저것 알아보니 재밌네요. 여기에 실시간 운항현황을 덧붙이면 [flightraddar24][7]나 [Plane Finder][8]와 같은 서비스가 되겠고 데이터가 쌓이면 빅데이터가 되고 그걸 분석해서 연착이 잦은 노선이나 노선 운항횟수의 증감 등을 파악해서 항공사 운영 혹은 공항운영에 반영한다면 실생활에 영향을 미치게 되겠죠(말로는 간단합니다만&hellip; :)).

마지막으로 이번 포스팅에 사용한 R 코드는 다음과 같습니다. 

<noscript>
  <p>
    View the code on <a href="https://gist.github.com/5820018">Gist</a>.
  </p>
</noscript>

 [1]: http://openflights.org/
 [2]: http://cran.r-project.org/web/packages/ggmap/index.html
 [3]: https://blog.twitter.com/2013/geography-tweets-3
 [4]: https://twitter.com/miguelrios/status/340506256534024193
 [5]: http://www.flickr.com/photos/woosung/9090870270/ "Fig2 by Woosung Yang, on Flickr"
 [6]: http://www.flickr.com/photos/woosung/9090870244/ "Fig3 by Woosung Yang, on Flickr"
 [7]: http://www.flightradar24.com/
 [8]: http://planefinder.net/