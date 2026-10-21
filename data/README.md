# Data

| 경로 | 내용 |
| --- | --- |
| `raw/seoul_people_roads.csv` | 기존 저장소의 원천 보행 네트워크 자료 |
| `processed/non_buffered_roads.*` | 앱이 사용하는 선형 도로와 노드 연결 정보 |
| `processed/hourly_link_stat_20250708.csv` | 링크·시간대별 그늘 비율 |
| `processed/gangnamgu_shade_shelters.csv` | 그늘막 위치 자료 |
| `processed/processed_roads.*` | 그늘 교차 분석용 버퍼 도로 |
| `processed/processed_buildings.*` | 가공 건물 공간정보 |
| `processed/solar_scenario_20250708.csv` | 태양 위치 시나리오 |

SHP·SHX·DBF·PRJ는 함께 유지합니다. 앱의 기본 데이터 경로는 저장소 기준으로 결정되며 `COOLINGPATH_DATA_DIR` 환경변수로 가공 데이터 경로를 변경할 수 있습니다.

그림자 형상 저장을 위한 ERD는 `docs/diagrams/erd.mmd`에 있습니다.

실제 프로젝트 당시 생성된 전체 Shadow Polygon 원본은 저장소에 포함하지 않습니다.

대신 `scripts/preprocessing/`의 재현 스크립트를 이용해
`processed_buildings.shp`, Solar Scenario, Road Buffer를 기준으로
시간대별 Shadow Geometry와 Link-level Shadow Statistics를 다시 생성할 수 있습니다.

데이터는 기존 저장소에 공개된 파일을 재배치했습니다. 원천 데이터의 출처·이용 조건은 코드와 별도로 적용됩니다.
