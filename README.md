# 다음 분기 게임 설계 데이터 분석

## 개요

1. 각 국가별 게임 선호도에 유의미한 차이가 있는가?
2. 플랫폼에 따른 게임 판매량이 유의미하게 차이나는가?
3. 연도별 게임의 트렌드는 어떠한가?
4. 출고량이 높은 게임은 어떠한 특성을 가지고 있는가?
    1. 퍼블리셔의 규모와 각 게임 판매량은 유의미한 상관관계가 있는가?
    2. 플랫폼의 규모와 각 게임 판매량은 유의미한 상관관계가 있는가?

## 각 국가별 게임 선호도

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864.png)

 선호도 : $pre(x) = \frac{\sum x(sales)}{len(x)}$ 

한 지역의 총 선호도: $\sum pre(x) = 1$

[prefdt.csv](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/prefdt.csv)

- 위의 그래프에서 미국은 액션, 스포츠, FPS 게임 순으로 선호도가 높음
- 유럽은 미국과 비슷하게 액션, 스포츠, FPS 순으로 선호도가 높음
- 일본은 Role-Playing, 액션, 스포츠 게임 순으로 선호도가 가장 높음.
- 그 외의 국가는 액션, 스포츠, FPS 순으로 선호도가 높음.
- 특이한 점으로, 일본은 롤플레잉 게임의 선호도가 압도적으로 높음.

다음과 같은 선호도 차이가 유의미한 것인지 검증하기 위해 ANOVA Test로 검정.

신뢰도는 95%로 설정

만약 선호도 차이가 유의미하지 않다면, 각 카테고리의 모든 데이터는 모두 비슷한 값을 지닐 것임.

그러나 선호도 차이가 유의미하다면, 각 카테고리의 모든 데이터중 적어도 하나는 유의미한 차이를 보일 것임.

p-value값이 0.05 이상이면 같은 카테고리에 있는 모든 데이터가 유의미한 차이를 보이지 않는다고 볼 수 있음. 반면 p-value값이 0.05 이하면 같은 카테고리에 있는 데이터 중 적어도 한 데이터가 유의미한 차이를 보인다고 할 수 있음.

```python
**[IN]**
def pvaltest(floating):
  if floating > 0.05 and floating < 0.1:
    print("do the test again.")
  elif floating > 0.1:
    print("accept null hypothesis")
  else:
    print("reject null hypothesis")

pvaltest(actpval)
pvaltest(advpval)
pvaltest(mispval)
pvaltest(plfpval)
pvaltest(sptpval)
pvaltest(simpval)
pvaltest(rcepval)
pvaltest(rpgpval)
pvaltest(puzpval)
pvaltest(rtspval)
pvaltest(fgtpval)
pvaltest(fptpval)

**[OUT]**
reject null hypothesis
reject null hypothesis
reject null hypothesis
reject null hypothesis
reject null hypothesis
reject null hypothesis
reject null hypothesis
reject null hypothesis
reject null hypothesis
reject null hypothesis
reject null hypothesis
reject null hypothesis
```

결론: 모든 카테고리에서 적어도 한 지역 이상은 게임 장르 선호도에서 유의미한 차이를 보임.

