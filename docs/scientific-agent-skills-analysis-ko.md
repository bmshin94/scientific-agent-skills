# Scientific Agent Skills 분석 정리 (한국어)

> 이 문서는 `scientific-agent-skills` 레포지토리를 직접 열어보고 분석한 내용을 정리한 기록입니다.
> 작성일: 2026-09-18

---

## 📎 관련 GitHub 주소

| 구분 | 주소 |
|---|---|
| **이 레포 (포크)** | https://github.com/bmshin94/scientific-agent-skills |
| **원본 레포 (upstream)** | https://github.com/K-Dense-AI/scientific-agent-skills |
| 제작사 홈페이지 | https://k-dense.ai |
| 관련 오픈소스 (AI 코사이언티스트) | https://github.com/K-Dense-AI/k-dense-byok |
| Agent Skills 표준 | https://agentskills.io/ |
| Agent Plugins 표준 | https://agent-plugins.org/ |
| 논문 (arXiv) | https://arxiv.org/abs/2609.00065 |
| Anthropic 공식 스킬 (docx/pdf/pptx/xlsx 출처) | https://github.com/anthropics/skills |
| 보안 스캐너 | https://github.com/cisco-ai-defense/skill-scanner |

---

## 1. 이게 뭐하는 물건인가

> **한 줄 요약: AI 에이전트(Claude Code, Cursor 등)를 "연구용 AI 과학자"로 바꿔주는 과학 분야 매뉴얼 166권 세트.**

- 제작: **K-Dense Inc.**
- 라이선스: **MIT** (상업적 이용 가능)
- 버전: **v2.69.0**
- 정체: **Agent Skills 166개 + Agent Plugins 1.0.0 패키지**
- arXiv 논문 보유: *Scientific Agent Skills: A Library of Procedural Knowledge for Research Agents* (arXiv:2609.00065)

### 핵심 아이디어

AI는 코드는 잘 짜지만, 특정 라이브러리의 **최신 버전 문법 / API 엔드포인트 / 현장의 함정**은 모릅니다.
그래서 추측으로 코드를 짜고 → 환각(hallucination) → 에러가 발생합니다.

이 레포는 그 간극을 **마크다운 문서(커닝페이퍼)** 로 메웁니다.
즉, **실행되는 프로그램이 아니라 "AI에게 읽히는 설명서"** 입니다.

---

## 2. 폴더 구조 (실제 확인 결과)

```text
scientific-agent-skills/
├── plugin.json          # Agent Plugins 1.0.0 매니페스트 (신분증)
├── pyproject.toml       # Python 3.13+, pytest, cisco-ai-skill-scanner
├── README.md            # 75KB(!) 초대형 문서
├── AGENTS.md            # 기여자용 규칙집 (스킬 설계 철학)
├── CONTRIBUTING.md      # 20KB PR 가이드
├── SECURITY.md          # 보안 정책
├── CITATION.cff         # 인용 정보
├── skills/              # ⭐ 핵심: 166개 스킬
│   └── <skill-name>/
│       ├── SKILL.md     # 필수 (YAML frontmatter + 본문)
│       ├── references/  # 선택: 긴 문서, 필요할 때만 로딩
│       ├── scripts/     # 선택: 실행 헬퍼 (106개 스킬이 보유)
│       └── assets/      # 선택: 템플릿/정적 리소스
├── tests/               # 111개 테스트 폴더 (스킬 폴더 밖에 위치)
├── docs/
│   ├── security-report.md   # 주간 보안 스캔 결과
│   └── images/              # 스킬별 워크플로우 다이어그램
├── scripts/
│   └── generate_skill_image.py
└── .github/workflows/
    ├── security-scan.yml
    ├── pr-skill-scan.yml
    ├── skill-spec-validation.yml
    ├── skill-tests.yml
    └── release.yml
```

### SKILL.md 실제 예시 (`skills/rdkit/SKILL.md`)

