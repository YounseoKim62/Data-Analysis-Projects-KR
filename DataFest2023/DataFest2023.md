# The American Statistical Association DataFest - 최우수상 수상 (2023)
* Team Megabyte (Younseo Kim, Yong Jun Choi, Woosung Lim, Hyeongkwon Kim & Taehwan Lee)
* 2023.03.25 ~ 2023.03.26
* 프로그래밍 언어 및 도구: R (tidyverse, dplyr, ggplot2), Tableau, Excel, Figma
* 활용한 기술: EDA, 데이터 전처리, 알고리즘 개발, 프로토타입 UI/UX 디자인

<br/>

## [프로젝트 소개]
* 미국 변호사 협회에서 제공한 50만 건 이상의 의뢰인 데이터, 2만 명의 변호사 데이터, 30만 건 이상의 상담 데이터를 활용하여 협회의 무료 법률 지원 상담 서비스의 효율성을 개선한 프로젝트

<br/>

## Step 1: 의뢰인의 무료 법률 서비스 자격 판별

<br/>

### [문제점] 
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/0a999767-1408-43e4-9921-9e934a314d7a)

* 주 마다 무료 법률 서비스 기준이 상이합니다.
* 무료 법률 서비스를 받기 위해서는 자산과 수입 두 가지 기준을 모두 충족해야 하며, 관련 변수들이 나뉘어져 있어 변호사들이 의뢰인이 무료 법률 서비스 기준에 부합하는지 한눈에 판단하기 어렵습니다.

<br/>

### [데이터 전처리]
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/8f389c0d-3dc0-4e4a-b14c-e733799a2cd8)

![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/0cd50bdb-bcd6-4a71-abc9-af242cb1e94a)

1. 'clients' 데이터셋에서 AnnualIncome (수입 변수)이 NULL인 경우, 자산 관련 변수들도 모두 NULL이므로 **AnnualIncome이 NULL인 의뢰인들을 제거**합니다.
2. 'clients' 데이터셋의 자산과 수입에 관련된 변수들이 Categorical Variable (범주형 변수)로 저장되어 있어 **Numeric Variable (숫자형 변수)로 변환**합니다.
3. 'clients' 데이터셋의 StateAbbr 변수를 통해 'statesites' (주별 무료 법률 서비스 기준 데이터셋)과 **LEFT JOIN하여 통합된 데이터셋을 생성**합니다.
4. *(대회 진행요원이 자산과 관련된 변수들에서 의뢰인들이 0을 적는 대신 기입을 하지 않은 경우가 많다고 하여, 자산과 관련된 변수들 중 1개 이상 기입한 의뢰인들에 한해서는 NULL 값을 0으로 간주합니다)* <br/> 'clients' 데이터셋의 자산과 관련된 변수들을 하나의 변수로 통합한 **sum_assets 파생변수를 생성**합니다.

<br/> 

### [알고리즘 개발]
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/64cb43c4-19ac-4c9f-9607-6429093e605b)

1. 수입 기준:
   * AnnualIncome이 AllowedIncome보다 낮으면 ProBono_income을 'Y', 높으면 'N'으로 저장합니다.

2. 자산 기준:
   * 'statesites' (주별 무료 법률 기준 데이터셋)에 없는 주 출신의 경우 ProBono_assets를 'NAA (No Allowed Assets)'로 저장합니다.
   * 자산 관련 변수들이 모두 NULL이면 ProBono_assets를 'NC (Not Classified)'로 저장합니다.
   * sum_assets가 AllowedAssets보다 낮으면 ProBono_assets를 'Y', 높으면 'N'으로 저장합니다.

3. 최종 판정:
   * ProBono_income과 ProBono_assets가 모두 'Y'이면 ProBono_final을 'Y'로 저장합니다.
   * ProBono_income 또는 ProBono_assets 중 하나라도 'N'이면 ProBono_final을 'N'으로 저장합니다.
   * ProBono_income이 'Y'이고 ProBono_assets가 'NAA'이면 ProBono_final을 'Y'로 저장합니다. (무료 법률 서비스 기준 자산이 없는 주들이 존재)
   * ProBono_income과 상관없이 ProBono_assets가 'NC'이면 ProBono_final을 'NC'로 저장합니다.

<br/> 

## Step 2: 효율적인 변호사와 의뢰인 매칭

<br/> 

### [문제점]
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/2b281ef6-1fcf-43c9-8df4-91df789ba9bc)

![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/06b28e1b-fd7b-4ab6-ad76-e9c515a01689)

* 의뢰인이 변호사의 전문 분야와 실력을 판단할 수 있는 요소가 없습니다.
* 변호사가 지금까지 의뢰인들과 얼마나 상담했는지, 어떤 전문 분야에서 가장 많이 상담했는지, 얼마나 많은 의뢰를 처리했는지 한눈에 파악할 수 없습니다.

<br/> 

 ### [데이터 전처리]
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/b91fb619-1363-4c9e-a09c-f61a19ef65f9)

1. 'questions' (상담 기록 데이터셋)에서 **변호사가 답을 하지 않은 기록들을 제거**합니다. (TakenByAttorneyUno가 NULL인 경우)
2. 'questions'에서 변호사가 가장 많이 답변한 Category (전문분야)와 Subcategory (세부 전문분야)를 분석하여 변호사의 **전문분야와 세부 전문분야를 알려주는 변수들을 기존 'attorneys' (변호사 데이터셋)에 생성**합니다.
3. 'questions'에서 변호사가 맡은 상담 기록의 횟수를 **NumberofCases라는 변수를 'attorneys'에 생성**합니다.
4. 'attorneytimeentries' (상담 당 변호사가 할애한 시간 데이터셋)에서 변호사가 할애한 시간의 총합을 **TotalHours라는 변수를 'attorneys'에 생성**합니다.
* *모든 변수들이 NULL인 변호사는 등록만 되어 있을 뿐, 상담을 진행하지 않은 변호사입니다.*