- **사후검정 결과값 보기(Pairwise Tukeyhsd)**

    ```python
    **[IN]**
    from statsmodels.stats.multicomp import pairwise_tukeyhsd

    v = np.concatenate([act['NA_Sales'], act['EU_Sales'], act['JP_Sales'], act['Other_Sales']])
    labels = ['NA_Sales']*len(act['NA_Sales']) + ['EU_Sales']*len(act['EU_Sales']) + ['JP_Sales']*len(act['JP_Sales']) + ['Other_Sales']*len(act['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([adv['NA_Sales'], adv['EU_Sales'], adv['JP_Sales'], adv['Other_Sales']])
    labels = ['NA_Sales']*len(adv['NA_Sales']) + ['EU_Sales']*len(adv['EU_Sales']) + ['JP_Sales']*len(adv['JP_Sales']) + ['Other_Sales']*len(adv['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([mis['NA_Sales'], mis['EU_Sales'], mis['JP_Sales'], mis['Other_Sales']])
    labels = ['NA_Sales']*len(mis['NA_Sales']) + ['EU_Sales']*len(mis['EU_Sales']) + ['JP_Sales']*len(mis['JP_Sales']) + ['Other_Sales']*len(mis['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([plf['NA_Sales'], plf['EU_Sales'], plf['JP_Sales'], plf['Other_Sales']])
    labels = ['NA_Sales']*len(plf['NA_Sales']) + ['EU_Sales']*len(plf['EU_Sales']) + ['JP_Sales']*len(plf['JP_Sales']) + ['Other_Sales']*len(plf['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([spt['NA_Sales'], spt['EU_Sales'], spt['JP_Sales'], spt['Other_Sales']])
    labels = ['NA_Sales']*len(spt['NA_Sales']) + ['EU_Sales']*len(spt['EU_Sales']) + ['JP_Sales']*len(spt['JP_Sales']) + ['Other_Sales']*len(spt['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([sim['EU_Sales'], sim['JP_Sales'], sim['NA_Sales'], sim['Other_Sales']])
    labels = ['EU_Sales']*len(sim['EU_Sales']) + ['JP_Sales']*len(sim['JP_Sales']) + ['NA_Sales']*len(sim['JP_Sales']) + ['Other_Sales']*len(sim['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([rce['EU_Sales'], rce['JP_Sales'], rce['NA_Sales'], rce['Other_Sales']])
    labels = ['EU_Sales']*len(rce['EU_Sales']) + ['JP_Sales']*len(rce['JP_Sales']) + ['NA_Sales']*len(rce['JP_Sales']) + ['Other_Sales']*len(rce['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([rpg['EU_Sales'], rpg['JP_Sales'], rpg['NA_Sales'], rpg['Other_Sales']])
    labels = ['EU_Sales']*len(rpg['EU_Sales']) + ['JP_Sales']*len(rpg['JP_Sales']) + ['NA_Sales']*len(rpg['JP_Sales']) + ['Other_Sales']*len(rpg['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([puz['EU_Sales'], puz['JP_Sales'], puz['NA_Sales'], puz['Other_Sales']])
    labels = ['EU_Sales']*len(puz['EU_Sales']) + ['JP_Sales']*len(puz['JP_Sales']) + ['NA_Sales']*len(puz['JP_Sales']) + ['Other_Sales']*len(puz['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([rts['EU_Sales'], rts['JP_Sales'], rts['NA_Sales'], rts['Other_Sales']])
    labels = ['EU_Sales']*len(rts['EU_Sales']) + ['JP_Sales']*len(rts['JP_Sales']) + ['NA_Sales']*len(rts['JP_Sales']) + ['Other_Sales']*len(rts['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([fgt['EU_Sales'], fgt['JP_Sales'], fgt['NA_Sales'], fgt['Other_Sales']])
    labels = ['EU_Sales']*len(fgt['EU_Sales']) + ['JP_Sales']*len(fgt['JP_Sales']) + ['NA_Sales']*len(fgt['JP_Sales']) + ['Other_Sales']*len(fgt['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    v = np.concatenate([fps['EU_Sales'], fps['JP_Sales'], fps['NA_Sales'], fps['Other_Sales']])
    labels = ['EU_Sales']*len(fps['EU_Sales']) + ['JP_Sales']*len(fps['JP_Sales']) + ['NA_Sales']*len(fps['JP_Sales']) + ['Other_Sales']*len(fps['Other_Sales'])
    tukey_results = pairwise_tukeyhsd(v, labels, 0.05)
    print(tukey_results)

    **[OUT]**

    **Action**
    Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ==========================================================
     group1     group2   meandiff p-adj  lower   upper  reject
    ----------------------------------------------------------
    EU_Sales    JP_Sales  -0.1102 0.001 -0.1345 -0.0859   True
    EU_Sales    NA_Sales   0.1065 0.001  0.0822  0.1308   True
    EU_Sales Other_Sales  -0.1017 0.001 -0.1261 -0.0774   True
    JP_Sales    NA_Sales   0.2167 0.001  0.1924   0.241   True
    JP_Sales Other_Sales   0.0085 0.784 -0.0159  0.0328  False
    NA_Sales Other_Sales  -0.2083 0.001 -0.2326 -0.1839   True
    ----------------------------------------------------------
    #JP_Sale과 Other_Sale간의 평균 차이만 통계적으로 유의미하지 않음

    **Adventure**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales  -0.0093 0.5272  -0.027  0.0084  False
    EU_Sales    NA_Sales   0.0301  0.001  0.0124  0.0478   True
    EU_Sales Other_Sales   -0.037  0.001 -0.0547 -0.0193   True
    JP_Sales    NA_Sales   0.0394  0.001  0.0217  0.0571   True
    JP_Sales Other_Sales  -0.0277  0.001 -0.0454   -0.01   True
    NA_Sales Other_Sales  -0.0671  0.001 -0.0848 -0.0494   True
    -----------------------------------------------------------
    #EU_Sale과 JP_Sale간의 평균 차이만 통계적으로 유의미하지 않음

    **Misc**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales  -0.0619  0.001 -0.1006 -0.0231   True
    EU_Sales    NA_Sales    0.111  0.001  0.0722  0.1498   True
    EU_Sales Other_Sales  -0.0806  0.001 -0.1194 -0.0418   True
    JP_Sales    NA_Sales   0.1728  0.001  0.1341  0.2116   True
    JP_Sales Other_Sales  -0.0188 0.5876 -0.0576    0.02  False
    NA_Sales Other_Sales  -0.1916  0.001 -0.2304 -0.1528   True
    -----------------------------------------------------------
    #JP_Sale과 Other_Sale의 평균차만 통계적으로 유의미하지 않음

    **Platform**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales  -0.0803 0.2056 -0.1859  0.0253  False
    EU_Sales    NA_Sales   0.2804  0.001  0.1748  0.3859   True
    EU_Sales Other_Sales  -0.1709  0.001 -0.2765 -0.0653   True
    JP_Sales    NA_Sales   0.3607  0.001  0.2551  0.4662   True
    JP_Sales Other_Sales  -0.0906 0.1221 -0.1962   0.015  False
    NA_Sales Other_Sales  -0.4512  0.001 -0.5568 -0.3457   True
    -----------------------------------------------------------
    #EU_Sales과 JP_Sales, JP_Sales과 Other_Sales의 평균차가 통계적으로 유의미하지 않음

    **Sports**
       Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ==========================================================
     group1     group2   meandiff p-adj  lower   upper  reject
    ----------------------------------------------------------
    EU_Sales    JP_Sales  -0.1027 0.001 -0.1539 -0.0516   True
    EU_Sales    NA_Sales   0.1289 0.001  0.0777    0.18   True
    EU_Sales Other_Sales  -0.1038 0.001 -0.1549 -0.0526   True
    JP_Sales    NA_Sales   0.2316 0.001  0.1805  0.2828   True
    JP_Sales Other_Sales   -0.001   0.9 -0.0522  0.0501  False
    NA_Sales Other_Sales  -0.2327 0.001 -0.2838 -0.1815   True
    ----------------------------------------------------------
    #JP_Sales과 Other_Sales의 평균차가 통계적으로 유의미하지 않음

    **Simulation**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales  -0.0585 0.0105 -0.1069   -0.01   True
    EU_Sales    NA_Sales   0.0809  0.001  0.0324  0.1294   True
    EU_Sales Other_Sales  -0.0965  0.001  -0.145 -0.0481   True
    JP_Sales    NA_Sales   0.1393  0.001  0.0909  0.1878   True
    JP_Sales Other_Sales  -0.0381 0.1806 -0.0865  0.0104  False
    NA_Sales Other_Sales  -0.1774  0.001 -0.2259  -0.129   True
    -----------------------------------------------------------
    #JP_Sales와 Other_Sales의 평균차가 통계적으로 유의미하지 않음

    **Racing**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales  -0.1466  0.001 -0.2001 -0.0931   True
    EU_Sales    NA_Sales   0.0982  0.001  0.0447  0.1517   True
    EU_Sales Other_Sales    -0.13  0.001 -0.1835 -0.0766   True
    JP_Sales    NA_Sales   0.2448  0.001  0.1913  0.2983   True
    JP_Sales Other_Sales   0.0166 0.8384 -0.0369    0.07  False
    NA_Sales Other_Sales  -0.2282  0.001 -0.2817 -0.1747   True
    -----------------------------------------------------------
    #JP_Sales과 Other_Sales의 평균차가 통계적으로 유의미하지 않음

    **Role-Playing**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales   0.1091  0.001  0.0594  0.1588   True
    EU_Sales    NA_Sales   0.0926  0.001  0.0429  0.1423   True
    EU_Sales Other_Sales  -0.0863  0.001  -0.136 -0.0365   True
    JP_Sales    NA_Sales  -0.0165 0.8065 -0.0662  0.0332  False
    JP_Sales Other_Sales  -0.1954  0.001 -0.2451 -0.1456   True
    NA_Sales Other_Sales  -0.1789  0.001 -0.2286 -0.1291   True
    -----------------------------------------------------------
    #JP_Sale과 Other_Sale의 평균차가 통계적으로 유의미하지 않음

    **Puzzle**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales   0.0102    0.9 -0.0801  0.1005  False
    EU_Sales    NA_Sales   0.1256  0.002  0.0354  0.2159   True
    EU_Sales Other_Sales  -0.0672 0.2225 -0.1574  0.0231  False
    JP_Sales    NA_Sales   0.1154 0.0056  0.0252  0.2057   True
    JP_Sales Other_Sales  -0.0774 0.1225 -0.1676  0.0129  False
    NA_Sales Other_Sales  -0.1928  0.001 -0.2831 -0.1026   True
    -----------------------------------------------------------
    #NA_Sales과 Other_Sales, EU_Sales과 NA_Sales, JP_Sales과 NA_Sales의 평균차가
    #통계적으로 유의미함

    **RTS(Strategy)**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales   0.0064    0.9 -0.0217  0.0345  False
    EU_Sales    NA_Sales   0.0344 0.0091  0.0063  0.0625   True
    EU_Sales Other_Sales  -0.0502  0.001 -0.0783 -0.0221   True
    JP_Sales    NA_Sales    0.028 0.0513 -0.0001  0.0561  False
    JP_Sales Other_Sales  -0.0566  0.001 -0.0847 -0.0285   True
    NA_Sales Other_Sales  -0.0846  0.001 -0.1127 -0.0565   True
    -----------------------------------------------------------
    #EU_Sales와 JP_Sales, JP_Sales와 NA_Sales의 평균차가 통계적으로 유의미하지 않음

    **Fighting**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales  -0.0153 0.7383 -0.0556   0.025  False
    EU_Sales    NA_Sales   0.1443  0.001  0.1039  0.1846   True
    EU_Sales Other_Sales  -0.0763  0.001 -0.1167  -0.036   True
    JP_Sales    NA_Sales   0.1595  0.001  0.1192  0.1998   True
    JP_Sales Other_Sales  -0.0611  0.001 -0.1014 -0.0207   True
    NA_Sales Other_Sales  -0.2206  0.001 -0.2609 -0.1803   True
    -----------------------------------------------------------
    #EU_Sales과 JP_Sales의 평균차가 통계적으로 유의미하지 않음

    **FPS(Shooting)**
        Multiple Comparison of Means - Tukey HSD, FWER=0.05    
    ===========================================================
     group1     group2   meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------
    EU_Sales    JP_Sales  -0.2125  0.001 -0.2812 -0.1438   True
    EU_Sales    NA_Sales   0.2064  0.001  0.1378  0.2751   True
    EU_Sales Other_Sales  -0.1628  0.001 -0.2315 -0.0941   True
    JP_Sales    NA_Sales    0.419  0.001  0.3503  0.4877   True
    JP_Sales Other_Sales   0.0498 0.2451 -0.0189  0.1184  False
    NA_Sales Other_Sales  -0.3692  0.001 -0.4379 -0.3005   True
    -----------------------------------------------------------
    #JP_Sales과 Other_Sales의 평균차가 통계적으로 유의미하지 않음
    ```

