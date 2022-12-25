> # 제주도 도로 교통량 예측 프로젝트

## 1. 주제 정의
* 주제: 제주도 도로 교통량 예측 AI 모델 개발
* 요약: 제주도의 교통 정보를 이용하여 도로 교통량을 예측하는 모델을 개발함

## 2. 필요성 및 목적
### 현황 파악
* 제주도 내 주민등록인구는 2022년 기준 약 68만 명으로, 연평균 1.3% 정도 매년 증가하고 있습니다.
* 또한 외국인과 관광객까지 고려하면 전체 상주인구는 90만 명을 넘을 것으로 추정되며, 제주도민 증가와 외국인의 증가로 현재 제주도의 교통체증이 심각한 문제로 떠오르고 있습니다.

### 분석 목적
* 탐색적 데이터 분석을 통해 제주도의 도로 교통량에 영향을 미치는 요인을 알아보고, 다양한 머신러닝 기법을 활용하여 도로 교통량을 예측하는 최적의 AI 모델을 개발하고자 합니다.

## 3. 활용 데이터 및 변수

<details>
<summary>활용 데이터</summary>
<div markdown="1">       

* train 데이터(.csv)  
-2022년 8월 이전 데이터만 존재 (단, 날짜가 모두 연속적이지 않음)  
-4,701,217개의 데이터  
-id: 샘플별 고유 id  
-target: 도로의 차량 평균 속도(km)  

|id|base_date|day_of_week|base_hour|lane_count|road_rating|road_name|multi_linked|connect_code|maximum_speed_limit|vehicle_restricted|weight_restricted|height_restricted|road_type|start_node_name|start_latitude|start_longitude|start_turn_restricted|end_node_name|end_latitude|end_longitude|end_turn_restricted|target|
|------|---|---|---|---|---|---|---|---|---|---|---|---|---|---|----|---|---|---|---|---|---|---|
|TRAIN_0000000|20220623|목|17|1|106|지방도1112호선|0|0|60.0|0|32400|0|3|제3교래교|33.427747|126.662612|없음|제3교래교|33.427749|126.662335|없음|52.0|
|TRAIN_0000001|20220728|목|21|2|103|일반국도11호선|0|0|60.0|0|0|0|0|광양사거리|33.500730|126.529107|있음|KAL사거리|33.504811|126.526240|없음|30.0|
|TRAIN_0000002|20211010|일|7|2|103|일반국도16호선|0|0|80.0|0|0|0|0|창고천교|33.279145|126.368598|없음|상창육교|33.280072|126.362147|없음|61.0|
|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|
  
* test 데이터(.csv)  
-2022년 8월 데이터만 존재 (단, 날짜가 모두 연속적이지 않음)  
-291,241개의 데이터  

|id|base_date|day_of_week|base_hour|lane_count|road_rating|road_name|multi_linked|connect_code|maximum_speed_limit|vehicle_restricted|weight_restricted|height_restricted|road_type|start_node_name|start_latitude|start_longitude|start_turn_restricted|end_node_name|end_latitude|end_longitude|end_turn_restricted|
|------|---|---|---|---|---|---|---|---|---|---|---|---|---|---|----|---|---|---|---|---|---|
|TEST_000000|20220825|목|17|3|107|연삼로|0|0|70.0|0|0|0|0|산지2교|33.499427|126.541298|없음|제주은행사거리|33.500772|126.543837|있음|
|TEST_000001|20220809|화|12|2|103|일반국도12호선|0|0|70.0|0|0|0|3|중문입구|33.33.258507|126.427003|없음|관광단지입구|33.258119|126.415840|없음|
|TEST_000002|20220805|금|2|1|103|일반국도16호선|0|0|60.0|0|0|0|0|도순3교|33.258960|126.476508|없음|도순2교|33.259206|126.474687|없음|
|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|...|

</div>
</details>


<details>
<summary>활용 데이터 출처</summary>
<div markdown="1">       

* [DACON - 제주도 도로 교통량 예측 AI 경진대회](https://dacon.io/competitions/official/235985/data)

</div>
</details>

<details>
<summary>활용 변수</summary>
<div markdown="1">

|변수명|변수 설명|
|----|----------|
|id|아이디|
|base_date|날짜|
|day_of_week|요일|
|base_hour|시간대|
|road_in_use|도로사용여부|
|lane_count|차로수|
|road_rating|도로등급|
|multi_linked|중용구간 여부|
|connect_code|연결로 코드|
|maximum_speed_limit|최고속도제한|
|weight_restricted|통과제한하중|
|height_restricted|통과제한높이|
|road_type|도로유형|
|start_latitude|시작지점의 위도|
|start_longitude|시작지점의 경도|
|start_turn_restricted|시작 지점의 회전제한 유무|
|end_latitude|도착지점의 위도|
|end_longitude|도착지점의 경도|
|end_turn_restricted|도작지점의 회전제한 유무|
|road_name|도로명|
|start_node_name|시작지점명|
|end_node_name|도착지점명|
|vehicle_restricted|통과제한차량|
|target|평균속도(km)|


</div>
</details>
  
  
  
## 4. 자료 분석 과정  

### 분석 절차  
![제주_도로_교통량_분석_절차](https://user-images.githubusercontent.com/118996664/208552474-d2fc3348-3a73-4519-aec8-726edeeffec0.png)  

* 탐색적 데이터 분석(EDA)  
* 데이터 전처리: 결측치 처리, 파생변수 생성, 불필요한 컬럼 제거, 이상치처리, 범주형 변수 인코딩  
* 예측모델 개발: 세 가지 모형(XGBoost, LGBM, 랜덤포레스트+엑스트라트리 앙상블) 개발하여, MAE 값이 가장 낮은 최적의 모델 발굴  

#### [데이터 분석 및 예측모델 개발 코드.ipynb](https://github.com/jiazzang/Project_2022_jeju_traffic_volume_data_analysis/blob/main/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%A0%84%EC%B2%98%EB%A6%AC_%EB%B0%8F_%EB%AA%A8%EB%8D%B8_%EA%B0%9C%EB%B0%9C.ipynb) 
