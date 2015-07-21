---
title: 기업 환경에서의 R
layout: post
permalink: /2013/08/r-in-the-enterprise/
dsq_thread_id:
  - 1581522229
categories:
  - R-Tips
tags:
  - Netezza
  - Oracle R Enterprise
  - R-Tips
  - Revolution R
  - SAP HANA
  - SAS
  - SPSS
  - Sybase IQ
comments: true
---
## 무료 데이터 분석 환경 R

R은 무료 데이터 분석 소프트웨어이지만 고급 통계분석환경을 이용할 수 있습니다. 조작성에서도 GUI 환경을 지원하는 [R Commander][1], 통합개발환경 [R Studio][2]등 무료로 이용할 수 있는 보조 소프트웨어가 다수 등장하여 점점 더 손쉽게 사용할 수 있는 환경이 조성되고 있습니다. 따라서 통계분석을 업무에 도입하는 경우에도 R은 충분한 기능을 제공하고 있다 말할 수 있습니다.  
　  
그러나 표준 R 환경만으로 모든 기업의 요구를 충족시킬 수 있는가에 대한 물음에는 아니오라고 답할수 밖에 없습니다. 제품 보수 지원, Q&A 대응, 한글 설명서, 교육과 같은 서비스가 필요한 경우 SAS, SPSS 등 상용제품을 활용하는 것이 인적 자원, 교육 비용, 시간 비용등을 고려할 때 바람직한 경우도 있을 것입니다. 

### 표준 R 환경의 한계

표준 R 환경은 기본적으로 &ldquo;데이터를 메모리 공간에 불러와 처리"하는 형식입니다. 이 때문에 실행 컴퓨터의 이용 가능한 메모리 용량보다 큰 데이터에 대해서는 1) 데이터를 작은 단위로 나누어 반복처리를 이용한 분할 계산을 하거나, 2) 다른 데이터를 메모리 공간에서 일시적으로 삭제하거나, 3) 메모리를 물리적으로 증설하는 등의 조처를 할 수 밖에 없습니다. 이 문제는 특히 빅 데이터 처리에 R을 활용하려 할 때 골치거리가 될 가능성이 높습니다. 

### 대용량 데이터의 고속처리

데이터 분석 속도는 실행 컴퓨터에 탑재된 CPU의 성능과 메모리량에 의존합니다. 통계분석을 할 때 고성능 분석환경을 준비하는 것이 데이터가 가진 가치의 활용 면에서 우위에 있다는 것은 틀림없는 사실입니다. 분석하는 데이터의 양이 늘어난다고 해서 더 정밀한 예측모델을 만들 수 있다고는 일률적으로 말할 수 없지만, 분석을 반복하여 시행착오를 가능하게 함으로서 빅 데이터가 가진 가치를 보다 빨리 발견할 수 있습니다.  
이러한 배경을 바탕으로 동시에 여러 대의 컴퓨터를 분석에 이용하는 병렬분산처리를 가능케 해주는 R 환경의 패키지도 다수 공개되어 있습니다.  
　 

## 제품업체의 참여

이미 테라바이트, 페타바이트 등 대용량 데이터 분석을 수행해오던 기존 데이터 웨어하우스 업계에서도 병렬분산처리를 적용해 처리 속도를 대폭 향상한 제품을 내놓고 있습니다. 또한, 데이터 분석 소프트웨어로써 R의 유용성이 널리 인정되자 제품업체 특히 데이터베이스를 주력 상품으로 하는 업체를 중심으로 R과의 연계 더 나아가 통합 환경을 제공하는 일에 앞을 다투고 있습니다. 

이번 포스팅에서는 대표적인 업체들의 대응 방법을 간략히 정리해 보려 합니다. 

### Revolution Analytics

[Revolution Analytics][3]사의 Revolution R은 표준 R 환경에 기능확장을 추가하여 병렬분산처리를 가능케 하는 소프트웨어 제품을 판매하고 있습니다. 따라서 현재까지는 표준 R 환경에 가장 가까운 형태의 소프트웨어라 할 수 있을 것 같습니다.  
앞서 언급한 바와 같이 R은 CRAN의 패키지 등을 이용해 병렬분산처리환경을 구축할 수 있지만, Revolution R을 이용함으로써 그러한 환경을 간단하게 구축할 수 있습니다. 또한, 구매 후 제품 지원, [Webinar][4]를 통한 교육등도 이용할 수 있습니다.  
교육기관 및 학생은 [Revolution R Enterprise Academic version][5]을 무상으로 이용할 수 있습니다. 지원하는 OS는 MS Windows 32/64 bit 및 Red Hat Enterprise Linux입니다. 