## 지역별 플랫폼당 게임 판매량

[Google Colaboratory](https://colab.research.google.com/drive/1XDCtX5HsQdeY0p7lrmtWjofydqrPm4F_?usp=sharing)

위의 colab 노트북을 통해 전세계 콘솔 선호도를 더 쉽게 볼 수 있습니다.

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/top10plfna.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/top10plfna.png)

미국의 경우 플레이스테이션과 닌텐도, 그리고 Xbox 플랫폼의 게임이 연도에 따라 비슷한 판매량을 보임.

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/top10plfjp.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/top10plfjp.png)

일본의 경우 플레이스테이션과 닌텐도 플랫폼 게임이 가장 판매량 많음

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/top10plfeu.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/top10plfeu.png)

유럽의 경우 대체로 플레이스테이션이 강세이며, 닌텐도가 제품 주기에 따라 판매량을 역전하기도 함.

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/top10plfot.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/top10plfot.png)

그 외의 국가에서는 플레이스테이션 플랫폼 게임 판매량이 가장 많음.

```python
nintendo = ['GB', 'DS', 'Wii', 'GBA', 'GC', 'N64', 'WiiU', '3DS', 'NES', ]
playstation = ['PS', 'PS2', 'PS3', 'PS4', 'PSP', 'PSV']
xbox = ['XB', 'X360', 'XOne']
```

