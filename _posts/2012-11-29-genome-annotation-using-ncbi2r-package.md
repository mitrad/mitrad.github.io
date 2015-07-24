---
title: NCBI2R 패키지를 이용해 게놈 주석 불러오기
author: 양우성
layout: post
permalink: /2012/11/genome-annotation-using-ncbi2r-package/
dsq_thread_id:
  - 949887444
tmac_last_id:
  - 274327321337540608
categories:
  - R-Tips
  - 유전통계학
tags:
  - Annotation
  - Genome
  - NCBI
  - NCBI2R
  - R
comments: true
---
관련분석(association study)을 하다 보면 필연적으로 SNP와 게놈정보를 많이 이용하게 됩니다. 보통 클라이언트에게 제출하는 분석결과 보고서에는 각 SNP에 대한 위치정보와 대응하는 유전자 이름을 같이 넣게 되는데 보통 DNA chip 제조회사가 제공하는 주석(annotation) 정보를 이용하게 됩니다.

저는 관련분석 시 주로 [plink][1]의 분석 결과 파일을 R로 불러와 그래프 작성 및 보고용 파일을 만드는데, plink의 결과 파일에는 유전자에 대한 정보가 포함되어 있지 않기 때문에 별도로 DNA chip 제조회사가 제공하는 주석 파일에서 필요한 부분을 추출하여 보고용 파일을 만들곤 합니다. 대략 아래와 같이&hellip;

![클라이언트에게 돌려 주는 결과][2] 

<!--more-->

또한, 클라이언트에 따라 특정 유전자영역에 대해서만 분석을 의뢰하는 예도 있어서 특정 유전자 영역 내에 있는 SNP의 목록이 필요할 때도 있습니다. 이때도 보통 리눅스의 셸 스크립트를 이용하거나 R에서 작업합니다. 

그리고 간혹 NCBI의 [dbSNP][3]에 접속해 SNP 정보를 확인하는데 관련 지식이 미천한지라 제가 필요로 하는 그 이상의 정보가 존재하기 때문에 필요한 부분만 가지고 올 수 없을까 하는 생각을 했었습니다. 

![dbSNP 검색결과 화면][4]

물론 R에는 온라인 데이터베이스를 탐색할 수 있는 많은 패키지가 존재하며 대표적인 것이 생물정보학을 위한 [Bioconductor][5]라는 프로젝트입니다. 

그러던 중 `NCBI2R` 라는 패키지를 이용해 NCBI의 필요한 정보만을 R로 가지고 오는 방법을 알게 되어 소개하고자 합니다. 

*   `NCBI2R` 패키지 인스톨 

<pre><code class="r">install.packages("NCBI2R")
</code></pre>

*   NCBI dbDNP에서 SNP 정보 가지고 오기

<pre><code class="r">library(NCBI2R)
snplist &lt;- c("rs12345", "rs333", "rs624662")
mySNPs &lt;- GetSNPInfo(snplist)
</code></pre>

<pre><code class="r">mySNPs[, c("marker", "genesymbol", "chr", "chrpos")]
</code></pre>

    ##     marker genesymbol chr   chrpos
    ## 1  rs12345   CRYBB2P1  22 25855459
    ## 2    rs333       CCR5   3 46414947
    ## 3 rs624662             11 56621020
    

*   특정 유전자 안에 있는 SNP 목록 가지고 오기

<pre><code class="r">locusID &lt;- GetIDs("GNA12[SYM] human")
mySNPs &lt;- GetSNPsInGenes(locusID)
head(mySNPs)
</code></pre>

    ## [1] "rs202189675" "rs202173063" "rs202159410" "rs202158005" "rs202152230"
    ## [6] "rs202148124"
    

*   NCBI에서 유전자 정보 가지고 오기

<pre><code class="r">GetGeneInfo(locusID)
</code></pre>

    ##   locusID org_ref_taxname org_ref_commonname   OMIM     synonyms
    ## 1    2768    Homo sapiens              human 604394 RMP gep NNX3
    ##   genesummary                                                genename
    ## 1             guanine nucleotide binding protein (G protein) alpha 12
    ##   phenotypes
    ## 1
    ##                                                                                                                                                                                                                                              pathways
    ## 1 KEGG pathway: Regulation of actin cytoskeleton--- KEGG pathway: Long-term depression--- KEGG pathway: Vascular smooth muscle contraction--- KEGG pathway: MAPK signaling pathway--- Reactome Event:Signal Transduction--- Reactome Event:Hemostasis
    ##   GeneLowPoint GeneHighPoint ori chr genesymbol
    ## 1      2767739       2883959   -   7      GNA12
    ##                                 build   cyto approx
    ## 1 Homo sapiens Annotation Release 104 7p22.2      0
    

*   HapMap 프로젝트 데이터를 이용해 LD 정보 가지고 오기

<pre><code class="r">LD &lt;- GetLDInfo("4", 123456789, 123556789)
</code></pre>

<pre><code class="r">head(LD)
</code></pre>

    ##     chrpos1   chrpos2 pop      SNPA       SNPB Dprime    r2  LOD distance
    ## 1 123458119 123458414 CEU rs4833821 rs13119119  1.000 0.123 2.24      295
    ## 2 123458119 123459238 CEU rs4833821 rs17005650  1.000 0.947 19.1     1119
    ## 3 123458119 123460069 CEU rs4833821 rs13152362  1.000 0.111 2.49     1950
    ## 4 123458119 123460497 CEU rs4833821  rs4459999  1.000 0.072 1.83     2378
    ## 5 123458119 123468500 CEU rs4833821 rs16997701  0.469 0.022 0.26    10381
    ## 6 123458119 123469834 CEU rs4833821 rs10027161  1.000 0.029 0.75    11715
    

물론 많은 양의 SNP에 대한 정보를 가지고 올 때는 시간이 오래 걸릴 수 있습니다. 그러나 관련분석 결과 통계적으로 유의한 SNP는 보통 많아야 수십 개기 때문에 `NCBI2R` 패키지를 이용해 작업하는 편이 훨씬 편리하리라 생각합니다. 다만 DNA chip 제조회사가 제공하는 주석 파일의 위치정보와 NCBI의 위치정보가 다를 수도 있으므로 주의해야 합니다. `NCBI2R` 패키지의 모든 함수목록과 사용법은 [이곳][6]에서 확인하실 수 있습니다. 

**참고사이트: [Genome annotation with NCBI2R][7]**

꼬리말: 이 글은 [RStudio][8]와 knitr 패키지를 이용해 html 파일을 만들고 워드프레스에서 편집기에서 약간의 수정을 하였습니다. 처음부터 워드프레스 에서 글 작성하는 것에 비하면 완전 딴세상이네요. 강추!!!

 [1]: http://pngu.mgh.harvard.edu/%7Epurcell/plink/
 [2]: https://lh3.googleusercontent.com/-monEPlIDXAQ/ULcOTwzCuXI/AAAAAAAACIw/PNA54FutabQ/s512/report.png
 [3]: http://www.ncbi.nlm.nih.gov/projects/SNP/
 [4]: https://lh5.googleusercontent.com/-ReI2cx_SJzw/ULdd4-gKYLI/AAAAAAAACKI/7X_G8_gPWjk/s550/dbSNP-2.png
 [5]: http://bioconductor.org
 [6]: http://cran.r-project.org/web/packages/NCBI2R/index.html
 [7]: http://www.milanor.net/blog/?p=501
 [8]: http://www.rstudio.org