```markdown
---
name: rdkit
description: Cheminformatics toolkit for fine-grained molecular control.
             SMILES/SDF parsing, descriptors (MW, LogP, TPSA), fingerprints...
             For standard workflows with simpler interface, use datamol.
             Use rdkit for advanced control, custom sanitization.
license: BSD-3-Clause license
allowed-tools: Read Write Edit Bash
compatibility: Examples target RDKit 2026.03.x
metadata:
  version: "1.3"
  skill-author: K-Dense Inc.
---

# RDKit Cheminformatics Toolkit
## Overview / Installation / Core Capabilities ...
```

---

## 3. 166개 스킬 카테고리

| 분야 | 스킬 예시 |
|---|---|
| 생명정보학/유전체 | `biopython`, `scanpy`, `pysam`, `alphagenome`, `genomic-intelligence`, `pydeseq2`, `phylogenetics` |
| 신약개발/화학 | `rdkit`, `deepchem`, `diffdock`, `medchem`, `molfeat`, `datamol`, `pytdc` |
| 단백질 공학 | `esm`, `torchdrug`, `adaptyv`, `tamarind` |
| 임상/의료 | `clinical-decision-support`, `pkpd-modeling`, `pydicom`, `pyhealth`, `pathml`, `treatment-plans` |
| 머신러닝 | `scikit-learn`, `pytorch-lightning`, `transformers`, `shap`, `pymc`, `stable-baselines3` |
| 물리/천문/양자 | `astropy`, `qiskit`, `qutip`, `cirq`, `pennylane`, `sympy` |
| 데이터/시각화 | `polars`, `dask`, `matplotlib`, `seaborn`, `networkx`, `vaex`, `zarr-python` |
| 논문 작성/조사 | `literature-review`, `scientific-writing`, `peer-review`, `paperclip`, `pyzotero`, `latex-posters` |
| 실험실 자동화 | `opentrons-integration`, `benchling-integration`, `pylabrobot`, `protocolsio-integration` |
| 문서 생성 | `docx`, `pdf`, `pptx`, `xlsx` (**Anthropic 제작분을 벤더링**) |
| DB 조회 | `database-lookup` 하나로 **78개 공개 DB** 접근 |

### 규모 요약

| 항목 | 수치 |
|---|---|
| 스킬 개수 | 166개 |
| 연결 DB | 100개 이상 (database-lookup만 78개) |
| 테스트 폴더 | 111개 |
| scripts 보유 스킬 | 106개 |
| API 키 필요 스킬 | **37개** |
| API 키 불필요 스킬 | **129개 (78%)** |

---

## 4. 설치 및 사용법

### 방법 1: npx (가장 간단)
```bash
npx skills add K-Dense-AI/scientific-agent-skills
```

### 방법 2: GitHub CLI (필요한 것만 선택 — 권장)
```bash
gh skill install K-Dense-AI/scientific-agent-skills            # 대화형
gh skill install K-Dense-AI/scientific-agent-skills scanpy     # 개별 설치
gh skill install K-Dense-AI/scientific-agent-skills --agent cursor
gh skill install K-Dense-AI/scientific-agent-skills --pin v2.66.0   # 버전 고정
gh skill update --all                                          # 업데이트
```
> `gh` v2.90.0 이상 필요

### 방법 3: 플러그인으로 통째로
```bash
# Cursor
mkdir -p ~/.cursor/plugins/local
ln -s "$(pwd)" ~/.cursor/plugins/local/scientific-agent-skills

# Codex
codex plugins install .
```

### 방법 4: 수동 clone
```bash
git clone https://github.com/K-Dense-AI/scientific-agent-skills.git ~/.agents/skills/scientific-agent-skills
git clone https://github.com/K-Dense-AI/scientific-agent-skills.git .agents/skills/scientific-agent-skills
```