- 최근 10년간 닌텐도, 플레이스테이션, xbox 플랫폼의 게임이 모든 지역에서 평균적으로 가장 많이 판매.
- 일본을 제외한 모든 국가의 경우, 플랫폼에 따른 게임 판매량 차이가 유의미한지 ANOVA Test로 검증
- 일본의 경우, 플레이스테이션 플랫폼 게임의 판매량과 닌텐도 플랫폼 게임의 판매량을 을 2-Sample-t test를 통해 검증
- 미국의 경우 ANOVA의 p-value가 약 0.17로 세 집단간의 차이가 유의미하지 않으며 우연에 의해 차이가 발생했을 가능성 있음.
- 일본 역시 2 sample t-test의 p-value가 약 0.18로 플레이스테이션과 닌텐토 플랫폼 판매량 차이가 유의미하지 않으며 우연에 의해 차이가 발생했을 가능성 있음.
- 유럽과 그 외 지역의 경우 ANOVA의 p-value가 각각 0.001과 약 1.67e-06으로 세 집단가운데 적어도 한 집단은 다른 분포를 지니며, 이는 적어도 한 플랫폼의 판매량 차이가 유의미하다는 것을 의미.
- 이 차이는 우연에 의해 발생했을 가능성이 매우 낮음.
- **사후검정 결과값 보기(Pairwise Tukeyhsd)**

    ```python
    **Europe**
    Multiple Comparison of Means - Tukey HSD, FWER=0.05        
    ===================================================================
        group1        group2   meandiff p-adj   lower    upper   reject
    -------------------------------------------------------------------
    EU_playstation     eu_Xbox -28.5853  0.001 -45.9058 -11.2647   True
    EU_playstation eu_nintendo -14.1958 0.1281 -31.5164   3.1248  False
           eu_Xbox eu_nintendo  14.3895 0.1216  -2.9311    31.71  False
    -------------------------------------------------------------------
    #유럽의 Playstation과 Xbox만 통계적 유의미성을 가진다.

    **Others**     
    Multiple Comparison of Means - Tukey HSD, FWER=0.05          
    =======================================================================
        group1           group2      meandiff p-adj   lower   upper  reject
    -----------------------------------------------------------------------
        Other_Xbox    Other_nintendo   3.5484 0.4864 -3.8538 10.9507  False
        Other_Xbox Other_playstation  17.0642  0.001   9.662 24.4664   True
    Other_nintendo Other_playstation  13.5158  0.001  6.1136  20.918   True
    -----------------------------------------------------------------------
    #기타 지역의 Xbox와  Playstation, 그리고 Playstation과 Nintendo가 통계적 유의미성을 지닌다.
    ```

