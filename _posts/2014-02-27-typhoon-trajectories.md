---
layout: post
title: "태풍이동경로의 시각화"
date: 2014-2-28
categories: R
tags: [R, dplyr package]
image: typhoon.png
---



Gaston Sanchez씨의 [Visualizing Hurricane Trajectories](http://rpubs.com/gaston/hurricanes)를 읽고 **dplyr** 패키지 연습도 겸해서 따라 해보기. 


### 데이터 
National Climatic Data Center에서 제공하는 [International Best Track Archive for Climate Stewardship(IBTrACS)](http://www.ncdc.noaa.gov/oa/ibtracs/index.php?name=wmo-data)을 이용했다. 다양한 포맷의 데이터를 제공하는데 여기서는 csv 파일을 이용.  

<!--more-->


우리나라 및 일본의 최근 데이터를 찾아보았지만, 일본 기상청은 개별 태풍에 대해 [pdf 파일](http://www.data.jma.go.jp/fcd/yoho/typhoon/position_table/index.html)만 제공하고 있고 우리나라도 [국가 태풍센터](http://typ.kma.go.kr/TYPHOON/statistics/statistics_02_4.jsp)에서 진로정보를 공개하고 있지만, 파일이 아닌 웹사이트의 정보를 크롤링해야 하는 삽질이 필요해서 포기. 

### R을 이용한 시각화

{% highlight r %}
> # 패키지 불러오기
> library(ggmap)
> library(ggplot2)
> library(dplyr)
{% endhighlight %}


우리나라에 영향을 미치는 태풍의 경로를 알아보기 위해 IBTrACS에서 Western North Pacific(WP) 지역 데이터 불러온다. 불러온 데이터의 형식이 모두 문자형이기 때문에 연도, 위도, 경도를 숫자형으로 변환. 풍속단위를 노트(knot)에서 초속(m/s)으로 변환.


{% highlight r %}
> # IBTrACS에서 Western North Pacfic(WP) 지역 데이터 불러오기 
> noaa <- "ftp://eclipse.ncdc.noaa.gov/pub/ibtracs/v03r05/wmo/csv/basin/"
> WP.basin = read.csv(paste(noaa, "Basin.WP.ibtracs_wmo.v03r05.csv", sep = ""), 
+                     skip = 1, stringsAsFactors = FALSE)
> # 변수 정보 제거
> WP.basin <- WP.basin[-1,]
> 
> # dplyr 패키지를 이용하기 위해 tbl_df로 변환
> WP.basin.df <- tbl_df(WP.basin)
> 
> # Season, Latitude, Logitude : 문자 -> 숫자
> # Wind.WMO. : 문자 -> 숫자 & 노트 -> 초속(m/s)
> # ISO_time에서 월 정보 추출
> WP.df <- mutate(WP.basin.df, 
+        Season = as.numeric(Season),
+        Latitude = as.numeric(gsub("^ ", "", Latitude)),
+        Longitude = as.numeric(gsub("^ ", "", Longitude)),
+        Wind.WMO. = as.numeric(gsub("^ ", "", Wind.WMO.)) * 0.5144,
+        ISO_time = as.POSIXct(ISO_time),
+        Month = factor(substr(ISO_time, 6,7), labels = c(month.name))
+ )
{% endhighlight %}


데이터에 수록된 가장 최근 기록이 2010년이기 때문에, 1999~2010년 태풍 정보만 추출하고 이름이 없는 태풍도 삭제.

지도에 각 태풍의 경로를 그리기 위해 태풍 ID 생성.

{% highlight r %}
> substorms <- WP.df %.% 
+   filter(Season %in% 1999:2010 & !(Name == "NOT NAMED")) %.%
+   mutate(ID = as.factor(paste(Name, Season, sep = ".")))
{% endhighlight %}


**maps** 패키지를 이용하면 참고했던 사이트의 그림과 비슷한 그림을 그릴 수 있겠지만, 지도의 윗부분이 이상하게 나와서 **ggmap** 패키지를 이용하기로 했다. 구글맵에서 위도 40도, 경도 145도를 중심으로 한 지도를 가져온 후, 그 위에 각 태풍의 경로를 시각화. 


{% highlight r %}
> map <- ggmap(get_googlemap(center = c(lon=145, lat=40), 
+                            zoom=3, maptype="terrain", 
+                            ,color="bw", scale=2), 
+              extent="device")
> 
> # 태풍이동경로 1999 - 2010
> map + geom_path(data = substorms, 
+             aes(x = Longitude, y = Latitude,group = ID, colour = Wind.WMO.), 
+             alpha = 0.5, size = 0.8) + 
+   labs(x = "", y = "", colour = "Wind \n(m/sec)",
+        title = "Typhoon Trajectories 1999 - 2010") +
+   theme(panel.background = element_rect(fill = "gray10", colour = "gray30"), 
+        axis.text.x = element_blank(), 
+        axis.text.y = element_blank(), axis.ticks = element_blank(), 
+        panel.grid.major = element_blank(), panel.grid.minor = element_blank())
{% endhighlight %}

![center](/figs/2014-02-27-typhoon-trajectories/figure1.png) 



함수 `ggplot2::facet_warp`를 사용하여 연도별, 월별 태풍 이동 경로를 시각화


{% highlight r %}
> map + 
+   geom_path(
+     data = substorms, 
+     aes(x = Longitude, y = Latitude,group = ID, colour = Wind.WMO.), 
+     alpha = 0.5, size = 0.8
+   ) + 
+   labs(
+     x = "", y = "", colour = "Wind \n(m/sec)",
+        title = "Typoon Trajectories by Year (1999 - 2010)"
+   ) + 
+   facet_wrap(~Season) +  
+   # facet_grid(~Month) + 
+   theme(
+     panel.background = element_rect(fill = "gray10", colour = "gray30"), 
+     axis.text.x = element_blank(), 
+     axis.text.y = element_blank(), 
+     axis.ticks = element_blank(), 
+     panel.grid.major = element_blank(), 
+     panel.grid.minor = element_blank()
+   ) 
{% endhighlight %}

![text-center](/figs/2014-02-27-typhoon-trajectories/figure2.png) 


![center](/figs/2014-02-27-typhoon-trajectories/figure3.png) 


그나저나 구글맵 쓸때마다 저 Sea of Japan이 참 거슬리네...

> 구글아 구글아 

> 동해를 내놓아라

> 내어놓지 않으면

> 구워서 먹으리.

