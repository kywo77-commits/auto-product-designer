# 디자인 초안 자동화

Claude Code를 활용해 **3-에이전트 프레임워크** 기반으로 와이어프레임 초안을 자동 생성하는 프로덕트 디자이너용 템플릿입니다.

## 이 스킬이 하는 일

에픽/디스커버리 문서를 넣으면 → AI가 3가지 관점(비즈니스·경쟁사·도메인)으로 분석한 뒤 → 구조화된 마크다운 초안을 자동 생성합니다.

```
인풋 (에픽/디스커버리 문서)
    ↓
Agent A: 비즈니스 컨텍스트 분석
    ↓
Agent B: 경쟁사/시장 벤치마크
    ↓
Agent C: 도메인 정책 검증
    ↓
아웃풋 (마크다운 초안 → Stitch → Figma)
```

## 빠른 시작 (5분)

### 1. 파일 복사

```
your-project/
├── .claude/
│   └── rules/
│       └── design-draft.md          ← ① 행동 규칙 (복사)
├── references/
│   ├── design-draft-template.md     ← ② 아웃풋 포맷 (복사)
│   ├── ux-checklist.md              ← ③ UX 검증 기준 (복사, 공통)
│   ├── domain-policy.md             ← ④ 도메인 정책 (직접 작성)
│   └── competitor-data.csv          ← ⑤ 경쟁사 데이터 (직접 준비)
└── outputs/                         ← 생성물 저장 폴더
```

### 2. 커스터마이징

`design-draft.md`에서 아래 3곳만 자기 프로젝트에 맞게 수정:

| 항목 | 수정 내용 | 예시 |
|------|----------|------|
| Agent B 데이터소스 | 경쟁사 분석용 데이터 경로 | `references/competitor-data.csv` |
| Agent C 참조문서 | 도메인 정책/제약 문서 경로 | `references/domain-policy.md` |
| 디자인시스템 링크 | 팀 Figma 라이브러리 URL | Figma 링크 |

### 3. 실행

Claude Code에서:
```
/design-draft [에픽 Notion URL 또는 문서 경로]
```

## 파일 설명

| 파일 | 용도 | 커스텀 필요 |
|------|------|------------|
| `rules/design-draft.md` | AI 행동 규칙 + 워크플로우 정의 | O (데이터소스 경로) |
| `references/design-draft-template.md` | 초안 아웃풋 마크다운 포맷 | △ (섹션 추가/삭제 가능) |
| `references/ux-checklist.md` | 닐슨 휴리스틱 + UX 심리학 검증 기준 | X (공통) |
| `references/domain-policy-example.md` | 도메인 정책 작성 예시 | O (자기 도메인으로 교체) |

## 3-에이전트 프레임워크

### Agent A — 디스커버리 분석
에픽/디스커버리 문서에서 추출:
- 비즈니스 목표
- 타겟 사용자 & 시나리오
- 성공 지표 (KPI)
- 데이터 분석 결과

### Agent B — 경쟁사/시장 분석
경쟁사 데이터 기반 분석:
- UX 패턴 벤치마크
- 고객 Pain Point
- 차별화 포인트

### Agent C — 도메인 정책 검증
도메인 제약조건 기반 검증:
- 비즈니스 정책 준수 여부
- 법적/규제 제약사항
- UX 체크리스트 대조

## 워크플로우 상세

```mermaid
graph TD
    A["/design-draft 실행"] -->|Phase 1| B["Agent A: 디스커버리 분석"]
    A -->|Phase 2| C["Agent B: 경쟁사 분석"]
    A -->|Phase 3| D["Agent C: 도메인 검증"]
    A -->|Phase 4| E["마크다운 초안 생성"]
    E -->|리뷰 확정 후| F["Google Stitch 프롬프트 생성"]
    F --> G["Stitch에서 멀티스크린 UI 생성"]
    G --> H["Figma 내보내기 → 디테일 수정"]

    style A fill:#4a9eed,color:#fff
    style B fill:#f59e0b,color:#fff
    style C fill:#f59e0b,color:#fff
    style D fill:#f59e0b,color:#fff
    style E fill:#22c55e,color:#fff
    style F fill:#8b5cf6,color:#fff
    style G fill:#8b5cf6,color:#fff
    style H fill:#ec4899,color:#fff
```

## 인풋별 처리 가이드

| 인풋 | 실행되는 에이전트 | 아웃풋 |
|------|-----------------|--------|
| 에픽/디스커버리 문서 | A → B → C (전체) | 디자인 초안 마크다운 |
| 기존 Figma 화면 | B (경쟁사 대비 UX 크리틱) | 개선 리포트 |
| 경쟁사 자료만 | B → C (벤치마크 + 차별점) | 벤치마크 리포트 |

## 커스터마이징 가이드

### 에이전트 추가/수정

`design-draft.md`의 `3 에이전트 프레임워크` 섹션에서 에이전트를 추가하거나 역할을 수정할 수 있습니다.

예시 — 접근성 전문 에이전트 추가:
```markdown
- **Agent D (접근성 검증)**: WCAG 2.1 AA 기준으로 초안의 접근성 이슈를 사전 점검
```

### 도메인 정책 문서 작성

`references/domain-policy-example.md`를 참고해서 자기 도메인의 정책/제약사항을 정리하세요.

### UX 체크리스트 확장

`references/ux-checklist.md`에 팀 고유의 검증 항목을 추가할 수 있습니다.

## FAQ

**Q: Claude Code 없이도 쓸 수 있나요?**
A: `.claude/rules/` 구조는 Claude Code 전용입니다. 다른 AI 도구 사용 시 `design-draft.md` 내용을 시스템 프롬프트로 직접 전달하면 됩니다.

**Q: Google Stitch 없이도 되나요?**
A: 마크다운 초안까지는 Stitch 없이 생성 가능합니다. 이후 Figma에서 직접 와이어프레임을 그리면 됩니다.

**Q: 경쟁사 데이터가 없으면요?**
A: Agent B를 비활성화하거나, 간단한 벤치마크 URL 목록만 제공해도 동작합니다.