### 오라클

[Oracle R Enterprise][6]라는 제품이 나와 있습니다. R 언어의 명령어, CUI 조작성은 그대로 유지하면서 오라클 데이터베이스에 접속하여 대용량 데이터를 이용한 통계분석을 할 수 있습니다. R 언어의 함수는 오라클 내부에서 병렬실행 가능한 질의로 변환되어 지금까지 이용해온 R 코드를 활용한 분석을 할 수 있습니다. 

*   참고자료: 
    *   [Oracle R Enterprise Bolg][7] 
    *   [Bringing R to the Enterprise][8]

### IBM (Netezza)

현재는 IBM 브랜드가 되었습니다만 [Netezza][9]에 앞서 소개한 Revolution R의 기술이 통합되어 있습니다.  
R 언어의 명령어, CUI 조작성은 그대로 유지하면서 Netezza 내부에 저장된 대용량 데이터에 대해 직접 통계분석을 할 수 있습니다.  
Netezza는 "가능하면 데이터를 움직이지 않는다(I/O 최소화)&rdquo;, &ldquo;데이터를 읽는 속도로 처리한다(스트림 처리)"라는 두 가지 제품구상을 바탕으로 데이터 웨어하우스로서의 고속처리와 R의 실행 속도에서 독창적인 구조로 되어 있습니다.  
앞서 언급한 바와 같이 통상 R 언어는 데이터를 메모리 공간에 이동시켜 계산을 실행하고 있지만, Netezza상의 R 언어는 분석처리를 수식과 데이터에 대한 포인터로 보유하여 데이터 연산이 필요한 시점에 필요한 데이터만을 메모리상에 불러와 고속 병렬처리를 실현하는 구조를 갖추고 있습니다. 

### SAP Sybase

[SybaseIQ][10]는 칼럼지향 데이터웨어하우스의 대표주자로 분석용 데이터 웨어하우스로서 고속성능을 기대할 수 있는 제품입니다. 또한, 어플라이언스제품이 아닌 소프트웨어 기반의 데이터 웨어하우스이므로 확장성도 우수한 것으로 알려져 있습니다.  
SybaseIQ 15.4 이후 버전에서 R 환경으로 부터의 접속을 지원하고 있습니다. RJDBC 인터페이스를 경유하여 SybaseIQ 내부의 데이터를 이용할 수 있습니다. 