## 연도별 게임 트렌드

### 10년 단위 게임 트렌드

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/trendsna.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/trendsna.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/trendsjp.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/trendsjp.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/trends.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/trends.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/trendsot.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/trendsot.png)

1. 대부분 게임의 출시량과 판매량이 감소 추세인 가운데, FPS 게임의 판매량 감소가 가장 낮으며 오히려 총 판매량이 높아진 경우도 생김.

### 최근 게임 트렌드(~16년도)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(10).png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(10).png)

단위: Million

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/countsgamena.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/countsgamena.png)

단위: 개수

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(11).png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(11).png)

단위: Million

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/countsgameeu.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/countsgameeu.png)

단위: 개수

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(12).png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(12).png)

단위: Million

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(18).png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(18).png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(13).png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(13).png)

단위: Million

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(19).png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_(19).png)

- 게임 판매량, 발매량 모두 2008, 2009년에 정점을 찍고 이후 감소 추세
- 반면 일본을 제외한 거의 모든 곳에서 FPS의 발매량 대비 판매량이 타 장르 대비 높은 수준.
- 이는 FPS 게임의 공급 대비 수요가 다른 장르의 게임보다 많을 수 있다는 뜻.
- → 다른 장르 대비 경쟁이 덜하다 추측 가능

## 판매량이 높은 게임 분석

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/download.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/download.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.15.37.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.15.37.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.15.47.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.15.47.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/download-2.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/download-2.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.35.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.35.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.46.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.46.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/download-1.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/download-1.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.02.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.02.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.11.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.11.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/download-3.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/download-3.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.57.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.16.57.png)

