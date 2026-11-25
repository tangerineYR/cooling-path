# QGIS & Shadow Preprocessing

강남구 역삼–선릉 일대의 건물과 도로를 바탕으로 시간대별 건물 그림자를 생성하고, 보행 링크별 그늘 비율을 집계합니다. 공개 파이프라인의 입력은 가공 건물과 도로 버퍼입니다.

## Spatial Inputs

QGIS에서는 분석 영역을 정하고 건물과 도로를 정제했습니다. 건물 높이가 빠졌거나 0인 구간은 층수 기반 추정과 일부 건물의 수동 보정으로 보완했습니다. 보행 도로는 면적 교차에 사용할 수 있도록 선형 형상 양쪽에 2m 버퍼를 적용했습니다. [데이터 파일과 공개 범위](../data/README.md)

현장실습 QGIS 프로젝트의 당시 기록에는 레이어 24개와 프로젝트 CRS `EPSG:5186`이 남아 있습니다. 거리·면적 계산에 사용한 주요 분석 레이어와 현재 [공개 QGIS 프로젝트](qgis/usage.md)는 `EPSG:5179`를 사용합니다. 당시 자료에는 `EPSG:4326`·`EPSG:5186` 원천 레이어와 `EPSG:3857`의 OSM 배경지도가 함께 있었습니다. 자세한 레이어 구성은 [QGIS Layer Inventory](qgis/layer-inventory.md)에 정리했습니다.

현장실습 자료에는 QGIS 도구의 전체 실행 순서와 개별 수동 편집 내역이 기록되어 있지 않습니다. 재현 절차는 저장소에 있는 가공 SHP부터 시작합니다.

## Shadow Pipeline

1. 강남구 기준 좌표(위도 37.5026°, 경도 127.0427°)에서 2025년 7월 8일 08:00–19:00 KST의 태양 고도와 방위각을 계산합니다.
2. 건물 형상을 `EPSG:5179`로 변환하고 허용 오차 0.3m로 단순화합니다.
3. 건물 높이와 태양 고도로 이동 거리를 계산합니다. 태양 반대 방향을 따라 원래 위치를 포함한 8개 형상을 합집합해 건물별 그림자를 근사합니다.
4. 시간대별 그림자와 보행 도로 버퍼가 겹치는 부분을 계산합니다. 링크별 그늘 면적을 도로 버퍼 면적으로 나눠 `shadow_ratio`를 0–1 범위로 저장합니다.

[`shadow_generation.ipynb`](../notebooks/shadow_generation.ipynb)는 현장실습 당시 그림자 전처리 셀을 보존한 기록입니다. 당시 Colab 경로를 사용하므로, 공개 데이터로 실행할 때는 아래 명령을 사용하세요.

## Reproduce the Preprocessing

저장소 루트에서 Python 3.11 이상으로 실행합니다. `preprocessing` 추가 의존성은 태양 위치 계산에 필요한 Pysolar를 설치합니다. 패키지 설치 후 세 명령은 외부 API 없이 실행됩니다. 아래는 Bash 예시입니다.

~~~bash
python -m pip install -e ".[preprocessing]"

python -m scripts.preprocessing.generate_solar_scenarios \
  --date 2025-07-08 --output /tmp/coolingpath-run/solar.csv

python -m scripts.preprocessing.generate_shadows \
  --buildings data/processed/processed_buildings.shp \
  --solar /tmp/coolingpath-run/solar.csv \
  --output /tmp/coolingpath-run/shadows.gpkg

python -m scripts.preprocessing.aggregate_shadow_by_link \
  --roads data/processed/processed_roads.shp \
  --shadows /tmp/coolingpath-run/shadows.gpkg \
  --overlap-policy union --output /tmp/coolingpath-run/links.csv
~~~

출력 파일이 이미 있으면 스크립트가 중단됩니다. 다시 실행할 때는 새 출력 경로를 지정하세요. 생성된 결과는 원본 SHP와 앱용 CSV와 별도로 저장됩니다. `--height-column`, `--steps`, `--simplify`로 높이 열, 이동 구간 수, 단순화 허용 오차를 바꿀 수 있습니다. 입력 다각형은 유효한 형상과 CRS를 갖춰야 합니다.

### Overlap Policies

| 정책 | 집계 방식 |
| --- | --- |
| `union` | 같은 시간대에 도로와 교차하는 건물 그림자를 합집합한 후 면적을 계산합니다. 여러 건물의 그림자가 겹친 부분은 한 번 집계합니다. |
| `legacy-sum` | 현장실습 노트북과 같이 건물별 교차 면적을 합산하고 비율을 최대 1로 제한합니다. 상한 적용 뒤에도 중복 면적의 영향이 남을 수 있습니다. |

공개 CSV와 비교하려면 `legacy-sum` 정책으로 별도 파일을 생성합니다.

~~~bash
python -m scripts.preprocessing.aggregate_shadow_by_link \
  --roads data/processed/processed_roads.shp \
  --shadows /tmp/coolingpath-run/shadows.gpkg \
  --overlap-policy legacy-sum --output /tmp/coolingpath-run/legacy-links.csv
~~~

앱과 공개 QGIS 프로젝트의 보행 도로 색상은 저장소의 [`hourly_link_stat_20250708.csv`](../data/processed/hourly_link_stat_20250708.csv)를 사용합니다. 이 CSV는 현장실습 방식으로 집계한 결과입니다.

## Reproduction Checks

공개 가공 데이터의 전처리 기록에는 건물 1,392개, 시간대별 그림자 16,704개, 도로 971개와 12시간에 대한 통계 11,652행이 남아 있습니다. 공개 CSV와 `legacy-sum` 결과의 최대 절대 오차는 약 `1.12e-15`였습니다. `union`의 그늘 비율은 `legacy-sum`보다 2,626개 링크·시간대에서 낮았습니다(차이 `1e-6` 초과 기준).

이 비교는 가공 데이터에서의 집계 재현성을 다룹니다. 건물 그림자를 유한한 간격으로 근사하므로 태양 고도가 낮거나 건물 폭에 비해 이동 간격이 크면 그림자 사이에 틈이 생길 수 있습니다. 현장 그림자 형상과의 오차는 측정하지 않았습니다. 자동화 테스트 설치·실행은 [Tests & CI](../README.md#tests--ci)를 참고하세요.