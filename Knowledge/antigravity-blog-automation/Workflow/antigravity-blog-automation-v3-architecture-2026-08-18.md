---
topic: antigravity-blog-automation-v3
title_kr: "[블로그 포스팅 자동화 구축기 #3] Control/Data Plane 분산 아키텍처와 Skill/Rule 체계 전면 개편"
category: Workflow
sub_category: System-Architecture-v3
version: 2026-08-18
status: Experimental
created_date: 2026-08-18
last_modified: 2026-08-18
language: KR
tags: [Antigravity, 블로그-자동화, 분산-아키텍처, Control-Plane, Data-Plane, Skill-시스템, Rule-가드레일, 슬래시-커맨드, 멀티-블로그, 시리즈-네비게이션]
sources_count: 5
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
* **Status:** Experimental
* **Date:** 2026-08-18
* **Language:** KR

---

#### 📚 Sources & Confidence

* [★★★★★] BlogDocs v3.0 아키텍처 및 소스 코드: `.agents/skills/` (1~5), `.agents/rules/`, `README.md`
* [★★★★★] 시리즈 네비게이션 UI 가이드: `Instructions/series_navigation_plan.md`
* [★★★★★] BlogDocs 커밋 이력 및 변경점 히스토리 (Git Log)
* [★★★★★] 이전 파이프라인 지식 문서: `antigravity-blog-automation-v2-pipeline-2026-08-17.md`
* [★★★★★] 사용자 실전 검증: Control/Data Plane 분리, 슬래시 커맨드 전환, 멀티 블로그 확장 셋업 경험

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
  * **v3.0 네이티브 모듈화:**
    * **Skills 모듈화 (`.agents/skills/`):** `1_주제`, `2_피드백`, `3_게시글`, `4_발행완료`, `5_블로그추가`의 5개 독립 스킬로 완벽히 캡슐화. 슬래시 커맨드(`/1_주제`, `/2_피드백` 등)를 통해 필요할 때만 해당 스킬이 활성화되어 오발동 원천 차단.
    * **전역 Rules 체계 (`.agents/rules/`):** 블로그 하네스 핵심 가드레일(`blog-harness-rules.md`), 클릭 가능한 절대경로 마크다운 링크 규칙(`always-link-files.md`), 군더더기 없는 직접 한국어 출력(`always-use-primitive-output.md`)을 상시 적용 규칙으로 승격하여 에이전트의 일탈을 방지. [FACT]

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

* [OPINION] 다중 블로그 확장을 처음부터 염두에 두지 않고 단일 레포지토리에 모든 데이터를 몰아넣으면 나중에 분리할 때 막대한 마이그레이션 비용이 든다. Control Plane(지능/룰)과 Data Plane(데이터/콘텐츠)을 초기부터 분리하는 구조가 장기적인 블로그 운영 자산화에 필수적이다. — 출처: 사용자 실전 경험 [USER VERIFIED]

---

#### ❓ Missing Info (사용자 직접 검증 필요 항목)

* [ ] **Control Plane ↔ Data Plane 경로 연동 검증:** `BlogDocs`의 Control Plane에서 `/3_게시글` 및 `/4_발행완료` 실행 시 `Blog_Core-Archive`의 Data Plane 파일들이 정상적으로 생성/수정/커밋되는지 전체 사이클 테스트.
* [ ] **Blogger 테마 동적 탭 네비게이션 렌더링 검증:** `Instructions/series_navigation_plan.md` 기반으로 블로그스팟 테마에 적용된 자바스크립트가 `series-nav` 컨테이너를 정상적으로 인식하여 시리즈 목록 탭과 추천 글 탭을 올바르게 파싱/렌더링하는지 실전 블로그 페이지에서 확인.
* [ ] **슬래시 커맨드(`/1_주제` ~ `/5_블로그추가`) UI 편의성 검증:** 안티그래비티 채팅창에서 슬래시 커맨드 입력 시 기존의 자연어 오발동 없이 의도한 스킬만 안정적으로 트리거되는지 확인.

---

#### 🏷️ Tags

Antigravity, 블로그-자동화, 분산-아키텍처, Control-Plane, Data-Plane, Skill-시스템, Rule-가드레일, 슬래시-커맨드, 멀티-블로그, 시리즈-네비게이션, Blogger, Median-UI

===== KNOWLEDGE PACKAGE END =====