![%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.17.13.png](%E1%84%83%E1%85%A1%E1%84%8B%E1%85%B3%E1%86%B7%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A6%E1%84%8B%E1%85%B5%E1%86%B7%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%20%E1%84%83%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%87%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A5%E1%86%A8%202d78edf748744adc9085717aae851864/_2021-03-30__9.17.13.png)

- 판매량 상위 10개 제품은 모두 잘 알려진 퍼블리셔가 퍼블리싱한 게임.
- 트렌드와 상위권 포진 게임들이 반드시 일치하지는 않음.
- 판매량 상위 플랫폼에서 가장 출고량이 높은 게임들이 출고되는 경향이 강함.

추측: 게임 출고량은 트렌드보다 플랫폼, 퍼블리셔와 상관관계가 있을 것이다.

그렇다면 퍼블리셔의 규모와 게임 매출액의 상관관계는 어떻게 될까?

## 퍼블리셔의 규모와 게임 판매량의 상관관계

- 퍼블리셔의 규모 = 퍼블리싱한 게임의 개수로 가정함.
- 신뢰도 = 95%
- 퍼블리셔의 규모가 거대할수록 더 많은 게임을 퍼블리싱할 여유가 생기기 때문.
- p-value가 0.05보다 작으면 퍼블리셔의 규모와 게임 판매량에 상관관계가 있는 것
- 반대로 p-value가 0.05보다 크면 퍼블리셔의 규모와 게임 판매량의 상관관계가 있다는 증거가 없는 것.

