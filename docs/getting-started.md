# Getting Started

Python 3.11 이상과 Kakao Local REST API 키가 필요합니다. 아래 명령은 저장소 루트에서 실행합니다.

~~~bash
git clone https://github.com/tangerineYR/cooling-path.git
cd cooling-path
python -m venv .venv
~~~

~~~powershell
# Windows PowerShell
.\.venv\Scripts\Activate.ps1
~~~

~~~bash
# macOS / Linux
source .venv/bin/activate
~~~

~~~bash
python -m pip install -e .
~~~

## Configure the Kakao Key

환경변수 `KAKAO_API_KEY`를 설정합니다.

~~~powershell
# Windows PowerShell
$env:KAKAO_API_KEY = "YOUR_KAKAO_REST_API_KEY"
~~~

~~~bash
# macOS / Linux
export KAKAO_API_KEY="YOUR_KAKAO_REST_API_KEY"
~~~

Streamlit 설정을 쓰려면 [설정 예시](../.streamlit/secrets.toml.example)를 `.streamlit/secrets.toml`로 복사한 후 같은 키를 입력합니다. `.env.example`은 환경변수 이름을 확인할 때 사용합니다. 앱은 `.env`를 자동으로 읽지 않습니다.

## Run the App

~~~bash
python -m streamlit run app.py
~~~

[http://localhost:8501](http://localhost:8501)에서 출발지·도착지와 시간대를 선택합니다. 폭염·강수 데모는 미리 정의된 기상값을 사용합니다. 장소 검색은 데모에서도 Kakao REST API 키를 사용합니다. 지도 배경은 브라우저에서 OpenStreetMap 타일을 불러옵니다. 두 기능 모두 인터넷 연결이 필요합니다.

장소 검색에 실패하면 Kakao 키 설정과 네트워크 연결을 확인합니다. 경로는 보이는데 지도 배경만 비어 있다면 브라우저에서 OpenStreetMap 타일 요청이 차단됐는지 확인합니다.

<details>
<summary>Docker 실행</summary>

호스트 셸에서 `KAKAO_API_KEY`를 설정한 뒤 실행합니다.

~~~bash
docker build -t coolingpath .
docker run --rm -p 8501:8501 -e KAKAO_API_KEY coolingpath
~~~

</details>