### 사전 준비물
- Python **3.13+**
- **uv** (필수): `curl -LsSf https://astral.sh/uv/install.sh | sh`
- macOS / Linux / Windows(WSL2)

### 사용법
따로 실행할 것이 없습니다. 평소처럼 자연어로 요청하면 에이전트가 관련 스킬을 자동 로딩합니다.
특정 스킬을 강제하려면 프롬프트에 스킬 이름을 직접 언급하면 됩니다.

> ⚠️ 공식 권고: **166개 전부 설치하지 말 것.** 컨텍스트 낭비 + 스킬 선택 혼란이 발생합니다.

---

## 5. 플러그인? 스킬? MCP?

**정답: 스킬(Skill) 모음집 + 플러그인(Plugin) 패키지. MCP는 아님.**

레포 내 `mcp.json` 없음을 직접 확인했고, `AGENTS.md`에도 명시되어 있습니다:
> *"no inline MCP, hooks, or client-only keys"*

| | Skill | Plugin | MCP |
|---|---|---|---|
| 정체 | 마크다운 문서 | 스킬 묶음 패키지 | 실행되는 서버 |
| 역할 | AI에게 "지식" 주입 | 배포 단위 | AI에게 "도구/데이터" 연결 |
| 실행 | ❌ 읽히기만 함 | ❌ 포장지 | ✅ 프로세스 동작 |
| 프로토콜 | 없음 | 매니페스트 | JSON-RPC |
| 비유 | 매뉴얼 | 매뉴얼 선물세트 | USB 장치 |

Skill과 MCP는 경쟁 관계가 아니라 **레이어가 다르며, 함께 쓰면 시너지**가 납니다.
(실제로 `tamarind` 스킬은 REST API 또는 MCP 접근을 안내합니다.)

---

## 6. API 토큰이 필요한가?

**129개(78%)는 키 없이 즉시 사용 가능합니다.** 파이썬 라이브러리 스킬은 대부분 무료입니다.

키가 필요한 37개는 외부 서비스 연동 스킬입니다.

| 환경변수 | 서비스 | 비고 |
|---|---|---|
| `NCBI_API_KEY` | NCBI | 무료 (레이트 리밋 완화) |
| `HF_TOKEN` | Hugging Face | 무료 |
| `FRED_API_KEY` | 미 연준 경제데이터 | 무료 |
| `MP_API_KEY` | Materials Project | 무료 |
| `S2_API_KEY` | Semantic Scholar | 무료 |
| `ZOTERO_API_KEY` | Zotero | 무료 |
| `OPENALEX_API_KEY` | OpenAlex | 무료 |
| `EXA_API_KEY`, `PARALLEL_API_KEY` | 웹 검색 | 유료 |
| `ANTHROPIC_API_KEY`, `OPENROUTER_API_KEY` | LLM | 유료 |
| `MODAL_TOKEN` | 클라우드 GPU | 유료 |
| `ALPHAGENOME_API_KEY`, `GI_API_KEY` | 유전체 서비스 | 유료 |
| `BENCHLING_API_KEY` | LIMS | 기업용 |
| `TAMARIND_API_KEY`, `ROWAN_API_KEY`, `ADAPTYV_API_KEY`, `ESM_API_KEY` | 단백질/화학 클라우드 | 유료 |
| `AWS_ACCESS_KEY`, `AWS_SECRET_ACCESS_KEY` | AWS | 유료 |

> 해당 스킬을 설치하지 않으면 키는 전혀 필요 없습니다.
> 키는 `.env`로 관리하며 (`python-dotenv` 의존성 존재), 절대 커밋하지 않도록 주의해야 합니다.

---

## 7. 왜 GitHub에서 유명한가

