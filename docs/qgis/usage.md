# QGIS Usage

저장소의 디렉터리 구조를 유지한 채 [`CoolingPath_QGIS.qgz`](CoolingPath_QGIS.qgz)를 QGIS에서 엽니다. 프로젝트는 같은 폴더의 GeoPackage와 [`data/processed/`](../../data/processed/)에 있는 건물·도로 Shapefile을 읽습니다. QGIS에서 지도를 보는 데 필요한 데이터는 저장소에 있으며, 앱이나 외부 API 연결 없이 열 수 있습니다.

![QGIS 인쇄 레이아웃의 보행 도로, 건물과 14시 그림자](screenshots/qgis-overview.png)

지도는 북쪽을 위로 표시하며 프로젝트 및 분석 레이어의 좌표계는 `EPSG:5179`입니다. 같은 이미지를 내보내려면 QGIS의 `Offline portfolio overview` 인쇄 레이아웃을 사용하세요.

## Map Layers

- 건물 형상과 보행 도로의 위치를 확인할 수 있습니다.
- 2025년 7월 8일 14:00 KST의 건물별 그림자를 표시합니다.
- 보행 도로 색상은 공개 CSV의 14:00 그늘 비율과 일치합니다.
- 도로 버퍼는 그림자와 교차 면적을 계산한 분석 영역을 보여줍니다.

공개 QGIS 프로젝트는 보행 링크 971개를 모두 표시합니다. 앱은 같은 두 노드를 잇는 도로 가운데 대표 링크를 선택해 경로를 계산합니다. 도로 색상에는 현장실습 방식으로 계산한 CSV의 값이 쓰이며, 전처리 명령의 기본 `union` 결과는 별도 파일로 생성됩니다. [레이어와 데이터 출처](layer-inventory.md) · [앱의 경로 탐색 기준](../architecture.md#routing-policy)

## Rebuild the Project

데비안·우분투에서 QGIS Python 바인딩을 설치한 환경을 기준으로 실행합니다. 먼저 저장소 루트에서 [그림자 전처리](../qgis-workflow.md#reproduce-the-preprocessing)를 실행해 `/tmp/coolingpath-run/shadows.gpkg`를 생성합니다.

빌더는 `docs/qgis/`에 기존 QGZ 또는 GeoPackage가 있으면 중단합니다. 다시 생성할 때는 별도 저장소 복사본에서 기존 QGZ·GeoPackage와 렌더링 이미지를 작업 폴더 밖으로 옮긴 뒤 아래 명령을 실행하세요.

~~~bash
QT_QPA_PLATFORM=offscreen /usr/bin/python3 scripts/qgis/build_portfolio.py \
  --shadows /tmp/coolingpath-run/shadows.gpkg

QT_QPA_PLATFORM=offscreen /usr/bin/python3 scripts/qgis/build_portfolio.py \
  --check docs/qgis/CoolingPath_QGIS.qgz
~~~

빌더는 공개 데이터의 건물 1,392개와 도로 971개를 기준으로 동작합니다. 입력 그림자는 2025년 7월 8일 시나리오에서 생성합니다.

## Validation

공개본 검증 기록에는 QGIS 3.40.6에서 프로젝트 개방, 인쇄 레이아웃 렌더링, 다른 위치로 옮긴 복사본의 네 레이어 로딩을 확인한 결과가 남아 있습니다. 자동화 테스트는 QGIS 없이 QGZ의 상대경로·CRS·데이터 제공자와 GeoPackage의 무결성·피처 수·공개 CSV와의 일치를 검사합니다. [QGIS 테스트](../../tests/test_qgis_project.py)