*   참고자료: 
    *   [Sybase IQ 15.4 &#8211; for big data analytics][11]

### SAP HANA

독일 SAP사의 인 메모리 데이터베이스 HANA도 R과의 연계강화를 [발표][12]했습니다. HANA DB 내부에서 R 스크립트를 실행할 수 있으며 R 언어가 가진 고급 통계분석기법을 HANA 상에서 이용할 수 있게 되었습니다. 

*   참고자료: 
    *   [SAP HANA R Integration Guide][13]
    *   [Integrating R into the SAP In-Memory Computing Engine][14]
    *   [When SAP HANA met R &#8211; First kiss][15]

### 내부처리의 차이에 따른 성능 격차

이번 포스팅에서 소개한 제품업체들은 공통으로 "R의 고속화 및 대용량데이터 대응"을 표방하고 있습니다. 그러나 그 구현 및 설계는 제품에 따라 다르므로 각각 장단점이 있습니다. 같은 데이터와 같은 함수를 사용한 R 코드를 실행한다 하더라도 제품에 따라 내부처리는 크게 다르기 때문입니다. 이 때문에 "제품을 도입한 결과 상상 이상으로 효과가 있었다"는 가능성도 있지만, 반대의 상황에 부닥치는 일도 있을 수 있습니다. 병렬분산처리 환경의 도입을 검토할 때 위에서 열거한 상용제품을 후보군에 포함해 검토한다면 단순 스팩상의 검토가 아닌 최대한 실제상황에 근접한 데이터를 이용해 확인하는 것이 안전한 도입방법이 될 것입니다. 

최근에는 검증이 가능한 설비를 가지고 있는 제품업체가 늘어나고 있으므로 그러한 검증환경을 이용해 기대에 맞는 효과를 얻을 수 있는가를 사전에 확인하는 것이 최적화에 이르는 가장 빠른 그리고 안전한 방법이라 할 수 있습니다. 

## SAS와 SPSS의 대응

### SAS

최근 R이 데이터 분석 소프트웨어로 주목을 받는다 하더라도 데이터 분석의 선두 주자는 역시 SAS라 할 수 있습니다. SAS를 이용한 분석이 적합한 경우도 있고, R 언어를 이용한 분석이 적합한 경우도 있겠죠.  
SAS/IML을 이용하면 R에서 생성된 행렬 및 데이터프레임을 SAS 데이터 셋으로 변환(반대도 가능)하거나 R 코드를 그대로 실행하여 결과를 SAS로 받아오는 것이 가능합니다. SAS와 R의 데이터 상호변환은 SAS/IML의 4가지 CALL subroutine을 이용하고, SAS 안에서 R 코드를 실행시키기 위해서는 SUBMIT /R; 과 ENDSUBMIT; 사이에 실행하고자 하는 R 코드를 입력하면 됩니다. 

*   참고자료: 
    *   [SAS/IML® R Interface CALL Subroutines][16]
    *   [Accessing R Within the SAS System][17]

### IBM SPSS

데이터 분석에 널리 쓰이는 소프트웨어로 SPSS도 빠질 수 없겠죠. SPSS에서도 R 언어와의 연동을 가능하게 해주는 패키지를 내놓았습니다. 

*   참고자료: 
    *   [IBM SPSS Statistics &#8211; Essentials for R: Windows 설치 지침][18]
    *   [R Integration package for IBM SPSS Statistics][19]

## 맺음말

이번 포스팅에서는 대용량의 데이터를 다루는 기업환경에서 R을 이용할 때 기존에 도입된 데이터베이스 및 통계 분석 소프트웨어와의 연동에 대해서 무지막지할 정도로 간략히 정리해 보았습니다. 하지만, R에 의한 분석뿐 아니라 일반 데이터 분석에서 주의해야 할 점은 분석 결과 및 한 번 만든 분석모형을 무턱대고 신용하지 말고 정기적으로 확인 작업을 해야 한다는 것입니다. 

예를 들어 회귀분석을 한다 할 때 관측한 변수 중 일부만을 설명변수로 사용할 수 있습니다. 하지만 설명변수를 잘못 선택했다 하더라도 수학적으로는 회귀모형을 추정할 수 있으므로 현실과는 다른 잘못된 판단을 할 수도 있습니다. 따라서 정기적으로 데이터 분석을 다시 실행하여 분석 모형에 대한 재확인 및 수정 절차를 거치는 과정이 필요합니다. 

통계분석은 어디까지나 계산에 의한 처리에 불과합니다. 계산된 모형 등이 정말로 믿을 수 있는가를 정확히 판단하기 위해서는 수치를 구하는 것만으로 만족하지 말고 데이터의 의미를 정확히 이해하려는 노력이 필요하다는 것을 항상 명심해야 합니다. 

마지막으로 Hadoop과 관련된 제품 및 기술들은 이번 포스팅에서 생략하였습니다. 좀 더 자료를 모으고 공부한 후 정리해 보도록 하겠습니다.

 [1]: http://socserv.mcmaster.ca/jfox/Misc/Rcmdr/
 [2]: http://www.rstudio.com/ide/
 [3]: http://www.revolutionanalytics.com/
 [4]: http://www.revolutionanalytics.com/news-events/free-webinars/
 [5]: http://www.revolutionanalytics.com/downloads/free-academic.php
 [6]: http://www.oracle.com/us/corporate/features/features-oracle-r-enterprise-498732.html
 [7]: https://blogs.oracle.com/R/
 [8]: http://www.oracle.com/technetwork/database/options/advanced-analytics/r-enterprise/bringing-r-to-the-enterprise-1956618.pdf
 [9]: http://www-01.ibm.com/software/data/netezza/
 [10]: http://www.sybase.com/products/datawarehousing/sybaseiq
 [11]: http://maheshgadgilsblog.blogspot.jp/2012/03/sybase-iq-154-for-big-data-analytics.html
 [12]: http://www.sap.com/korea/about/press/press.epx?pressid=20163
 [13]: http://help.sap.com/hana/SAP_HANA_R_Integration_Guide_en.pdf
 [14]: http://www.vldb.org/pvldb/vol4/p1307-grosse.pdf
 [15]: http://scn.sap.com/community/developer-center/hana/blog/2012/05/21/when-sap-hana-met-r--first-kiss
 [16]: http://support.sas.com/rnd/app/da/iml/CALL/SASandR.html
 [17]: http://www.sascommunity.org/wiki/images/9/98/Accessing_R_Within_the_SAS_System_-_Alan_Mitchell.pdf
 [18]: ftp://public.dhe.ibm.com/software/analytics/spss/documentation/statistics/20.0/ko/rplugin/InstallationDocuments/Windows/Essentials_for_R_Installation_Instructions.pdf
 [19]: ftp://public.dhe.ibm.com/software/analytics/spss/documentation/statistics/20.0/en/rplugin/Manuals/R_Integration_package_for_IBM_SPSS_Statistics.pdf