```python
**[IN]**
best_publisher = df.reset_index()['Publisher'].to_list()
pubpower = df.query('Publisher == @best_publisher').groupby(['Year', 'Publisher']).agg({'Publisher': 'count'}).rename(columns = {'Publisher': 'Count'})
pub = df.groupby(['Publisher', 'Year'], as_index=False).mean().set_index(['Year', 'Publisher']).sort_values(by=['Year'], axis=0)

pubna = pd.concat([pubpower['Count'], pub['NA_Sales']], axis=1) #merge
pubeu = pd.concat([pubpower['Count'], pub['EU_Sales']], axis=1)
pubjp = pd.concat([pubpower['Count'], pub['JP_Sales']], axis=1)
pubot = pd.concat([pubpower['Count'], pub['Other_Sales']], axis=1)
****
stats.pearsonr(pubna.Count, pubna.NA_Sales)
stats.pearsonr(pubeu.Count, pubeu.EU_Sales)
stats.pearsonr(pubjp.Count, pubjp.JP_Sales)
stats.pearsonr(pubot.Count, pubot.Other_Sales)

**[OUT]**
(0.16106320113668707, 5.966395249377218e-15) #(Corr, p-value) 
(0.19826651639917758, 5.3931230900369585e-22)
(-0.013973550790114975, 0.5011219743929821)
(0.2861656602495528, 5.691050333913845e-45)
```

- 상관분석 결과 일본은 p-value가 0.05보다 높으며, 미국, 유럽, 그 외 지역은 p-value가 0.05보다 낮음.
- 보통 0.1 ~ 0.3의 상관계수는 약한 상관관계가 있다고 해석한다.
- 따라서 미국, 유럽, 그 외 지역에서는 퍼블리셔의 규모와 게임 판매량의 상관관계가 있다고 볼 수 있다.
- 반면 일본에서는 퍼블리셔의 규모와 게임 판매량의 상관관계가 있다고 할 수 없다.

## 플랫폼의 규모와 게임 판매량의 상관관계

- 플랫폼의 규모 = 플랫폼에 런칭한 게임의 개수로 가정함.
- 신뢰도 = 95%
- 플랫폼에 런칭한 게임의 개수가 많아질수록 플랫폼의 규모가 거대해지기 때문
- p-value가 0.05보다 작으면 플랫폼의 규모와 게임 판매량에 상관관계가 생기는 것
- 반대로 p-value가 0.05보다 크면 플랫폼의 규모와 게임 판매량간 상관관계가 있다는 증거가 없는 것.

```python
**[In]**
plfpower = df.groupby(['Platform', 'Year'], as_index=False).mean().set_index(['Year', 'Platform']).sort_values(by=['Year'], axis=0)
plfs = df.sort_values(['Year', 'Platform']).groupby(['Year', 'Platform']).mean()
plfna = pd.concat([plfpower['Count'], plfpp['NA_Sales']], axis=1)
plfeu = pd.concat([plfpower['Count'], plfpp['EU_Sales']], axis=1)
plfjp = pd.concat([plfpower['Count'], plfpp['JP_Sales']], axis=1)
plfot = pd.concat([plfpower['Count'], plfpp['Other_Sales']], axis=1)

stats.pearsonr(plfna.Count, plfna.NA_Sales)
stats.pearsonr(plfeu.Count, plfeu.EU_Sales)
stats.pearsonr(plfjp.Count, plfjp.JP_Sales)
stats.pearsonr(plfot.Count, plfot.Other_Sales)

**[Out]**
(-0.16525641959220058, 0.0418900992548478)
(-0.04791480333540331, 0.5577453382132943)
(-0.22239799637744515, 0.0058899110414261996)
(0.29958032570665066, 0.0001772053896050947)
```

- 미국과 일본에서 게임과 플랫폼의 규모는 약하거나 미약한 음의 상관관계를 가진다.
- 유럽은 p-value가 0.05보다 크므로 플랫폼과 게임 판매량간 상관관계가 있다고 보기 어렵다.

