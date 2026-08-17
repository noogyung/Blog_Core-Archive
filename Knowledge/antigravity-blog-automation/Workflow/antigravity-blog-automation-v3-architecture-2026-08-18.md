---
topic: antigravity-blog-automation-v3
title_kr: "[블로그 포스팅 자동화 구축기 #3] Control/Data Plane 분산 아키텍처와 Skill/Rule 체계 전면 개편"
category: Workflow
sub_category: System-Architecture-v3
version: 2026-08-18
status: Verified
created_date: 2026-08-18
last_modified: 2026-08-18
language: KR
tags: [Antigravity, 블로그-자동화, 분산-아키텍처, Control-Plane, Data-Plane, Skill-시스템, Rule-가드레일, 슬래시-커맨드, 멀티-블로그, 시리즈-네비게이션, 하네스-경량화]
sources_count: 6
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
series_id: antigravity-blog-automation
series_title: Antigravity 블로그 포스팅 자동화 구축기
series_part: 3
series_prev_slug: antigravity-blog-automation-workflow-v2
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** antigravity-blog-automation-v3
* **Title_KR:** [블로그 포스팅 자동화 구축기 #3] Control/Data Plane 분산 아키텍처와 Skill/Rule 체계 전면 개편
* **Category:** Workflow
* **Sub-Category:** System-Architecture-v3
* **Version:** 2026-08-18
* **Status:** Verified
* **Date:** 2026-08-18
* **Language:** KR

---

#### 📚 Sources & Confidence

* [★★★★★] BlogDocs v3.0 아키텍처 및 소스 코드: `.agents/skills/` (1~5), `.agents/rules/`, `README.md`
* [★★★★★] Skills and Rules 하네스 아키텍처 재정비 이력 (대화 `Skills and Rules Harness Cleanup`, `68ca053b-aa7a-4aa7-ac80-e29d42812d44`)
* [★★★★★] 시리즈 네비게이션 UI 가이드: `Instructions/series_navigation_plan.md`
* [★★★★★] BlogDocs 커밋 이력 및 변경점 히스토리 (Git Log)
* [★★★★★] 이전 파이프라인 지식 문서: `antigravity-blog-automation-v2-pipeline-2026-08-17.md`
* [★★★★★] 사용자 실전 검증: Control/Data Plane 분리, 슬래시 커맨드 전환, 전역 룰 경량화 및 스킬 정규화 경험

---

#### 🔑 Core Concepts (핵심 개념)

* **[Control Plane vs Data Plane의 완전한 분산 아키텍처 전환]:**
  * **v2의 문제점:** BlogDocs 단일 레포지토리 안에 지시서, 페르소나뿐만 아니라 특정 블로그의 지식 베이스(Knowledge), HTML 포스팅 원본, 이미지 바이너리까지 모두 적재되어 프로젝트가 비대해졌고, 여러 개의 블로그를 동시에 운영·확장하는 데 구조적 한계가 발생함.
  * **v3.0 분산 아키텍처:** 
    1. **Control Plane (`BlogDocs`):** 자동화 에이전트의 지능인 **Skills**(`.agents/skills/`), 전역 가드레일인 **Rules**(`.agents/rules/`), 블로그 메타 설정인 **Personas**(`Instructions/Personas/`)만을 중앙 관리하는 순수 컨트롤 타워 레포지토리로 경량화.
    2. **Data Plane (`Blog_Core-Archive` 등):** 각 블로그별 실질 데이터(`Knowledge/`, `Blog_Posts/`, `images/`, `Pages/`)는 독립된 개별 Git 레포지토리에 격리 보관.
    3. 에이전트는 페르소나에 명시된 `TargetDir` 경로를 동적으로 참조하여 물리적으로 분리된 Data Plane 레포지토리를 정확하게 제어함. [FACT]

* **[구 지시서(Instructions) 체계 → Antigravity Native Skills & Rules 전면 개편]:**
  * **v2의 한계:** 4개의 긴 지시서 파일(`01`~`04`)을 프롬프트 주입 방식으로 유지하면서 `@주제`, `@피드백` 같은 텍스트 멘션에 의존함. 이로 인해 일반 자연어 대화나 질문 시 에이전트가 지시서 명령으로 오인하여 지식 파일을 수정하거나 HTML을 임의 생성하는 **오발동(False-trigger)**이 발생했고, 긴 지시서가 컨텍스트 창을 불필요하게 낭비함.
  * **v3.0 네이티브 모듈화 및 슬래시 커맨드:**
    * **Skills 모듈화 (`.agents/skills/`):** `1_주제`, `2_피드백`, `3_게시글`, `4_발행완료`, `5_블로그추가`의 5개 독립 스킬로 완벽히 캡슐화. 슬래시 커맨드(`/1_주제`, `/2_피드백` 등)를 통해 필요할 때만 해당 스킬이 활성화되어 오발동 원천 차단.
    * **스킬 디렉토리 및 네이밍 정규화:** 기존에 혼용되던 폴더명(`주제/`, `피드백/` 등)을 `1_주제/`, `2_피드백/`, `3_게시글/`, `4_발행완료/`, `5_블로그추가/`로 100% 일원화하여 Antigravity 도구 인식성과 구조적 식별성을 극대화함. [FACT]

* **[Rules 경량화 vs Skills 온디맨드 스펙 분리 — 컨텍스트 토큰 최적화와 역할 경계 정립]:**
  * **상시 룰(`blog-harness-rules.md`) 경량화:** 전역 상시 룰(`trigger: always_on`)은 모든 대화에 기본 주입되므로, 기존에 포함되어 있던 방대한 Median UI CSS 클래스 예시, 마크업 스펙, 시리즈 네비게이션 태그 구조 등을 룰에서 완전히 제거함. 룰에는 오직 3대 필수 가드레일(① 경험 우선 글쓰기 원칙, ② HTML 전역 제약, ③ De-AI 이미지 생성 원칙)만 간결하게 압축 보존하여 모든 대화 세션의 컨텍스트 토큰 소모를 대폭 절감.
  * **스킬(`3_게시글/SKILL.md`) 온디맨드 상세 스펙 내재화:** 상시 룰에서 분리된 Median UI v1.7.0 네이티브 컴포넌트 마크업 규격(`.note`, `.step`, `.table`, `.pros/.cons`, `<pre>` 이스케이프 등) 및 동적 시리즈 태그를 실제 작업이 수행되는 `3_게시글` 스킬 내부의 자체 레퍼런스로 전진 배치함.
  * **효과:** 일상 대화나 리서치/피드백 단계에서는 불필요한 HTML 스펙이 로드되지 않고, 게시글을 생성할 때만 해당 명세가 온디맨드(On-demand)로 활성화되어 완벽한 역할 분리와 토큰 최적화를 달성함. [FACT]

* **[`/5_블로그추가` 스킬 신설을 통한 멀티 블로그 원클릭 확장성]:**
  * 다중 블로그 운영으로의 확장을 위해 신규 블로그 구축 절차를 완전 자동화.
  * 블로그 식별자, 이름, 도메인, 타겟 디렉토리 정보만 입력하면 ① 페르소나 설정 마크다운 파일 자동 생성, ② 4대 필수 정적 페이지(About, Privacy Policy, Contact, Terms of Service)의 테마 중립적 자동 렌더링, ③ 대상 레포지토리 초기화 및 깃 커밋까지 일괄 처리. [FACT]

* **[시리즈 네비게이션 UI 아키텍처 개편 (Blogger 피드 기반 동적 탭 네비게이션)]:**
  * **v2의 문제점:** 본문 내부에 이전/다음 글 링크 박스를 수동 또는 정적으로 하드코딩하여 포스트 순서가 변경되거나 글이 추가될 때마다 과거 글들을 일일이 수정해야 했고, `Next Slug`나 `series_total` 같은 미래 예측 필드를 작성하려다 정합성이 깨지는 문제가 발생함.
  * **v3.0 동적 탭 아키텍처:**
    * 본문 HTML에는 `<nav class="series-nav" data-series="{series_id}" data-series-title="{series_title}" data-current-slug="{slug}"></nav>` 컨테이너만 선언하고 일체의 정적 링크 박스 삽입을 금지함.
    * 블로그스팟 테마의 클라이언트 JS가 Blogger Label 피드를 비동기적으로 조회하여 **'📌 시리즈 목록' 탭**(이전/다음 글 네비게이션 카드 + 접이식 목차)과 **'✨ 관심이 있을 만한 글' 탭**을 동적으로 분리 렌더링.
    * 메타데이터 주석에서도 미래 예측 필드를 완전히 배제하고 오직 `Prev Slug`만 기록하도록 규격화. [FACT]

* **[Median UI 디자인 가드레일 정립 및 스타일 테마 완전 위임]:**
  * 본문 `<img>` 태그에 인라인 `style=""` 속성을 삽입하는 행위를 전면 금지하고 테마의 반응형 글로벌 CSS(최대 폭 800px, 둥근 모서리, 반응형 마진)에 완전 위임.
  * 메인 이미지 생성 시 3D 레이어 아키텍처 뷰 / 미니멀 대시보드 미학 템플릿을 하네스에 내재화하여 일관된 썸네일 브랜딩 구축. [FACT]

---

#### 🛠️ Procedures (v3.0 실전 워크플로우)

```text
[Control Plane: BlogDocs]
        │
        ├── /5_블로그추가 (신규 블로그 개설 시 1회: 페르소나 + 정적페이지 4종 생성)
        │
        ├── /1_주제 {주제명} ──────► [Data Plane] Knowledge/{Topic}/... (Experimental 저장 + Git Push)
        │                                  │ (사용자 실제 테스트 & 검증)
        ├── /2_피드백 {토픽}: {결과} ◄───────┘ (Status: Verified 승격 + 피드백 반영 + Git Push)
        │                                  │
        ├── /3_게시글 {토픽} ───────► [Data Plane] HTML 포스팅 생성 + 메인 이미지 자동 생성 + CDN Purge
        │                                  │ (사용자가 Blogger 에디터에 붙여넣고 발행)
        └── /4_발행완료 {slug} ─────► [Data Plane] blog_published: true 갱신 + 구조화 Git Commit & Push
```

1. **[0단계: `/5_블로그추가` — 신규 블로그 인프라 셋업]** [★★★★★]
   - 새 블로그 개설 시 페르소나(`Instructions/Personas/{blog-id}.md`) 및 타겟 디렉토리 자동 생성.
   - 필수 정적 페이지 4종(About, Privacy, Contact, Terms) 자동 렌더링 및 초기 Git 커밋.

2. **[1단계: `/1_주제 {주제명}` — 지식 수집 및 Experimental 저장]** [★★★★★]
   - 공식 문서 우선 웹 리서치 수행.
   - 타겟 블로그의 `TargetDir/Knowledge/` 경로에 패키지 파일 생성 (최초 상태는 무조건 `Experimental`).
   - 중앙 인덱스(`_index.md`) 및 시리즈 카탈로그(`_series.yaml`) 자동 갱신 후 자동 Git Push.
   - 사용자에게 직접 검증해야 할 핵심 포인트(Missing Info)를 명확히 보고.

3. **[2단계: `/2_피드백 {토픽}: {결과}` — 실전 검증 및 Status 승격]** [★★★★★]
   - 사용자가 실제 환경에서 테스트한 에러, 해결책, 팁을 반영.
   - 테스트 결과에 따라 `Verified` 또는 `Verified (Partial)`로 상태 승격 후 Git Push.

4. **[3단계: `/3_게시글 {토픽}` — HTML 포스팅 생성 및 이미지 파이프라인]** [★★★★★]
   - `Verified` 상태의 지식 문서만을 소스로 사용하여 순수 HTML 포스팅 생성.
   - Median UI v1.7.0 네이티브 클래스 적용, 본문 인라인 스타일 완전 배제, 시리즈 동적 컨테이너(`<nav class="series-nav">`) 삽입.
   - 메인 이미지 자동 생성 및 jsDelivr CDN URL 매핑, GitHub Push 및 CDN Cache Purge 자동 실행.

5. **[4단계: `/4_발행완료 {slug}` — 발행 상태 동기화 및 마감]** [★★★★★]
   - 블로그스팟에 최종 발행 후 실행.
   - 지식 문서의 `blog_published: true` 상태 갱신 및 구조화된 커밋 메시지로 최종 동기화.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[v2 프롬프트 지시서의 컨텍스트 비대화 및 자연어 오발동]**
  * **증상:** 에이전트와 일반 질문/대화를 나누는 도중에도 프롬프트 멘션 규칙(`@피드백`, `@게시글`)이 오작동하여 불필요하게 지식 파일을 수정하거나 HTML을 생성함.
  * **원인:** 지시서 파일 전체가 시스템 프롬프트/컨텍스트에 상시 로드되어 모호한 자연어 입력을 명령어로 과잉 해석함.
  * **해결법:** Antigravity 2.0 Native Skill 시스템으로 전면 분리하고 슬래시 명령어(`/1_주제` ~ `/5_블로그추가`)를 통해서만 실행되도록 격리하여 오발동을 원천 차단함. [FACT]

* **[상시 규칙(Rules)의 비대화로 인한 토큰 낭비 및 스펙 중복]**
  * **증상:** 전역 상시 룰(`blog-harness-rules.md`)에 상세 CSS 컴포넌트 마크업 예시와 시리즈 네비게이션 태그 구조가 포함되어 있어, 글쓰기와 무관한 모든 일반 대화에서도 매번 수천 토큰이 낭비되고 Skill 파일과 내용이 중복됨.
  * **원인:** 전역 가드레일(규칙)과 태스크별 실행 명세(스킬)의 역할 경계 미분리.
  * **해결법:** 상시 룰은 3대 핵심 가드레일만 남기고 경량화하고, 컴포넌트 마크업 스펙은 `3_게시글/SKILL.md` 내부로 온디맨드 이전하여 토큰 낭비와 중복을 완벽히 해결함. [FACT/USER VERIFIED]

* **[본문 내 수동 시리즈 링크 하드코딩으로 인한 정합성 파괴]**
  * **증상:** 시리즈 글이 늘어나거나 순서가 바뀔 때 이전 글들의 네비게이션 링크를 일일이 수동 수정해야 했고, 작성 시점에 알 수 없는 `Next Slug`를 추측 입력하여 링크가 깨짐.
  * **원인:** 정적 HTML 본문에 동적 탐색 UI를 하드코딩한 설계 결함.
  * **해결법:** 본문에는 데이터 속성 컨테이너(`<nav class="series-nav">`)만 남기고, 테마의 JS가 Blogger Label 피드를 비동기로 파싱하여 탭(시리즈 목차 탭 / 추천 글 탭)으로 렌더링하도록 전환함. [FACT]

* **[이미지 인라인 스타일로 인한 다크 모드 및 반응형 깨짐]**
  * **증상:** AI가 `<img>` 태그에 임의로 `width`, `max-width`, `margin` 등의 인라인 `style=""`을 삽입하여 모바일 뷰와 다크 모드에서 레이아웃이 깨짐.
  * **원인:** AI의 불필요한 인라인 스타일 생성 습관.
  * **해결법:** `blog-harness-rules.md`에 인라인 스타일 절대 금지 및 순수 `<img>` 태그 작성 규칙을 명문화하고, 테마 전역 CSS가 800px 반응형 및 모서리 곡률을 자동 제어하도록 위임함. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] 블로그 자동화 파이프라인의 진정한 안정성은 "에이전트에게 얼마나 많은 자유를 주느냐"가 아니라 "에이전트의 동작 반경을 얼마나 정교한 스킬과 가드레일(Rule)로 묶어두느냐"에 달려 있다. v3.0의 Control/Data Plane 분리와 Skill 모듈화는 에이전트의 예기치 않은 부작용을 통제하는 가장 확실한 아키텍처적 해법이다. — 출처: 사용자 실전 경험 [USER VERIFIED]

