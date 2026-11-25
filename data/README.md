# Data

분석 범위는 서울특별시 강남구 역삼–선릉 일대입니다. 원천 데이터 수집일은 2026년 1월 5일이며, 그림자 시뮬레이션은 2025년 7월 8일 08:00–19:00 KST의 태양 위치를 사용합니다.

| 경로 | 데이터 | 활용 |
| --- | --- | --- |
| `raw/seoul_people_roads.csv` | 서울시 보행 네트워크 원천 자료 | 보행 링크 가공 |
| `processed/non_buffered_roads.*` | 선형 보행 도로, 노드 연결 정보 | 앱의 보행 그래프, QGIS 보행 도로 표시 |
| `processed/hourly_link_stat_20250708.csv` | 링크·시간대별 그늘 비율 11,652행 | 앱의 경로 비용·지표, QGIS 도로 색상 |
| `processed/gangnamgu_shade_shelters.csv` | 그늘막 위치 | 앱 지도 마커 |
| `processed/processed_roads.*` | 도로 양쪽에 2m 버퍼를 적용한 면형 자료 | 그림자 교차·집계 |
| `processed/processed_buildings.*` | 형상·높이를 가공한 건물 정보 | 그림자 생성과 QGIS 건물 레이어 |
| `processed/solar_scenario_20250708.csv` | 시간대별 태양 고도·방위각 | 그림자 방향·길이 계산 |

앱은 기본적으로 `data/processed/`에서 가공 데이터를 읽습니다. 다른 디렉터리를 사용하려면 `COOLINGPATH_DATA_DIR`를 설정합니다. Shapefile을 이동할 때는 같은 이름의 `SHP`, `SHX`, `DBF`, `PRJ` 파일을 함께 유지하세요.

## Derived Data

앱의 그늘 통계는 현장실습 노트북의 면적 합산 방식으로 집계한 값입니다. 전체 시간대의 건물 그림자 다각형은 공개 파일 범위에서 제외되어 있습니다. 시간대별 그림자와 그늘 통계는 공개 가공 건물·도로를 사용해 다시 만들 수 있습니다. 생성 옵션과 앱용 CSV의 차이는 [QGIS & Shadow Preprocessing](../docs/qgis-workflow.md)을 참고하세요.

## Data Provenance

공개 자료는 원천 보행 네트워크와 분석용 가공 파일로 구성됩니다. 원본 행정경계 SHP와 초기 기상 분석에 사용한 방재기상관측(AWS) CSV는 현재 공개 범위에 포함되지 않습니다. 데이터셋별 제공처와 용도는 [Data Provenance](../README.md#data-provenance)에 있습니다. 이용 조건은 각 데이터 제공처와 [NOTICE](../NOTICE.md)를 참고하세요.