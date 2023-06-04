# 청년 창업가를 위한 상권 분석 기반 블루오션 지역 추천 서비스
### 9조 한번해보조

## 목차
1. 개발 환경
2. 프로그램 개요
3. 데이터셋
4. 실행 방법 및 유의사항
5. 실행 결과

## 1. 개발 환경
Google Colaboratory 
- Python version: 3.10.11

- Numpy version: 1.22.4
- Pandas version: pandas 1.5.3 / pandas-datareader 0.10.0 / pandas-gbq 0.17.9 / sklearn-pandas 2.2.0
- Matplotlib version: matplotlib                    3.7.1 / 
matplotlib-inline             0.1.6 / 
matplotlib-venn               0.11.9
- Folium version: 0.14.0




## 2. 프로젝트 소개
타겟은 창업에 대한 경험과 자원이 부족한 상태로 가게를 오픈하고 싶은 청년창업가다. 업종을 먼저 선택하고 창업하려는 사람들은, 어느 지역이 나의 창업에 성공을 가져다줄지 결정하는 데 상권 분석 정보가 필요하다.

따라서 본 데이터 분석 프로젝트에서는 공공데이터를 활용해 서울시내 블루오션을 분석해 청년 창업가들에게 창업이 성공할 확률이 높은 최적의 조건을 가진 지역을 추천한다.

## 3. 데이터셋

- 소상공인시장진흥공단_상가(상권)정보 업종코드_20230228.csv: 
2023년 2월 기준 소상공인 상업 업종코드를 분류한 csv 파일을 소상공인시장진흥공단에서 다운받아 이용했다. 사용자가 직접 입력해야하기 때문에 입력 편의를 위해서 대분류명, 중분류명을 사용하였다. 사용자가 어떤 분야의 창업을 원하는지 공인된 기준으로 입력받기 위해 활용하였다. 이후 잠재소비자 수를 업종별로 저장할 때 이용된다.

- 소상공인시장진흥공단_상가(상권)정보_서울_202303.csv: 
2023년 3월 기준, 영업 중인 전국 상가업소 데이터를 제공한다. 소상공인시장진흥공단에서 제작하였으며, 공공데이터 포털에서 csv형식의 원본 데이터파일을 다운받아 이용했다. 각 상가업소의 상권업종코드와 위치한 주소, 행정동코드 등이 표시되어있다. 해당 데이터는 블루오션 필터링 과정에서 입력받은 업종의 상가의 개수를 지역별로 알아내 동종업계 수를 파악하는 데에 사용된다.

- 2-3. 행정동 단위 서울 생활인구.csv: 
서울시가 보유한 공공데이터와 통신데이터로 측정한 특정시점에 서울의 특정 지역에 존재하는 모든인구수 정보이다. 서울특별시에서 제작했으며, 서울열린데이터광장 홈페이지에서 csv파일 원본을 다운받아 이용했다. 전처리 과정에서 행정동 코드별 총생활인구를 전부 더한 데이터는 블루오션 필터링 과정에서 동종업계 대비 생활인구가 높은 지역을 출력할 때 사용된다.

- 2-4. 서울시 우리마을가게 상권분석서비스(상권-소득소비).csv: 
서울 열린데이터 광장에 올라와 있는 상권분석서비스의 csv 파일로, 상권마다의 분야별 지출의 총 금액 데이터를 담고 있다. 2023년 3월에 올라온 2022년 4분기까지의 데이터이다. 상권의 분야마다 소비자 지출 수준을 파악하여 잠재소비자 계산에 활용한다. 

- 2-5. 서울시 우리마을가게 상권분석서비스(상권영역).csv: 
서울 열린데이터 광장에 올라와 있는 상권분석서비스의 csv 파일로, 2021년 12월 기준의 상권코드와 행정동코드 데이터를 담고 있다. 상권 코드로 분류되어 있는 데이터들에 행정동코드를 연결해주기 위해 상권코드와 행정동코드를 모두 포함하고 있는 상권영역 데이터가 필요하다.

- 2-6. 서울시_우리마을가게_상권분석서비스(신_상권_점포)_2021년.csv: 
서울 열린데이터 광장에 올라와 있는 상권분석서비스의 csv 파일로, 상권마다의 개・폐업 데이터를 담고 있다. 2022년 4월에 올라온 2021년 4분기까지의 데이터이다. 지역별로 폐업률을 계산하여 점수화 요인으로 사용한다. 폐업 점포 수 총합을 점포 수 총합으로 나누어 행정동의 폐업률을 알아낸다.

- 2-7. 한국부동산원_상업용부동산 임대동향조사_임대정보_분기별 지역별 임대료(소규모상가).csv: 
한국부동산원에서 제공한 해당 데이터의 원형태는 상권 인덱스에 대해 2022_1분기, 2022_2분기 컬럼만 존재하는 csv 파일이었기에, 직접 행정구 컬럼을 추가해 데이터를 추가해 새로 데이터셋을 구축했다. 지역별로 임대료를 계산하여 점수화 요인으로 사용한다. 

- 2-8. 서울시_우리마을가게_상권분석서비스(신_상권_추정매출)_2021년.csv: 
서울 열린데이터 광장에 올라와 있는 상권분석서비스의 csv 파일로, 2021년 기준 업종별 분기당 매출 데이터다. 데이터 원본을 csv파일로 다운받아 이용하였다. 지역별로 매출을 계산하여 점수화 요인으로 사용한다. 

- 2-9. KIKmix: 
행정안전부 공식 홈페이지 업무안내-지방자치균형발전실-주민등록, 인감 페이지에 게시된 2023. 2. 24. 행정기관(행정동) 및 관할구역(법정동) 현황이다. 행정동코드, 시도명, 시군구명, 읍면동명 등으로 전국의 지역을 나타낸다. 데이터 원본을 csv파일로 다운받아 이용하였다. 이 데이터는 전처리과정에서 행정동코드에 따른 시도명, 시군구명, 읍면동명을 데이터프레임에 붙이는 데 사용된다.










