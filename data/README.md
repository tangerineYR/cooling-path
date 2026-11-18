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

## Data Provenance

- **원천 데이터 수집일:** 2026.01.05
- **그림자 시뮬레이션 기준일:** 2025.07.08
- **분석 지역:** 서울특별시 강남구 역삼–선릉 일대
- **공개 범위:** 경로 탐색용 보행 네트워크 및 주요 가공 공간정보, 시간대별 그늘 통계
- **미포함 자료:** 원본 행정경계 SHP, 초기 AWS 관측 CSV, 현장실습 당시 생성한 전체 Shadow Polygon

원천 데이터셋의 공식 명칭, 상세 출처 및 활용 목적은 [README의 Data Provenance](../README.md#data-provenance)를 참고하세요.

현재 공개된 데이터는 현장실습 과정에서 가공한 공간정보를 포함하며, 원천자료 전체를 그대로 복제한 것이 아닙니다. 가공 파일을 재사용하거나 배포할 때에는 해당 원천 데이터의 이용 조건을 별도로 확인해야 합니다.
