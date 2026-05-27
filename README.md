# Table Order System

매장 테이블 오더 시스템. 손님이 태블릿에서 주문하면 POS에서 실시간으로 수신하고 영수증을 자동 출력합니다.

## 구성

| 컴포넌트 | 플랫폼 | 기술 스택 |
|----------|--------|-----------|
| 손님용 앱 | Android 태블릿 | Kotlin (키오스크 모드) |
| POS/주방 앱 | Windows PC | Python + pywebview |
| 백엔드 서버 | 가게 내 PC | Python FastAPI + WebSocket |
| 관리자 페이지 | 웹 브라우저 | 서버 내장 웹 페이지 |
| 프린트 서버 | POS PC (백그라운드) | Python + python-escpos |

## 문서

- [시스템 아키텍처](docs/architecture.md)
- [손님용 앱 화면 플로우](docs/customer-app-flow.md)
- [POS 앱 화면 플로우](docs/pos-app-flow.md)
- [기술 스택 및 결정 근거](docs/tech-stack.md)
- [MVP 기능 명세](docs/mvp-spec.md)

## Status

🚧 설계 단계 — 개발 미착수

## Author

ja2sng
