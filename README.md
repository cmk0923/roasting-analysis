# 🔥 로스팅 데이터 분석

커피 로스팅 데이터를 시각화하고 분석하는 정적 웹 애플리케이션입니다. GitHub Pages에 호스팅할 수 있습니다.

## 📁 프로젝트 구조

```
roasting-analysis/
├── docs/                       # GitHub Pages에 배포할 파일
│   ├── index.html              # 메인 페이지 (로스팅 목록)
│   ├── dashboard.html          # 로스팅 데이터 대시보드
│   ├── .nojekyll               # GitHub Pages 설정 (Jekyll 비활성화)
│   └── roastingData/           # JSON 데이터 폴더
│       ├── 105/
│       │   ├── history_105_chart.json
│       │   └── history_105_entry.json
│       └── 106/
│           ├── history_106_chart.json
│           └── history_106_entry.json
├── local-server/               # 로컬 데이터 저장용 (git에 포함 안됨)
│   ├── server.py               # Flask 서버
│   ├── roast_fetch.html        # 데이터 다운로드 페이지
│   └── websocket_fetch.py      # WebSocket 연결 모듈
├── .gitignore                  # git 무시 파일 설정
└── README.md                   # 이 파일
```

## 🚀 GitHub Pages에 배포하기

### 1. 로컬에서 데이터 저장

```bash
cd local-server
pip install flask websocket-client
python server.py
```

서버가 시작되면 `http://localhost:5001/savedata`에서 데이터를 다운로드합니다.

### 2. GitHub에 푸시

```bash
cd ..
git add docs/
git commit -m "Add new roasting data"
git push origin main
```

### 3. GitHub Pages 활성화

1. 저장소의 **Settings** → **Pages**로 이동
2. **Source**를 `Deploy from a branch`로 설정
3. **Branch**를 `main` / `(root)`로 선택 후 **Save**

### 4. 사이트 접속

`https://YOUR_USERNAME.github.io/roasting-analysis/docs/` 에서 사이트를 확인할 수 있습니다.

## 📊 데이터 저장 및 업데이트 흐름

```
1. 로컬 서버 실행 (local-server/server.py)
   ↓
2. roast_fetch.html에서 데이터 다운로드
   ↓
3. docs/roastingData/{id}/ 폴더에 JSON 파일 저장
   ↓
4. docs/index.html의 staticFolders 배열 자동 업데이트
   ↓
5. git add docs/ && git commit && git push
   ↓
6. GitHub Pages 자동 배포
```

## 📝 새로운 로스팅 데이터 추가하기

### 자동 방법 (권장)

1. `local-server/server.py` 실행
2. `http://localhost:5001/savedata` 접속
3. 히스토리 ID 입력 후 다운로드
4. `docs/index.html`이 자동으로 업데이트됨
5. git에 푸시

### 수동 방법

1. `docs/roastingData/{id}/` 폴더에 JSON 파일 생성
2. [`docs/index.html`](docs/index.html)의 `staticFolders` 배열에 추가:

```javascript
const staticFolders = [
    { id: '105', name: '로스팅 #105' },
    { id: '106', name: '코스타리카 SHB San Rafael' },
    { id: '107', name: '에티오피아 Yirgacheffe' }  // 새로 추가
];
```

## 💬 Giscus 댓글 기능 설정하기

Giscus는 GitHub Discussions를 사용한 댓글 시스템입니다.

### 1. Giscus 설정

https://giscus.app 에 접속하여 다음을 설정합니다:

1. **Repository**: 댓글을 사용할 저장소 선택
2. **Page ↔️ Discussions mapping**: `pathname` (각 페이지별로 별도 댓글)
3. **Discussion title**: `og:title` - page title
4. **Theme**: `dark_dimmed` (다크 테마)
5. **Language**: `Korean`

### 2. 설정 값 복사

생성된 스크립트에서 다음 값을 복사합니다:
- `data-repo`: `[YOUR_GITHUB_USERNAME]/[REPO_NAME]`
- `data-repo-id`: `[REPO_ID]`
- `data-category`: `[CATEGORY_NAME]`
- `data-category-id`: `[CATEGORY_ID]`

### 3. dashboard.html에 적용

[`docs/dashboard.html`](docs/dashboard.html)의 Giscus 스크립트 부분을 수정합니다:

```html
<script src="https://giscus.app/client.js"
    data-repo="YOUR_USERNAME/roasting-analysis"
    data-repo-id="R_kgDO..."
    data-category="Announcements"
    data-category-id="DIC_kwDO..."
    data-mapping="pathname"
    data-strict="0"
    data-reactions-enabled="1"
    data-emit-metadata="0"
    data-input-position="bottom"
    data-theme="dark_dimmed"
    data-lang="ko"
    crossorigin="anonymous"
    async>
</script>
```

### 4. Discussions 활성화

저장소의 **Settings** → **Features** → **Discussions**를 활성화합니다.

## 🎨 기능

- 로스팅 데이터 시각화 (BT, ET, RoR 차트)
- 화력 및 팬 제어 그래프
- 이벤트 로그 (TP, FC, DE 등)
- 로스팅 레벨 자동 계산
- Giscus 댓글 기능

## 🛠️ 로컬 서버 API

| 엔드포인트 | 설명 |
|-----------|------|
| `GET /` | 메인 페이지 (docs/index.html) |
| `GET /dashboard.html` | 대시보드 (docs/dashboard.html) |
| `GET /savedata` | 데이터 다운로드 페이지 |
| `GET /api/folders` | 폴더 목록 반환 |
| `GET /api/data/<folder_name>` | 차트 데이터 반환 |
| `GET /api/entry/<folder_name>` | 엔트리 데이터 반환 |
| `GET /api/download/<history_id>` | 로스터에서 데이터 다운로드 |

## 📄 JSON 파일 형식

**chart.json 예시:**
```json
{
  "data": [
    {
      "beanTemp": 156,
      "burner": 80,
      "exhaustAirTemp": 174.5,
      "rateOfRise": 18,
      "timestamp": 0
    },
    ...
  ]
}
```

**entry.json 예시:**
```json
{
  "data": {
    "creationDate": "1768638795",
    "endTemp": 174.25,
    "endWeight": 0.173,
    "fillTemp": 157.5,
    "fillWeight": 0.2,
    "name": "코스타리카 SHB San Rafael",
    "roastingTime": 644
  }
}
```

## 📝 주의사항

- `local-server/` 폴더는 git에 포함되지 않습니다 (`.gitignore` 설정)
- `docs/roastingData/` 폴더만 git에 푸시됩니다
- GitHub Pages는 무료로 호스팅할 수 있습니다

## 📄 라이선스

MIT License