<br/> 

### [알고리즘 개발]
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/fae49738-c932-47a7-9297-56aa836132e5)

1. TotalHours와 NumberofCases의 하위 40%, 60%, 100%의 기준값을 구하여 TotalHoursLevel과 NumberofCasesLevel의 기준점을 설정합니다.
2. TotalHours에 대해 하위 40%는 Level 1, 40%에서 80% 사이는 Level 2, 80%에서 100% 사이는 Level 3으로 설정하며, TotalHours가 0인 경우는 Level 1로 간주합니다.
3. NumberofCases에 대해서도 동일하게 적용하여 NumberofCasesLevel 변수를 생성하되, NumberofCases가 0인 경우는 Level 1로 간주합니다.
4. TotalHoursLevel과 NumberofCasesLevel을 종합하여 변호사의 전체적인 실력을 판단할 수 있는 Level 변수를 생성합니다.

<br/> 

## Step 3: 미국 내 법률 상담 트렌드 분석

<br/> 

### [가장 많이 상담된 법률 카테고리]
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/75161971-a632-4db0-b4d0-60bf2c085f4e)

* 주 별로 미국 변호사 협회의 무료 법률 상담 서비스에서 가장 많이 상담된 카테고리는 **가족 및 아동(Family and Children)** 으로 나타남
* 이는 많은 주에서 이혼, 양육권 분쟁, 가정 폭력 등 가족 갈등과 관련된 법률 상담이 많이 이루어지고 있음을 나타

<br/> 

### [두번째로 많이 상담된 법률 카테고리]
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/88b2c294-08f8-4a43-b915-c1bfb03b289d)

* 주별로 미국 변호사 협회의 무료 법률 상담 서비스에서 두 번째로 많이 상담된 카테고리는 **기타(Others)**로 나타났습니다.
* 기타 카테고리에는 상해, 자연재해, 이민, 세금과 관련된 상담들이 포함되며, 이는 많은 주에서 일상 생활과 관련된 법률 상담이 많이 이루어지고 있음을 나타냅니다.

<br/> 

### [세번째로 많이 상담된 법률 카테고리]
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/b1cecf1d-22f5-412c-a98d-13561b8622b8)

* 주별로 미국 변호사 협회의 무료 법률 상담 서비스에서 세 번째로 많이 상담된 카테고리는 **주거 및 노숙자(Housing and Homelessness)**로 나타났습니다.
* 이는 여러 주에서 주거 안정성, 세입자 권리, 노숙자 문제와 관련된 법률 상담이 많이 이루어지고 있음을 나타냅니다.

<br/> 

### [시간에 따른 법률 상담 카테고리별 추이]
![trend graph](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/0edbeab7-12d9-4c7e-a84d-0b0a1442211f)

* 2020년도 초반부터 가족 및 아동, 기타 (상해, 자연재해, 이민, 세금과 관련된 상담), 주거 및 노숙자, 노동, 고용 및 실업에 관련 법률 상담의 급격한 증가가 관찰되었습니다.
* 가족 및 아동 상담의 증가는 코로나로 인해 재택근무가 증가하면서 가족 간의 갈등이 많아졌기 때문으로 추측됩니다.
* 기타 카테고리 상담의 증가는 코로나 관련 법률 상담이 자연재해로 분류되었기 때문입니다.
* 주거 및 노숙자 상담의 증가는 팬데믹 동안 경제적 어려움으로 주거 불안정성이 심화되었기 때문으로 추정됩니다.
* 노동, 고용 및 실업 관련 상담의 증가는 팬데믹 동안 실업률 증가와 재택근무로 인한 변화 때문으로 추정됩니다.

<br/> 

## Step 4: 실제 적용 프로토타입

<br/> 

### [의뢰인의 무료 법률 서비스 자격 판별, 효율적인 변호사와 의뢰인 매칭]
![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/c778e406-18a9-4b4f-9fea-8dbef37a751c)

![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/9b29b63d-813d-446a-ab85-0e87abcad67a)

* 프로토타입을 사용함으로써 의뢰인이 무료 법률 지원 상담 서비스의 수입과 자산 기준에 부합하는지 확인할 수 있습니다.
* 또한, 의뢰인이 신청한 상담의 법률 카테고리를 전문 분야로 가지고 있는 변호사를 추천해주며, 변호사가 지금까지 얼마나 많은 의뢰인들과 상담했는지, 어떤 전문 분야에서 가장 많이 상담했는지, 얼마나 많은 의뢰를 처리했는지 한눈에 파악할 수 있습니다.

<br/> 

### [미국 내 법률 상담 트렌드 분석]

<br/> 

![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/16eb19c9-2492-4d11-9a9e-35236cb8e611)

![image](https://github.com/YounseoKim62/Data-Analysis-Projects-KR/assets/161654460/1edbb54a-1ad6-4b10-8684-f0a788c62b72)

* 프로토타입을 사용함으로써 변호사는 어떤 법률 카테고리에 상담이 집중되는지 파악하여 업무를 효율적으로 배분하고, 시간이 많이 소요되는 카테고리에 대한 대비를 할 수 있습니다.
* 또한, 변호사 협회는 상담 빈도가 높은 카테고리에 인적 자원과 시간을 집중 투입하여 효율적인 자원 관리를 할 수 있습니다.