1. **타이밍** — Agent Skills 표준 등장 직후 "166개 완성품"을 제시한 킬러 앱
2. **서사** — "당신의 코딩 에이전트를 데스크톱 AI 과학자로"라는 강력한 스토리
3. **학술 신뢰도** — arXiv 논문(2609.00065) 보유 → 연구자 인용 → 유입 선순환
4. **압도적 문서화** — README 75KB, 뱃지 15개, 유튜브 튜토리얼 6편, 스킬별 다이어그램
5. **기업급 신뢰 인프라** — 주간 자동 보안 스캔(Cisco AI Defense), PR 스캔, 규격 검증, 테스트 CI
6. **정직함** — "모든 스킬을 완벽히 검토했다고 보장 못 한다. 다 설치하지 말라"고 공개 명시
7. **넓은 호환성** — Cursor / Claude Code / Codex / Gemini CLI / Antigravity / Copilot / VS Code / Kiro

---

## 8. 로컬 에이전트 구축에 주는 도움

### (1) description 설계 패턴 — 166개의 검증된 사례
좋은 `description`은 세 요소를 담습니다.
1. **What** — 무엇을 하는가
2. **Trigger 키워드** — 사용자가 이 단어를 쓰면 발동 (SMILES, LogP, fingerprint...)
3. **Disambiguation** — "간단한 작업은 datamol, 고급 제어는 rdkit" → **형제 스킬 간 선택 충돌 방지**

### (2) Progressive Disclosure (토큰 절약의 정석)
- `SKILL.md`는 짧게(요약 표 + 링크), 상세 내용은 `references/`로 분리 → **필요할 때만 로딩**
- `database-lookup`이 극단적 예시: 78개 DB를 각각 `.md`로 쪼개고 `database_selection_guide.md`로 라우팅
- → **라우터 문서 + 세부 문서** 아키텍처는 그대로 차용 가능

### (3) 권한 최소화
`allowed-tools: Read Write Edit Bash` — 스킬별 도구 제한

### (4) 테스트/CI 전략
- **테스트는 절대 `skills/` 안에 두지 않음** — 스킬 폴더엔 에이전트가 로딩할 것만 둔다는 원칙
- `tests/<skill-name>/test_scripts.py` + `fixtures/`
- 테스트는 상대경로 탐색이 아닌 명시적 앵커로 스킬에 접근:
  ```python
  SKILL_ROOT = Path(__file__).resolve().parents[2] / "skills" / "<skill-name>"
  ```
- `pyproject.toml`의 `addopts = "--import-mode=importlib"` — prepend 모드에서 `tests/<skill>/`가
  네임스페이스 패키지가 되어 미설치 라이브러리가 설치된 것처럼 보이는 함정을 회피

### (5) 스킬 설계 철학 (AGENTS.md)
받지 않는 스킬:
- 범용 코딩/엔지니어링 스킬 — 모든 작업에서 선택 경쟁 유발
- 과학 예제만 붙인 범용 인프라 — 하나 받으면 모든 경쟁사를 받아야 함
- 다른 스킬로 라우팅하는 오케스트레이터 스킬 — 전문가 스킬과 전부 중복
- 기존 스킬이 이미 커버하는 서비스의 두 번째 provider

→ 핵심 원칙: **스킬은 좁아야 한다(narrow). 기능 하나에 스킬 하나.**

---

## 9. React / PHP로 만들 수 있는가

### (A) React/PHP로 "스킬"을 만들 수 있는가 → 가능
스킬은 **언어 중립적인 마크다운**입니다. 60개 스킬은 스크립트 없이 순수 문서로만 구성되어 있습니다.
React 스킬, Laravel 스킬 모두 자유롭게 작성 가능하며, `scripts/`도 Python일 필요가 없습니다.

```markdown
---
name: react-hooks-master
description: React 19 Hooks 완전 가이드. useState, useEffect, useOptimistic,
             use(), Server Components 패턴. React 코드 작성/리팩터링 시 사용.
allowed-tools: Read Write Edit Bash
metadata:
  version: "1.0"
---
```

### (B) React/PHP로 "이런 시스템"을 만들 수 있는가 → 가능 (역할 분담 필요)