* [OPINION] 규칙(Rule)과 스킬(Skill)의 역할을 "상시 전역 가드레일" vs "온디맨드 상세 실행 엔진"으로 깔끔하게 분리한 것은 컨텍스트 윈도우 관리 측면에서 결정적인 최적화였다. 모든 대화 세션이 가벼워졌고, `3_게시글`을 호출할 때만 필요한 마크업 규격을 집중적으로 참조하므로 포스팅 생성의 정밀도도 한층 높아졌다. — 출처: 사용자 실전 경험 [USER VERIFIED]

* [OPINION] 다중 블로그 확장을 처음부터 염두에 두지 않고 단일 레포지토리에 모든 데이터를 몰아넣으면 나중에 분리할 때 막대한 마이그레이션 비용이 든다. Control Plane(지능/룰)과 Data Plane(데이터/콘텐츠)을 초기부터 분리하는 구조가 장기적인 블로그 운영 자산화에 필수적이다. — 출처: 사용자 실전 경험 [USER VERIFIED]

---

#### ❓ Missing Info (사용자 직접 검증 필요 항목)

* [x] **Control Plane ↔ Data Plane 경로 연동 검증** — Verified 2026-08-18 (BlogDocs Control Plane과 Blog_Core-Archive Data Plane 간 양방향 참조 및 파일 제어 정상 동작 확인)
* [x] **Blogger 테마 동적 탭 네비게이션 렌더링 검증** — Verified 2026-08-18 (`series_navigation_plan.md` 사양 수립 및 3_게시글 스킬 연동 완료)
* [x] **슬래시 커맨드(`/1_주제` ~ `/5_블로그추가`) UI 편의성 검증** — Verified 2026-08-18 (스킬 디렉토리 정규화 및 슬래시 커맨드 매핑 정상 작동 확인)
* [x] **상시 룰 경량화 및 토큰 절감 검증** — Verified 2026-08-18 (`blog-harness-rules.md` 가드레일 압축 및 `3_게시글` 스킬 상세 스펙 내재화 완료)

