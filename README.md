# TC_View_Exporter Streamlit App

이 애플리케이션은 Trimble Connect API를 사용하여 특정 모델의 부모 폴더 내에 있는 도면 파일(.dwg)의 위치(Alignment)를 자동으로 업데이트하는 웹 애플리케이션입니다.  
파일명에서 층 정보를 추출하고, CSV 파일에 정의된 층별 z 좌표 매핑을 기반으로 지정된 x, y 좌표와 결합하여 도면의 위치를 업데이트합니다.

## 주요 기능

- **OAuth 2.0 인증**  
  Trimble Identity의 OAuth 2.0 Authorization Code Grant Flow를 통해 access token 및 refresh token을 획득하고, 토큰을 파일에 저장하여 재사용합니다.  
  (토큰 만료 임박 시 자동으로 refresh token을 사용하여 갱신)

- **모델 및 폴더 조회**  
  사용자가 입력한 모델 ID를 기반으로 해당 모델의 상세정보를 조회하고, 부모 폴더 ID를 추출합니다.  
  부모 폴더 내의 도면 파일 목록(.dwg)을 불러와 목록으로 표시합니다.

- **층별 z 좌표 매핑**  
  CSV 파일(예: `층정리.csv`)에서 "층번호"와 "레벨" 정보를 읽어, 각 층에 대응하는 z 좌표 값을 매핑합니다.  
  파일명에서 층 정보를 자동으로 추출하여, CSV 매핑에 따라 올바른 z 좌표를 결정합니다.

- **도면 위치 업데이트**  
  사용자가 지정한 x, y 좌표와 추출된 z 값을 이용하여, 각 도면 파일의 위치(Alignment)를 PUT 방식으로 업데이트합니다.

## 요구 사항

- Python 3.7 이상
- [Streamlit](https://streamlit.io/)
- [python-dotenv](https://github.com/theskumar/python-dotenv)
- 기타: requests, csv, re 등 (requirements.txt 파일 참조)

## 설치 및 설정

1. **레포지토리 클론 및 가상환경 설정**

   ```bash
   git clone <레포지토리 URL>
   cd <프로젝트 폴더>
   python -m venv venv
   # Windows
   venv\Scripts\activate
   # macOS/Linux
   source venv/bin/activate
   ```

2. **필수 패키지 설치**

   ```bash
   pip install -r requirements.txt
   ```

   *예시 requirements.txt:*
   ```
   streamlit
   python-dotenv
   requests
   ```

3. **환경 변수 설정 (.env 파일 생성)**  
   프로젝트 루트에 `.env` 파일을 생성하고 다음 정보를 입력하세요.

   ```env
   CLIENT_ID=MY_CLIENT_ID
   CLIENT_SECRET=MY_CLIENT_SECRET
   CALLBACK_URL=https://oauth.pstmn.io/v1/callback
   AUTH_URL=https://id.trimble.com/oauth/authorize
   TOKEN_URL=https://id.trimble.com/oauth/token
   SCOPE=openid MY_APP_NAME
   ```

4. **층 정보 CSV 파일 준비**  
   프로젝트 루트에 `층정리.csv` 파일을 준비합니다. CSV 파일은 헤더로 **"층번호"**와 **"레벨"** 열을 포함해야 합니다.  
   예를 들어:
   ```
   층번호,레벨
   ...
   -1,-30000
   1,27500
   2,33900
   3,37800
   ...
   ```

## 실행 방법

1. **OAuth 인증**  
   - 애플리케이션 화면에 리디렉션 URL을 입력하는 칸이 나타납니다.  
   - 안내된 URL로 이동하여 로그인 후, 브라우저의 리디렉션 URL(authorization code 포함)을 복사해 입력합니다.

2. **모델 ID 입력**  
   - 모델 ID를 입력하면 해당 모델의 부모 폴더 ID가 조회됩니다.

3. **도면 파일 목록 확인**  
   - 부모 폴더 내의 도면 파일(.dwg) 목록이 화면에 출력됩니다.

4. **좌표 입력 및 업데이트 실행**  
   - 적용할 x, y 좌표를 입력하고 “위치 업데이트 실행” 버튼을 클릭하면, 각 도면 파일의 파일명에서 층 정보를 추출하여 CSV 매핑에 따른 z 값을 적용해 PUT 방식으로 위치 업데이트를 진행합니다.

## 참고 사항

- **토큰 관리**  
  토큰은 `token.json` 파일에 저장되며, 만료 60초 전이면 자동으로 refresh token을 사용하여 갱신됩니다.

- **에러 처리**  
  각 API 호출 시 에러 메시지가 Streamlit 화면에 출력됩니다.

- **파일 업데이트**  
  도면 파일의 위치 업데이트는 PUT 방식으로 진행되며, 기존 위치 정보가 있더라도 업데이트합니다.