| 만들 것 | 가능 여부 |
|---|---|
| 스킬 마켓플레이스 웹 | ✅ |
| 스킬 GUI 에디터 | ✅ |
| 검색·평점 사이트 | ✅ |
| 보안 스캔 대시보드 | ✅ |
| 웹 기반 AI 채팅 (스킬 로딩) | ✅ |
| 에이전트 실행 엔진 자체 | ⚠️ 별도 런타임 권장 |

권장 아키텍처:
```text
React (Next.js)  ─ 프론트: 브라우징 / 에디터 / 채팅 UI
      │ REST · SSE
Laravel (PHP)    ─ 백엔드: 인증 / 결제 / 스킬 CRUD / 버전 관리
      ├── Claude API (Agent SDK)   ─ 에이전트 실행
      └── 샌드박스 (Docker/Python) ─ 과학 라이브러리 격리 실행
```

결론: **React/PHP는 "스킬 생태계의 관리·유통·UI 레이어"** 를 담당하는 것이 최적입니다.

---

## 10. 수익화 아이디어

### TIER 1 — 즉시 시작 가능 (초기비용 ≈ 0)

**1) 기업 내부 지식 → 스킬 변환 컨설팅** ⭐ 최우선 추천
- 사내 위키 / 코딩 컨벤션 / 레거시 API 문서를 스킬로 변환
- 가격: 스킬 1개당 50~200만원, 패키지 500~3,000만원
- 기간: 스킬 1개당 1~3일 / 필요 기술: 마크다운
- 상품 예시: "AI 온보딩 팩" (코딩 컨벤션 + API 가이드 + 배포 런북 + 장애 대응 + 교육)

**2) 도메인 스킬팩 판매 (K-Dense 모델 복제)** ⭐ 최우선 추천
- 블루오션: 법률/계약, 세무/회계, 부동산, 이커머스, 마케팅, 제조/ERP
- **특히 "한국 특화"**: 공공데이터포털, 국세청, 카카오/네이버 API — 영어권 AI가 모르는 영역
- 수익: Free(5개) / Pro 월 2만원 / Team 월 10만원 / Enterprise 연 500만원~

**3) 교육 / 강의 / 전자책**
- 전자책 3~5만원, 인프런·유데미 강의 5~15만원, 기업 출강 4시간 200~500만원
- 근거: Agent Skills 한국어 자료가 사실상 부재 → 선점 효과

### TIER 2 — 중간 난이도 (개발 1~3개월)

**4) 스킬 마켓플레이스 (React + PHP 최적)**
- 검색/카테고리/랭킹, 평점·리뷰, 자동 보안 스캔 배지, 결제, 제작자 대시보드, 버전 관리
- 스택: Next.js + Laravel + 토스페이먼츠/Stripe + `cisco-ai-skill-scanner`
- 수익: 판매 수수료 20~30%, 제작자 Pro 구독, 기업 프라이빗 레지스트리, 인증 배지 수수료
- 리스크: 네트워크 효과 필요 → 초기에 자체 스킬 100개 선제 확보로 해결

**5) 노코드 스킬 빌더 (SaaS)** ⭐ 최우선 추천
- 문서 업로드 → AI가 SKILL.md 초안 생성 → GUI 편집 → 테스트 → 원클릭 배포
- Free / Pro 월 2.9만원 / Team 월 9.9만원 / Enterprise 월 50만원~
- 핵심 근거: **스킬 작성은 개발자만 가능하지만, 지식을 가진 사람은 비개발자** → 이 간극이 시장

**6) 스킬 보안 감사 서비스**
- 악성 명령어, 데이터 유출, 프롬프트 인젝션, 과도한 권한, 의존성 취약점 스캔
- 단건 1만원 / 구독 월 5만원 / 기업 CI 연동 월 50만원~ / 인증 배지 10만원
- 근거: 레포 스스로 "스킬은 임의 코드 실행 가능"이라 경고 → 수요 존재

