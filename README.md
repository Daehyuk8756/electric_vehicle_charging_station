# electric_vehicle_charging_station
*******README******.  
In Korean.  
서울시내의 전기차 충전소의 최적 위치선정을 위한 코드입니다. 
실행순서는 BS_Station_location > CS_Station_location > final_model/buffer_binary_final

1. 사용한 데이터 : 서울시 전기차 충전소 리스트, 서울시 버스정류장 위치정보, 서울시 지도 shp
전기차 충전소 리스트 : 위치정보가 주소로만 저장되어 있음
버스 정류장 위치정보 : 좌표계로 변환되어 있음
  
2. 데이터 전처리
전기차 충전소 리스트 : geopandas를 통하여 버스정류장 위치 정보를 좌표계로 변환
전기차 충선소와 버스 정류장의 위치정보를 서울시 지도에 mapping
QGIS를 이용하여 서울시를 100M단위로 그리드 분할
mapping한 위치정보의 coverage를 500M로 설정하고,  해당 포인트의 반지름이 500M인 버퍼를 생성
그리드에 포함된 버퍼들을 count.

3. 머신러닝 모델링 : 입지선정지수 개발
-그리드에맵핑된 전기차 충전소의 buffer의 수를 counting 해봤을 때, Midean 값이 2였고, 이를 통해 2개 이상 커버하고 있는 그리드는 1로, 그렇지 않은 그리드는 0으로 하는 새로운 Target Label을 만들어 BinaryClassification model을 구현
   - ML 모델은 SGD(확률적 경사하강모델), RandomForest, XGBoost 를 사용하였고, 모델의 roc auc score는 각각 0.68, 0.70, 0.70으로 괜찮은 성능을 보임
   - 성능과 처리속도가 가장 좋았던 RandomForest를 최종 모델로 선택하고, predict_prob()를 통해 해당 그리드를 포함할 확률를 구함
   - 이를 입지선정지수(가중치)로 사용

4. 위치선정문제 개발
   ㅇ MCLP 모델을 통해 최적 위치선정 문제 개발
지역 : 압구정동
그리드 수 : 264개 
coverage : 500M
스테이션 개수 : 10개
두 점 사이의 거리는 각 그리드의 center를 포인트로 반환하고 두 점사이의 유클리드 거리를 구하여 distance_matrix를 구현
압구정 동에 대해서는 이런 결과값이 나왔으나, 다른 지역을 인풋으로 넣으면 해당 지역의 최적 스테이션 위치가 아웃풋으로 나옴.

In English.  
This code is for selecting the optimal location of electric vehicle charging stations in Seoul. 
The order of execution is... 
BS_Station_location > CS_Station_location >final_model/buffer_binary_final

1. Data used: list of electric vehicle charging stations in Seoul, location information of bus stops in Seoul, and map of Seoul. shp
Electric vehicle charging station list: Location information is stored only as an address.
Bus stop location information: converted into a coordinate system.

2. Data preprocessing.
Electric vehicle charging station list: Converting bus stop location information into a coordinate system through geopandas.
Map the location information of the electric vehicle charging station and bus stop to the map of Seoul.
Using QGIS, Seoul is divided into grids in units of 100M.
The coverage of the mapped location information is set to 500M, and a buffer with a radius of 500M is generated.
Count the buffers included in the grid.

3. Machine learning modeling: Location selection index development
-When counting the number of buffers of electric vehicle charging stations mapped to the grid, the Midan value was 2, through which a grid covering more than two grids was 1, and a grid that did not was 0 was created to implement a binary classification model.
   - ML models used SGD (Probability Slope Down Model), RandomForest, and XGBoost, and the rock core of the model showed good performance at 0.68, 0.70 and 0.70 respectively.
   - RandomForest, which had the best performance and processing speed, was selected as the final model, and the probability of including the grid was obtained through the present_prob().
   - Use this as the location selection index (weighted)

4. Developing a location selection problem.
Developing the optimal location selection problem through the MCLP model.
Region: Apgujeong-dong.
Number of grids: 264. 
coverage : 500M
Number of stations: 10.
The distance between the two points returns the center of each grid as a point and calculates the Euclidean distance between the two points to implement distance_matrix.
These results came out for Apgujeong-dong, but if you input another area, the optimal station location in the area comes output.
