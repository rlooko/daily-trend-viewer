# 데일리 트렌드 뷰어 — 채널 레퍼런스판

유튜브 인기 영상·쇼츠·인스타 릴스·틱톡·X·스레드를 카테고리별 × 조회수순으로 매일 확인하는 웹사이트입니다.
한 화면에서 두 채널의 레퍼런스를 **카테고리 앞말로 구분**해 봅니다.

- **다은 님 채널 '이렇게 아름다은'** — 클래식 클린걸 · 자기관리 · 뷰티/올리브영/자라 룩북 · 바레/붓기 루틴
- **용은 님 채널 '수은집'** — 신혼·이사 리빙 + AI 부업 수익화

- 원본: [lmorejh/daily-trend-viewer](https://github.com/lmorejh/daily-trend-viewer) (API 키 없이, 파이썬 표준 라이브러리만 사용)
- 이 저장소: [rlooko/daily-trend-viewer](https://github.com/rlooko/daily-trend-viewer) — 원본을 포크해 카테고리·구독 계정만 바꾼 사본입니다.

## 🌐 웹 버전 (설치 불필요)

**https://rlooko.github.io/daily-trend-viewer/**

GitHub Actions(`.github/workflows/pages.yml`)가 매시 23분에 데이터를 수집해 GitHub Pages로 배포합니다.
웹 버전에서는 검색·새로고침·계정 추가/삭제가 꺼져 있고(정적 스냅샷), 계정 목록은 아래 `server.py` 기본값을 따릅니다.
전체 데이터(계정 즉시 추가, 검색, 좋아요순)를 보려면 로컬 실행을 쓰세요.

## 카테고리 (`server.py`)

| 카테고리 | 유튜브 검색어 |
| --- | --- |
| 다은:뷰티/올영 | 올리브영 · 뷰티 메이크업 |
| 다은:룩북/패션 | 룩북 · 데일리룩 |
| 다은:자기관리/바레 | 자기관리 · 바레 |
| 수은:신혼/리빙 | 신혼집 · 리빙 |
| 수은:AI/부업 | AI 부업 · 부업 |

'전체' 탭은 위 5개를 합쳐 조회수순으로 다시 정렬합니다(`ALL_MERGE`). 'AI 영상' 탭은 원본 기능 그대로 남아 있습니다.

검색어는 일부러 1~2단어로 짧게 둡니다. 긴 조합어(예: `뷰티 메이크업 패션`)는 유튜브 조회수순 + 업로드 기간 필터에서
결과가 0건으로 돌아왔습니다(2026-11-19 확인). 한 카테고리에 검색어는 리스트로 여러 개 넣을 수 있습니다.

## 구독 계정 (`server.py`)

- **인스타 릴스** — 차옳은 `itsoreun`, 응정 `_goodjung_` 외 뷰티·자기관리·룩북 계정 12개 (다은 님 레퍼런스)
- **틱톡** — `_goodjung_`
- **스레드** — `_goodjung_`
- **X** — `diet_prada`, `voguemagazine`, `allure`

수은집(신혼·리빙) 레퍼런스 계정은 로컬 실행 화면의 '계정 추가'로 넣으면 됩니다.
화면에서 추가·삭제한 내용은 `reels_accounts.json`·`tiktok_accounts.json` 등에 저장되지만, 그 파일들은 `.gitignore` 대상이라
**웹 버전에는 반영되지 않습니다.** 웹 버전 계정을 바꾸려면 `server.py`의 `DEFAULT_*_ACCOUNTS`를 고쳐서 커밋·푸시하세요.

## 그 밖에 바꾼 것

- `collect.py`의 `PAGES_BASE` → `https://rlooko.github.io/daily-trend-viewer/` (수집 실패 시 이전 스냅샷을 재사용하는 주소)
- `collect.py`의 `CATEGORY_SLUGS`, `static_shim.js`의 `SLUG`를 새 카테고리에 맞춰 수정 — **셋이 서로 달라지면 수집이 assert에서 멈춥니다.**
- 시드(`seed/`)의 기존 AI 릴스 데이터는 비웠습니다. 웹 버전 릴스는 아래 시드 갱신으로 채우면 됩니다.

## 카테고리·계정 바꾸는 법 (3곳을 같이)

1. `server.py` — `CATEGORIES`(카테고리명 → 검색어), `ALL_MERGE`, `DEFAULT_IG_ACCOUNTS` 등
2. `collect.py` — `CATEGORY_SLUGS` (카테고리명 → 영문 슬러그, 데이터 파일명에 쓰임)
3. `static_shim.js` — `SLUG` (collect.py와 반드시 동일)

```bash
python3 -c "import collect, server; assert set(collect.CATEGORY_SLUGS) == {'전체','AI', *server.CATEGORIES}; print('ok')"
```

## 실행 방법

```bash
python3 server.py
```

브라우저에서 **http://localhost:8778** 접속. 맥은 `실행-Mac.command`, 윈도우는 `실행-Windows.bat` 더블클릭으로도 됩니다.

### 릴스 시드 갱신 (웹 버전용)

인스타그램 미러(imginn)는 GitHub 서버 IP를 차단하므로, 웹 버전의 릴스는 집 PC에서 수집해 `seed/`에 커밋합니다.
`시드갱신-릴스.bat`(맥은 `python3 update_seed.py`)을 실행하면 수집 → 커밋 → 푸시까지 한 번에 됩니다.

## 참고

- 유튜브는 내부 검색 API(InnerTube)를 조회수순 정렬로 호출합니다(공개 급상승 피드는 2025년 폐지).
- 인스타 릴스는 미러(imginn.com) 경유, 조회수는 없어 좋아요순이 기본입니다.
- X는 nitter RSS + 임베드 CDN, 스레드는 GraphQL/RSSHub, 틱톡은 tikwm 공개 API를 씁니다. 제3자 공개 소스라 막히면 비어 있을 수 있습니다.
- 상세 기능(탭·정렬·팝업 재생) 설명은 원본 저장소 README를 참고하세요.
