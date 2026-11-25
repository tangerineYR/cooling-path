# QGIS Layer Inventory

공개 QGIS 프로젝트와 네 레이어의 좌표계는 `EPSG:5179`입니다. 파일 경로는 [`CoolingPath_QGIS.qgz`](CoolingPath_QGIS.qgz)의 위치를 기준으로 표시합니다.

| QGIS 표시 이름 | 데이터 경로·테이블 | 피처 수 | 용도 |
| --- | --- | ---: | --- |
| Walking links &#124; shade at 14:00 | `./CoolingPath_showcase.gpkg` · `roads_shade_20250708_1400` | 971 | 공개 CSV의 14:00 그늘 비율을 입힌 보행 도로 |
| `Buildings` | `../../data/processed/processed_buildings.shp` | 1,392 | 건물 형상과 높이 |
| Shadows &#124; 14:00 KST | `./CoolingPath_showcase.gpkg` · `shadows_20250708_1400` | 1,392 | 공개 전처리 코드로 생성한 14:00 건물 그림자 |
| Road buffers &#124; 2 m | `../../data/processed/processed_roads.shp` | 971 | 그림자 교차 면적 계산에 사용한 2m 도로 버퍼 |

GeoPackage에는 보행 도로와 14:00 그림자가 들어 있습니다. 건물과 도로 버퍼는 저장소의 Shapefile을 참조합니다. GeoPackage에는 레이어 표시에 필요한 ID, 길이, 시간, 그늘 비율 등의 속성을 사용합니다.

## Internship Data Lineage

현장실습 QGIS 프로젝트에는 24개 레이어가 기록되어 있으며 프로젝트 CRS는 `EPSG:5186`이었습니다. 공개 프로젝트는 아래 자료에서 분석에 필요한 형상과 통계를 연결합니다.

| 현장실습 레이어 | 공개 프로젝트에서의 대응 |
| --- | --- |
| `processed_buildings`, `processed_buildings2` | 같은 건물 데이터를 `Buildings` 레이어로 표시 |
| `processed_roads` | 공개 가공 도로를 `Road buffers` 레이어로 표시 |
| `non_buffered_roads2` | 공개 선형 도로와 링크별 그늘 CSV로 `Walking links` 레이어 생성 |
| `processed_shadows` | 공개 가공 건물과 태양 위치로 14:00 `Shadows` 레이어 생성 |
| `Roads_Teheran_5179` | 이름과 달리 건물 파일을 참조한 원본 레이어로, 공개 범위에서 제외 |
| `Shadow_KT_Fixed_5179`, `Buildings_Teheran_5179` | 현장실습 당시 수동 편집 자료로, 공개 범위에서 제외 |
| `Roads_Buffered_2m`, `Roads_Clean_5179`, `Roads_Buffered` | 중간 도로 자료 대신 좌표계가 정의된 공개 도로 버퍼 사용. 당시 `Roads_Buffered` CSV의 CRS 표기는 비어 있었음 |
| `Roads_Shadow_Parts`, `Roads_Shadowed_Only` | 중간 교차 자료. 동일한 데이터 유형은 [전처리 절차](../qgis-workflow.md)에서 생성 |
| `Shadow_2026_0708_1400`, `Shadow_Real_2026` | 명칭과 날짜가 공개 2025년 시나리오와 다른 외부 참조로, 공개 범위에서 제외 |
| `Teheran_Block_Buildings`, `Teheran_Roads_Network` | 외부 건물·도로 자료로, 공개 가공 레이어의 범위와 구분 |
| `AL_D010_11_20260104`, `BND_SIGUNGU_PG`, `fixed_AL_D010`, `gangnam_str`, `gangnamgu` | 원천·행정경계·보정 자료로, 공개 레이어에 포함되지 않음 |
| `서울시 자치구별 도보 네트워크 공간정보` | 가공된 보행 네트워크 사용 |
| `OpenStreetMap` | 오프라인 프로젝트의 공개 레이어에 포함되지 않음 |

원천과 가공 데이터의 출처 및 이용 조건은 [Data Provenance](../../data/README.md#data-provenance)를 참고하세요.