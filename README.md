# grill-with-docs-html

`grill-with-docs`의 기본(default) HTML 폼 출력 변형 스킬입니다.
같은 Socratic 인터뷰로 플랜을 검증하면서, 결정 트리를 언제나 인터랙티브 HTML 폼으로 내보냅니다.

## 이 스킬이 하는 일

- CONTEXT.md / ADR 기반 Socratic 인터뷰로 플랜·후보를 검증합니다 (grill-with-docs와 동일)
- 후보별 **FACTS 패널** — 파일 경로 + 라인 번호, 실제 상수·SQL·DTO 목록, 마찰 신호
- **결정 카드** — 선택지마다 한 줄 trade-off, 권장 옵션 사전 선택, **WHY 패널** (녹색)
- **Before / After Mermaid 다이어그램** 나란히 배치 (mmdc 검증 필수)
- **SIDE EFFECTS 패널** (노란색) — 추가할 CONTEXT.md 용어, ADR 후보, 하위 테스트·계약 영향
- **스티키 하단 바** — "결정 텍스트 생성" + "클립보드로 복사" 버튼, 구조화 마크다운 생성

## grill-with-docs 와의 관계

이 스킬은 `grill-with-docs`를 대체하지 않고 보완합니다.
인터뷰 행동(CONTEXT.md 발견, ADR 제안, 용어 다듬기)은 `grill-with-docs`를 그대로 재사용하고,
이 스킬의 파일들은 HTML 내보내기 레이어만 담당합니다.
`grill-with-docs/SKILL.md`는 이 스킬을 기본 동작으로 가리킵니다.

## 설치

```bash
git clone https://github.com/cskwork/grill-with-docs-html ~/.claude/skills/grill-with-docs-html
```

`grill-with-docs` 스킬도 함께 필요합니다:

```bash
# grill-with-docs 가 없다면
git clone https://github.com/obra/grill-with-docs ~/.claude/skills/grill-with-docs
```

## 사용 트리거

| 언어 | 키워드 |
|------|--------|
| 한국어 | "그릴링", "디자인 결정", "결정 폼", "후보 그릴링" |
| English | "grilling", "design decision", "decision form", "grill candidates" |

## 요구사항

- **Node.js + npx** — Mermaid 다이어그램 검증에 사용 (`@mermaid-js/mermaid-cli`). 첫 실행 시 `npx` 가 자동으로 설치합니다.
- `gh` CLI (선택) — 스킬 업데이트 확인 목적.

## 파일 구성

| 파일 | 역할 |
|------|------|
| `SKILL.md` | 스킬 진입점 — 인터뷰 순서, HTML 내보내기 조건, skip 규칙 |
| `HTML-FORM.md` | 자체 완결 HTML 폼 템플릿 (Tailwind CDN, Mermaid CDN, Pretendard 폰트) |
| `MERMAID-SAFE-SUBSET.md` | mmdc 렌더링 안전 부분집합 — 금지 패턴 목록 및 허용 문법 |
| `VALIDATION.md` | Mermaid 블록 추출·검증 스니펫 (mmdc 파이프라인) |

## 예시 출력

스크린샷은 `screenshots/` 디렉토리가 추가되면 확인할 수 있습니다.

## 라이선스 (License)

MIT — see [LICENSE](LICENSE).
