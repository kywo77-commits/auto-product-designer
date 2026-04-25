# 핸드오프 가이드 자동화 (Handoff Guide Agent)

## 역할

Figma 코멘트 + Notion 기획 문서를 분석해서 개발팀용 핸드오프 가이드를 자동 생성한다.
디자이너가 수동으로 정리하던 코멘트 분류, 정책 정리, 가이드 작성 과정을 자동화.

## 규칙

- 언어: 한국어
- 명시적 요청 없이 외부 서비스(Notion, Figma 등)에 직접 생성 금지
- Figma resolved 코멘트도 반드시 수집 (확정 정책의 근거)
- 코멘트의 최종 얼라인 내용으로 가이드 생성 (중간 논의 과정 X)

## 사전 준비

이 스킬을 사용하기 전에 팀에서 아래 항목을 먼저 세팅해야 합니다.

### 1. Figma 코멘트 컨벤션 정의

팀원들이 Figma 코멘트를 일관되게 작성해야 자동 분류 정확도가 올라갑니다.
아래는 예시이며, 팀 상황에 맞게 수정하세요.

```
✅ 확정된 내용 (또는 resolved 처리)
❓ 미정/논의중
🔁 정책 변경됨
```

### 2. 코멘트 분류 규칙 정의

아래 규칙을 팀의 코멘트 컨벤션에 맞게 수정하세요.

```
<!-- ↓ 팀 컨벤션에 맞게 키워드와 카테고리를 수정하세요 -->
- "✅", "반영완료", "반영했어요", resolved → [정책확정]
- "❓", "확인필요", "미정", "추후논의" + unresolved → [미결이슈]
- FE/BE 개발자 관련 협의 → [개발협의]
- QA 관련 → [QA피드백]
- 키워드 없는 unresolved → [미결이슈] (기본값)
```

### 3. 환경 설정

| 항목 | 설정 | 비고 |
|------|------|------|
| Figma API 토큰 | `.env`의 `FIGMA_API_TOKEN` | Figma > Settings > Personal access tokens |
| Notion MCP | Claude Code에 연동 | 가이드 자동 생성 시 필요 |
| Figma MCP | Claude Code에 연동 (선택) | 디자인 컨텍스트 추출 시 |

## 워크플로우

### Step 1. Figma 파일 구조 파악
- Figma REST API로 파일 구조 파악 (`depth=3`)
- 핸드오프 페이지 식별 (팀 컨벤션에 따라 페이지명 기준)
  <!-- ↑ 예: 📌 이모지로 시작하는 페이지, "핸드오프"가 포함된 페이지 등 -->
- 디바이스 타입은 프레임명에서 파싱 (iOS/AOS/Web)

### Step 2. 코멘트 수집 + 자동 분류
- resolved + unresolved 코멘트 전체 수집
- 위 분류 규칙에 따라 자동 카테고리 분류
- 분석 결과 저장: `references/figma-analysis-{파일명}.md`

### Step 3. 개발 가이드 생성
- `references/handoff-guide-template.md` 포맷으로 가이드 생성
- 인풋: Figma 분석 결과 + Notion 기획 문서 (있는 경우)

### Step 4. (선택) Figma 어노테이션
- Figma Plugin으로 어노테이션 카드 배치 (수동 실행 필요)
- REST API는 읽기 전용이므로 Plugin API 사용

## 인풋별 처리

| 인풋 | 처리 |
|------|------|
| Figma URL | 구조 파악 + 코멘트 수집 + 분류 + 분석 MD 저장 |
| Figma URL + Notion URL | 위 + 기획 컨텍스트 통합 → 풍부한 가이드 |
| 기존 분석 MD | 분석 건너뛰고 가이드 생성만 |

## 아웃풋 구조

### Figma 분석 결과 (`references/figma-analysis-{파일명}.md`)
```
1. 파일 정보 (URL, 수정일, 코멘트 수, 작성자별 통계)
2. 페이지 구조 (섹션, 프레임 계층)
3. 코멘트 분류 결과 (정책확정/미결이슈/개발협의/QA피드백)
```

### 개발 가이드 (`references/handoff-guide-template.md` 참조)
```
1. 프로젝트 개요 (배경, 목적, 링크)
2. 기능 정의 및 정책 (기능 목록, 분기 조건)
3. 화면 명세 (섹션별 화면 설명 + Figma 딥링크)
4. 미결 이슈 목록
5. 개발 협의 히스토리 (날짜별 결정사항)
6. QA 체크리스트 (화면/케이스별 검증 항목)
```

## 참고 리소스

- 가이드 템플릿: `references/handoff-guide-template.md`
- 분석 결과 예시: `references/figma-analysis-example.md`

## 알려진 한계

- Figma 코멘트 분류는 키워드 기반이라 맥락 파악이 부족한 경우 있음
- 결론이 명시적이지 않은 스레드는 놓칠 수 있음
- Figma 어노테이션 배치는 Plugin 수동 실행 필요 (REST API 쓰기 불가)
- 80% 자동 생성 + 20% 수동 보정이 현실적 목표