```python
**[In]**
ninpp = df[df['Platform'] == 'Nintendo'].groupby(['Platform', 'Year'], as_index=False).mean().set_index(['Year', 'Platform']).sort_values(by=['Year'], axis=0)
pspp = df[df['Platform'] == 'PlayStation'].groupby(['Platform', 'Year'], as_index=False).mean().set_index(['Year', 'Platform']).sort_values(by=['Year'], axis=0)

ninna = pd.concat([ninpower['Count'], ninpp['NA_Sales']], axis=1) #nintendo
nineu = pd.concat([ninpower['Count'], ninpp['EU_Sales']], axis=1)
ninjp = pd.concat([ninpower['Count'], ninpp['JP_Sales']], axis=1)
ninot = pd.concat([ninpower['Count'], ninpp['Other_Sales']], axis=1)

psna = pd.concat([pspower['Count'], pspp['NA_Sales']], axis=1) #playstation
pseu = pd.concat([pspower['Count'], pspp['EU_Sales']], axis=1)
psjp = pd.concat([pspower['Count'], pspp['JP_Sales']], axis=1)
psot = pd.concat([pspower['Count'], pspp['Other_Sales']], axis=1)

print(stats.pearsonr(ninna.Count, ninna.NA_Sales)) #nintendo Result
print(stats.pearsonr(nineu.Count, nineu.EU_Sales))
print(stats.pearsonr(ninjp.Count, ninjp.JP_Sales))
print(stats.pearsonr(ninot.Count, ninot.Other_Sales))

**[Out]**
(-0.3976799809488482, 0.017992723905817083) #nintendo(corr, pvalue)
(-0.3009237322911985, 0.07898483010107123)
(-0.5768849851155042, 0.0002860183509933711)
(-0.19937407299652074, 0.2508654687160458)
```

```python
**[In]**
print(stats.pearsonr(psna.Count, psna.NA_Sales)) #playstation result
print(stats.pearsonr(pseu.Count, pseu.EU_Sales))
print(stats.pearsonr(psjp.Count, psjp.JP_Sales))
print(stats.pearsonr(psot.Count, psot.Other_Sales))

**[Out]**
(0.22312135822669743, 0.29463869950160576) #playstation(corr, pvalue)
(0.2552454789820596, 0.2286846974243712)
(-0.44494603301038144, 0.029356391014599224)
(0.6344234092705844, 0.0008696956945228564)
```

## 결론

1. 일본을 제외한 모든 국가가 액션, 스포츠, FPS 게임 순으로 선호도가 높으며, 지역간의 선호도 차이는 최소 한 지역의 경우에 유효하다.
2. 지역별 콘솔당 판매량의 경우, 북미와 일본의 경우 콘솔별 판매량이 차이가 유의미하지 않았으나 유럽 및 그 외 지역의 경우 콘솔별 판매량 차이가 유의미하다.
3. 최신 게임 트렌드 분석을 통해 일본을 제외한 모든 국가에서 액션 게임과 FPS(슈터), 그리고 스포츠 게임의 판매량이 가장 높음을 알아냈으나, 모든 게임의 판매량과 공급이 점점 떨어지고 있음. 그러나 FPS의 경우 아직 다른 게임에 비해 발매량 대비 판매량이 높음.
4. 일본을 제외한 전 지역에서는 퍼블리셔의 규모와 판매량에 미약한 상관관계를 지녔으며, 플랫폼의 규모의 경우 유럽을 제외한 전 지역에서 약한 음의 상관관계를 지님. 닌텐도의 경우 북미, 일본에서 약하거나 보통인 음의 상관관계를 가짐. 퍼스트파티 게임과의 경쟁 때문인 것으로 추측. 한편 플레이스테이션의 경우 일본에서 음의 상관관계, 그 외 지역에서는 양의 상관관계를 지님.
5. 이와 같은 결과들을 종합해보면, 다음에 만들어야 할 게임은 플레이스테이션 기반의 미국, 유럽, 기타 지역을 타겟으로 한 FPS 게임임을 알 수 있음.