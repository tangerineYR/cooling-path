# Architecture

[`app.py`](../app.py)는 Streamlit 진입점입니다. [`src/coolingpath/ui/app.py`](../src/coolingpath/ui/app.py)가 출발지·도착지 입력부터 경로 지도와 비교 지표 출력까지 연결합니다.

| 모듈 | 책임 |
| --- | --- |
| [`config.py`](../src/coolingpath/config.py) | 데이터 경로, 화면 설정, 캐릭터 정보 |
| [`data.py`](../src/coolingpath/data.py) | 선형 도로, 시간대별 그늘 통계, 그늘막 데이터 로딩 |
| [`services.py`](../src/coolingpath/services.py) | Kakao 장소 검색, Open-Meteo 기상 조회, 폭염·강수 데모 |
| [`routing.py`](../src/coolingpath/routing.py) | 최근접 노드 연결, 도로 그래프 구성, 경로 비용과 탐색 |
| [`metrics.py`](../src/coolingpath/metrics.py) | 거리·예상 시간·그늘 비율·그늘 보정 온도·경로 등급 계산 |
| [`ui/components.py`](../src/coolingpath/ui/components.py) | 경로 지도와 지표 카드, 캐릭터 표시 |

## Data Flow

[사전 공간 연산](qgis-workflow.md)에서 그림자 다각형과 도로 버퍼를 교차해 링크·시간대별 그늘 비율을 계산합니다. 앱은 선형 도로의 노드와 링크로 그래프를 만들고, 공개 CSV의 `shadow_ratio`를 링크 ID와 시간대로 결합합니다. 그래프의 도로 선형 형상은 지도 렌더링에 사용합니다. 장소 검색 좌표는 `EPSG:5179`로 변환한 후 KDTree로 최근접 도로 노드에 연결합니다.

경로 선은 보행 그래프의 도로 형상에서 가져오고, Folium은 브라우저에서 OpenStreetMap 타일을 배경으로 불러옵니다. Kakao Local API는 출발지와 도착지의 장소 검색에 사용합니다.

## Routing Policy

| 경로 | 선택 기준 |
| --- | --- |
| 최단 경로 | 도로 길이 `length`의 합을 최소화합니다. |
| 쿨링 경로 | 거리, 그늘 부족, 시설과 강수 조건을 반영한 `cost_cooling`의 합을 최소화합니다. |
| 대로 우선 경로 | 거리와 시설 조건으로 대로를 근사한 `cost_main`을 사용합니다. |
| 개인화 경로 | 사용자가 정한 최단거리 대비 우회율 안에서 `cost_personal`을 비교합니다. 비용순으로 최대 30개 후보를 확인하고 조건을 만족하는 후보가 없으면 최단 경로를 반환합니다. |

단순 무방향 그래프에서 같은 두 노드를 연결하는 링크가 여럿이면 가장 짧은 링크를 남깁니다. 길이가 같으면 문자열 `link_id` 순서로 결정합니다. 제외 링크는 `G.graph["omitted_parallel_links"]`에 남고, 앱 화면에도 이 제한을 표시합니다. 경로 최적화는 남아 있는 링크를 대상으로 수행합니다. `cost_walkability`는 보행 시설과 강수량을 반영하는 별도 비용이며 최단거리 계산은 `length`를 사용합니다.

[흐름도](diagrams/flowchart.mmd)와 [ERD](diagrams/erd.mmd)는 현장실습 당시의 설계 자료입니다. 흐름도의 예상 이동 시간 +14% 필터는 [당시 시뮬레이션](evaluation.md#internship-route-comparison)에 해당합니다. 현재 개인화 경로의 제약 기준은 거리입니다.