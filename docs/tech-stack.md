# 기술 스택 및 결정 근거

## 최종 기술 스택

| 영역 | 기술 | 버전 기준 |
|------|------|-----------|
| 손님용 앱 | Kotlin + Android SDK | Android 10+ |
| POS 앱 | Python + pywebview + WebView2 | Python 3.12 |
| 백엔드 | Python FastAPI + uvicorn | - |
| 실시간 통신 | WebSocket (FastAPI 내장) | - |
| DB | PostgreSQL (운영) / SQLite (초기) | - |
| 프린트 서버 | Python + python-escpos | - |
| 관리자 페이지 | HTML/CSS/JS (서버 내장) | - |

## 결정 근거

### 손님용 앱: Kotlin 네이티브

**선택 이유:**
- 키오스크 모드(COSU) 잠금은 Android 시스템 레벨 제어가 필요
- 홈 버튼, 상태바, 뒤로 가기 차단은 네이티브에서만 안정적
- 태블릿에 고정 설치되는 앱이므로 크로스 플랫폼(React Native 등) 이점이 없음
- 바코드 리더기 등 하드웨어 연동 확장성

**기각된 대안:**
- React Native: 키오스크 모드 구현이 네이티브 브릿지 의존적이라 불안정
- 웹앱(PWA): 키오스크 잠금, 시스템 UI 차단 불가능
- Flutter: Dart 학습 비용 추가, 키오스크 모드 생태계 부족

### POS 앱: pywebview

**선택 이유:**
- Slink v3.2.2에서 검증된 아키텍처 (pywebview + 로컬 HTTP API + pystray)
- Windows 환경에서 프린터 제어(USB)를 Python에서 직접 처리 가능
- HTML/CSS/JS로 UI 구현 → 디자인 자유도 높음
- 기존 POS PC(Windows)에서 바로 실행 가능

**Slink에서 확립된 핵심 규칙 (반드시 준수):**
1. pywebview의 js_api/expose/events 절대 사용 금지 (WinForms AccessibilityObject 재귀 버그)
2. JS↔Python 통신은 fetch() → HTTP API만 사용
3. body 없는 호출은 GET으로 전송 (API 함수 설계 주의)
4. 창 제어(hide/show/minimize/quit)는 cmd_queue → webview.start(func, window)의 func 내 루프에서 실행
5. pystray 콜백도 같은 cmd_queue 경유

### 백엔드: FastAPI

**선택 이유:**
- Python 기반으로 POS 앱, 프린트 서버와 언어 통일
- 내장 WebSocket 지원
- 비동기(async) 기본 → 실시간 주문 처리에 적합
- 자동 API 문서 생성 (Swagger UI)

### DB: PostgreSQL

**선택 이유:**
- 동시 접속 처리 (태블릿 16대+ 동시 요청)
- JSONB 타입으로 메뉴 옵션 같은 유연한 데이터 저장
- 초기에는 SQLite로 시작해서 검증 후 전환 가능

### 프린터: python-escpos

**선택 이유:**
- 대부분의 영수증 프린터가 ESC/POS 프로토콜 호환
- USB, 네트워크, 시리얼 연결 모두 지원
- POS PC에서 백그라운드 서비스로 실행

## 배포 구조

### 가게 내 PC 구성

하나의 Windows PC에서 다음이 동시 실행됩니다:

1. 백엔드 서버 (FastAPI, 포트 8000)
2. POS 앱 (pywebview, 로컬 HTTP API)
3. 프린트 서버 (백그라운드, 포트 8001)

### 빌드/배포

| 컴포넌트 | 배포 방식 |
|----------|-----------|
| 손님용 앱 | APK 직접 설치 (Play Store 불필요) |
| POS 앱 | PyInstaller → exe (Slink과 동일) |
| 백엔드 | PyInstaller → exe 또는 Python 직접 실행 |
| 프린트 서버 | 백엔드와 함께 패키징 |

### 업데이트

Slink에서 검증된 패턴 적용:
- GitHub Releases API로 버전 비교
- 새 버전 다운로드 → 배치 스크립트로 파일 교체
- 수동 재실행

손님용 Android 앱은 관리자 페이지에서 APK 다운로드 → 각 태블릿에 설치하는 방식 (또는 MDM 도입 시 원격 배포).