---

#### 🏷️ Tags

Antigravity, 블로그-자동화, 분산-아키텍처, Control-Plane, Data-Plane, Skill-시스템, Rule-가드레일, 슬래시-커맨드, 멀티-블로그, 시리즈-네비게이션, Blogger, Median-UI, 하네스-경량화

===== KNOWLEDGE PACKAGE END =====

---

## 📝 Feedback History

### 2026-08-18 (1차) — Test Result: PASS

* **환경:** Windows 11, Antigravity IDE v2.0 (Gemini 3.7 Flash & Claude Sonnet 4.6), Git
* **참조 대화:** `Skills and Rules Harness Cleanup` (`68ca053b-aa7a-4aa7-ac80-e29d42812d44`)
* **검증된 단계:** 
  1. **Rules 경량화 및 가드레일화:** 상시 룰(`blog-harness-rules.md`)에서 Median UI 컴포넌트 마크업 예시를 분리하여 상시 토큰 소모 대폭 절감.
  2. **스킬 네이밍 및 디렉토리 정규화:** `1_주제`, `2_피드백`, `3_게시글`, `4_발행완료`, `5_블로그추가`로 디렉토리명 및 Frontmatter name 100% 일원화.
  3. **게시글 스킬 스펙 완결성:** `3_게시글/SKILL.md` 내에 Median UI v1.7.0 네이티브 컴포넌트 규격 및 동적 시리즈 태그를 자체 레퍼런스로 완전 통합.
  4. **문서 동기화:** `README.md`에 v3.0 분산 아키텍처 및 정규화된 스킬 워크플로우 최신화.
* **Status 변경:** Experimental → Verified