### TIER 3 — 큰 그림 (6개월+)

**7) 버티컬 AI 에이전트 제품** — 스킬을 원가로 쓰고 제품을 판매 (예: "AI 세무사" 월 9.9만원)
**8) 기업용 프라이빗 스킬 레지스트리** — 온프레미스 + 권한관리 + 감사로그, 연 2,000만~1억원
**9) K-Dense 스킬 한국어 현지화 + 기술지원** — MIT라 합법, 국내 DB(KOBIC/KISTI) 추가, 연 500만원~

### 비교표

| 아이디어 | 난이도 | 초기비용 | 수익규모 | 속도 | 추천도 |
|---|---|---|---|---|---|
| 기업 스킬 컨설팅 | 낮음 | 0원 | 높음 | 매우 빠름 | ★★★★★ |
| 한국 특화 스킬팩 | 낮음 | ~0원 | 중간 | 빠름 | ★★★★★ |
| 교육/전자책 | 낮음 | 0원 | 낮음~중간 | 매우 빠름 | ★★★★ |
| 노코드 스킬 빌더 | 중간 | 중간 | 매우 높음 | 느림 | ★★★★★ |
| 마켓플레이스 | 높음 | 중간 | 매우 높음 | 매우 느림 | ★★★ |
| 보안 감사 | 중간 | 중간 | 중간 | 빠름 | ★★★ |
| 버티컬 제품 | 매우 높음 | 높음 | 최고 | 느림 | ★★★★ |
| 프라이빗 레지스트리 | 높음 | 높음 | 높음 | 느림 | ★★★ |
| 한국어 현지화 | 낮음 | 낮음 | 중간 | 빠름 | ★★★ |

### 추천 로드맵

```text
1개월차   한국 특화 스킬 10개 제작 후 GitHub 무료 공개 → 인지도·포트폴리오
2~3개월차 블로그/유튜브 "Agent Skills 한국어 가이드" 연재 → 문의 유입
3~6개월차 유입된 문의로 기업 컨설팅 수주 → 현금흐름 확보
6개월~    컨설팅에서 반복된 작업을 SaaS로 자동화 → 노코드 스킬 빌더 출시
```

전략 요약: **무료 공개 → 인지도 → 컨설팅 → SaaS**

---

## 11. 주의사항

1. **스킬은 AI에게 코드 실행·네트워크 요청·파일 수정을 지시할 수 있습니다.** 설치 전 `SKILL.md`를 읽으세요.
2. **166개 전부 설치하지 마세요.** 컨텍스트 낭비와 스킬 선택 혼란이 발생합니다. (공식 권고)
3. 커뮤니티 기여 스킬은 K-Dense가 전수 검토하지 못했다고 명시되어 있습니다.
4. 직접 스캔 가능:
   ```bash
   uv pip install cisco-ai-skill-scanner
   skill-scanner scan /path/to/skill --use-behavioral
   ```
5. 주간 스캔 결과는 `docs/security-report.md`에 공개됩니다.
6. `docx`, `pdf`, `pptx`, `xlsx` 스킬은 **Anthropic 저작물**이며 각 스킬의 `LICENSE.txt` 조건을 따릅니다.

---

## 12. 핵심 요약

- AI는 요리 천재지만 레시피를 몰랐고, 이 레포는 **과학 레시피 166권**이다.
- 레시피는 **실행 파일이 아니라 마크다운 텍스트**다.
- **MIT 라이선스**라 상업적 활용이 가능하다.
- **129개는 API 키 없이** 바로 쓸 수 있다.
- 이 레포의 진짜 가치는 스킬 자체보다 **"에이전트 스킬 설계 방법론"** 에 있다.
- 수익화는 **기업 컨설팅 → 한국 특화 스킬팩 → 노코드 빌더 SaaS** 순으로 접근하는 것이 현실적이다.
