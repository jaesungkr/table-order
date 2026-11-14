# Table Order System

Design notes for a restaurant ordering system connecting Android tablets, a Windows POS, and a local backend.

**Status: design stage.** This repository contains specifications and screen flows, not an implemented application.

## Planned workflow

1. Guests browse the menu and submit an order from a table tablet.
2. The backend sends the order to the POS over WebSocket.
3. The POS displays the order and requests a kitchen receipt.
4. Staff manage menus, availability, tables, and order history.

The initial payment plan records payment details after staff process the payment on an existing terminal. Payment-provider integration is outside the initial scope.

## Proposed components

| Component | Platform | Technology |
|---|---|---|
| Guest app | Android tablet | Kotlin, kiosk mode |
| POS / kitchen app | Windows | Python, pywebview |
| Backend | In-store PC | FastAPI, WebSocket |
| Admin interface | Browser | Server-hosted HTML, CSS, JavaScript |
| Print service | POS PC | Python, python-escpos |

## Design documents

The detailed planning documents are currently written in Korean.

- [System architecture](docs/architecture.md)
- [Guest app flow](docs/customer-app-flow.md)
- [POS app flow](docs/pos-app-flow.md)
- [Technology choices](docs/tech-stack.md)
- [MVP scope and priorities](docs/mvp-spec.md)

## Repository scope

This is a personal planning workspace. There is no application build, installation package, or deployment procedure yet.

## Author

ja2sng
