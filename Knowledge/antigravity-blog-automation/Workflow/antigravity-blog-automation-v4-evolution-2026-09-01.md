---
topic: antigravity-blog-automation-v4
title_kr: "[블로그 포스팅 자동화 구축기 #4] 이전 버전 비교와 현재 아키텍처 진화 (Mermaid, SSOT 동적 탭, 양방향 동기화)"
category: Workflow
sub_category: System-Evolution-Comparison
version: 2026-09-01
status: Verified
created_date: 2026-09-01
last_modified: 2026-09-01
language: KR
tags: [Antigravity, 블로그-자동화, 시스템-진화, Mermaid, SSOT, 동적-네비게이션, 양방향-동기화, 분산-아키텍처, 가드레일, Blogger, Median-UI]
sources_count: 7
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: antigravity-blog-automation
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** antigravity-blog-automation-v4
* **Title_KR:** [블로그 포스팅 자동화 구축기 #4] 이전 버전 비교와 현재 아키텍처 진화 (Mermaid, SSOT 동적 탭, 양방향 동기화)
* **Category:** Workflow
* **Sub-Category:** System-Evolution-Comparison
* **Version:** 2026-09-01
* **Status:** Verified
* **Date:** 2026-09-01
* **Language:** KR

---

#### 📚 Sources & Confidence

* [★★★★★] BlogDocs v3.1+ 아키텍처 및 소스 코드: `.agents/skills/` (1~5), `.agents/rules/`, `Instructions/Personas/`
* [★★★★★] 이전 파이프라인 지식 문서:
  - 1세대: `antigravity-blog-automation/Workflow/antigravity-blog-automation-2026-06-19.md`
  - 2세대: `antigravity-blog-automation/Workflow/antigravity-blog-automation-v2-pipeline-2026-08-17.md`
  - 3세대: `antigravity-blog-automation/Workflow/antigravity-blog-automation-v3-architecture-2026-08-18.md`
* [★★★★★] 시리즈 네비게이션 가이드 및 카탈로그: `Instructions/series_navigation_plan.md`, `Knowledge/_series.yaml`, `Knowledge/_series.json`
* [★★★★★] Median UI 테마 XML 및 동적 스크립트: `Instructions/Personas/Core-Archive_Theme_Median-UI-v1.7.0-Custom.xml` (Mermaid.js 및 Lightbox 줌 모달 패치 커밋)
* [★★★★★] 최근 실전 포스팅 구축 및 검증 이력: `windows-speaker-changer` (2026-09-01), `unity6-urp-rendergraph` (2026-08-31)
* [★★★★★] 사용자 실전 검증: Mermaid 다이어그램 580px 표준화, 극장형 Lightbox 모달 모바일(360px~412px) 실기기 렌더링 검증 완료, 양방향 피드백 동기화 운용

---

#### 🔑 Core Concepts (핵심 개념)

* **[세대별 파이프라인 진화 총괄 비교 (v1 → v2 → v3 → 현재 상태)]:**
  * **1세대 (v1, 2026-06-19):** NotebookLM 연동 실패 후 Antigravity 단독 루프로 전환. 리서치→추출→HTML 생성 3단계 단순 텍스트 파이프라인. 지시서 체계 및 하네스가 미흡하여 자연어 프롬프트에 의존했고, 이미지 수동 복붙으로 로컬-블로그 상태 불일치 발생.
  * **2세대 (v2, 2026-08-17):** 4대 프롬프트 지시서(`01`~`04`) 및 4대 `@멘션` 트리거(`@주제`, `@피드백`, `@게시글`, `@발행완료`) 도입. GitHub + jsDelivr CDN 자동 이미지 연동 및 캐시 Purge 구축. 하지만 프롬프트 지시서 상시 로드로 인한 토큰 낭비, 자연어 오발동, 미완성 구상 메모로 인한 AI의 `Next Slug` 날조(404 에러) 및 단일 레포의 확장성 한계가 드러남.
  * **3세대 (v3, 2026-08-18):** Control Plane(`BlogDocs`)과 Data Plane(`Blog_Core-Archive`) 분산 아키텍처 확립. Antigravity 2.0 Native Skills(5대)와 슬래시 커맨드(`/1_주제` ~ `/5_블로그추가`)로 전면 개편하여 오발동 원천 차단. 상시 Rules 경량화 vs Skills 온디맨드 스펙 분리. Blogger Feed 기반 동적 탭 네비게이션 도입.
  * **현재 상태 (v3.1+, 2026-09-01):** Mermaid.js 동적 다이어그램 렌더링 엔진 통합 및 580px 표준화, 92vw×88vh 극장형 고해상도 Lightbox 줌 모달 지원, `_series.yaml`/`_series.json` SSOT 카탈로그 정착, `/2_피드백` 지식-HTML 양방향 동기화, 이미지 버전 관리(`_v1`, `_v2`) 및 `ImagePaths` 톤앤매너 계승, 캐시 버스팅 정밀화. [FACT]

* **[v3 로드맵 예정 항목 실천 현황 비교 분석]:**
  1. **테마 XML 레거시/하드코딩 요소 정리:** Mermaid.js 통합 시 CDATA 래핑, XML 엔티티 파서 호환, viewBox 스케일링 패치 완료. 레거시 위젯 코드는 사이드바 커스텀과 병행하여 지속 정비 중.
  2. **`/5_블로그추가` 2호 블로그 엔드투엔드 실전 검증:** 제2호 블로그 개설 계획이 아직 확정되지 않아 실전 테스트를 보류하고 향후 개설 시점으로 연기. [USER VERIFIED]
  3. **가이드라인(Rules/Skills) 지속 고도화:** `always-link-files`, `always-use-primitive-output`, `blog-harness-rules` 3대 초경량 가드레일 정착. 이미지 버전 히스토리(`_v1`, `_v2`), `ImagePaths` 톤 계승, `?v=` 캐시버스팅 명문화 완료. [FACT]
  4. **구글 Blogger 기본 댓글 한계 극복 및 독립형 댓글 재검토:** 현재 기본 Blogger 댓글 유지 중이며 추후 독립 댓글 재도입 과제로 보류 유지. [FACT]
  5. **본문 보조 이미지 및 다이어그램 추천 파이프라인:** Mermaid.js 동적 렌더링 엔진 내재화를 통해 복잡한 아키텍처/흐름도를 텍스트 코드로 즉시 생성·시각화하는 방식으로 보조 이미지 수요를 대폭 대체 및 고도화 완료. [FACT]

* **[Mermaid.js 동적 다이어그램 & 극장 스케일 Lightbox 줌 모달 통합]:**
  * **기존 한계:** 복잡한 시스템 구조나 아키텍처를 설명할 때 외부 그래픽 툴로 이미지를 일일이 생성하거나 AI 썸네일에 의존해야 했고, 본문 내 텍스트/도식의 가독성과 수정 편의성이 떨어졌음.
  * **현재 구축된 아키텍처:**
    1. **Mermaid.js 동적 렌더링:** HTML 본문에 표준 Markdown/HTML `<pre class="mermaid">` 블록을 작성하면 브라우저 클라이언트가 즉시 SVG 벡터로 렌더링.
    2. **전역 580px 표준화 및 폰트 스케일:** SVG 다이어그램이 컨테이너 너비에 맞춰 억지로 늘어나 폰트가 깨지는 현상을 방지하기 위해 580px 자연 너비와 6px/13px 폰트 스케일, viewBox 보정, CDATA 래핑 적용.
    3. **극장형 Lightbox 모달 (92vw × 88vh):** 복잡한 다이어그램 클릭 시 화면의 92vw × 88vh 크기 뷰어로 1:1 고해상도 벡터 확대되어 데스크톱 및 모바일(360px~412px 뷰포트) 실기기 모두에서 정상 작동 확인. [FACT/USER VERIFIED]

* **[단일 진실 공급원(SSOT) 기반 동적 시리즈 네비게이션 체계]:**
  * **v2까지의 결함:** 개별 HTML 본문에 이전/다음 글 링크를 수동 삽입하여 연재 도중 순서 변경이나 미래 글 작성 시 전체 글을 수동 수정해야 했음.
  * **현재 체계:**
    1. 본문에는 오직 `<nav class="series-nav"></nav>` 컨테이너만 선언.
    2. 지식 베이스의 `_series.yaml`과 동기화된 `_series.json` 및 Blogger Label 피드를 테마 JS가 실시간 조회.
    3. '📌 시리즈 목록' 탭(정규 순서 이전/다음 카드 + 챕터별 아코디언 목차)과 '✨ 관심이 있을 만한 글' 탭을 100% 동적으로 분리 렌더링.
    4. 개별 문서에는 `Next Slug`, `series_total` 등 미래 예측 메타데이터를 영구 금지하고 오직 `series_id`만 유지. [FACT]

* **[양방향 동기화 피드백 시스템 (`/2_피드백`)]:**
  * **기존 한계:** 피드백이 Knowledge 파일에만 반영되거나, 이미 생성된 HTML 초안을 별도로 수동 수정해야 하여 지식 베이스와 블로그 게시글 내용 간 불일치가 발생.
  * **현재 체계:** `/2_피드백` 실행 시 타겟 `Knowledge/` 파일뿐만 아니라 이미 작성된 `Blog_Posts/` 하위 HTML 파일도 동시 감지하여 양방향으로 동기화 수정.
  * **경험 충실성 원칙:** 사용자의 실제 피드백에 수식어를 붙여 자의적으로 과대포장("효율 극대화", "완벽 장악" 등)하지 않고 진술된 사실 그대로만 담백하게 기술. [FACT]

* **[이미지 라이프사이클 관리 및 캐시 버스팅 고도화]:**
  * **버전 보존:** 이미지 수정 시 기존 파일을 덮어쓰지 않고 `{slug}-img1_v1.jpg`, `_v2.jpg` 형태로 버전 히스토리를 보존.
  * **톤앤매너 계승 (`ImagePaths`):** 시리즈 포스팅 생성 시 직전 편의 메인 이미지 경로를 `generate_image`의 `ImagePaths`로 전달하여 3D 테크 에디토리얼 스타일의 시각적 일관성 유지.
  * **캐시 버스팅:** `<img> src` 뒤에 `?v={YYYYMMDD_HHmm}` 쿼리스트링을 자동 부여하고 jsDelivr Purge API를 호출하여 블로그스팟 및 브라우저 캐시 고착을 방지. [FACT]

---

#### 🛠️ Procedures (현재 v3.1+ 실전 워크플로우 종합 비교)

| 단계 | 스킬 커맨드 | 1~2세대 방식 | 현재(v3.1+) 실전 처리 절차 |
|:---:|---|---|---|
| **0단계** | `/5_블로그추가` | 수동 레포 생성 및 설정 | 신규 블로그 페르소나(`Instructions/Personas/{blog}.md`) 생성, 4대 정적 페이지(About, Privacy, Contact, Terms) 테마 중립적 자동 렌더링, Data Plane 초기화 및 Git 커밋 |
| **1단계** | `/1_주제` | 자연어 검색 및 단일 파일 저장 | 웹 리서치(공식문서 우선), 타겟 블로그 `Knowledge/`에 `Experimental` 패키지 생성, `_index.md` 및 `_series.yaml`/`_series.json` 자동 갱신, Missing Info 보고 후 Git Push |
| **2단계** | `/2_피드백` | 지식 파일만 텍스트 갱신 | 사용자 테스트 결과 반영, `Verified` 상태 승격, **지식 베이스 + HTML 초안 양방향 실시간 동기화**, Git Push |
| **3단계** | `/3_게시글` | 수동 이미지 복붙 + 인라인 스타일 | Median UI 네이티브 클래스 적용, 본문 인라인 스타일 배제, `<nav class="series-nav">` 삽입, **Mermaid 다이어그램(580px 표준) 지원**, 3D 메인 이미지 자동 생성(`ImagePaths` 일관성 유지) 및 버전 관리, CDN Purge API 호출 |
| **4단계** | `/4_발행완료` | 단순 텍스트 상태 변경 | 블로그스팟 발행 후 `blog_published: true` 갱신, 구조화된 커밋 메시지(`feat(publish): ...`) 생성 및 Git Push |

---

#### 🐛 Errors & Solutions (버전별 주요 장애 및 극복 내역)

* **[Mermaid 다이어그램 크기 축소 및 폰트 왜곡 문제 (2026-08 말 해결)]**
  * **증상:** Median UI 테마에서 Mermaid 다이어그램이 컨테이너 가로폭(800px)에 맞춰 억지로 늘어나면서 폰트 크기가 불균일해지거나 텍스트가 잘리는 현상 발생.
  * **원인:** SVG viewBox와 CSS width/max-width 간의 비율 불일치 및 전역 스타일 상속 간섭.
  * **해결법:** Mermaid 다이어그램 너비를 580px 자연 너비로 표준화하고, 기본 폰트 크기를 clamp(6px/13px)로 정규화하며, 클릭 시 92vw×88vh 극장형 고해상도 Lightbox 모달에서 원본 벡터로 확대되도록 테마 스크립트 패치 완료. 모바일 뷰포트(360px~412px) 실기기에서도 정상 렌더링 검증 완료. [FACT/USER VERIFIED]

* **[Blogger XML 엔티티 파싱 오류 및 Mermaid CDATA 충돌 (2026-08 말 해결)]**
  * **증상:** Blogger 테마 XML 에디터에서 Mermaid 스크립트 및 스타일 내부의 특수문자(`<`, `>`, `&`)로 인해 템플릿 저장 시 파싱 에러 발생.
  * **원인:** Blogger 템플릿 엔진의 엄격한 XML 구문 검사.
  * **해결법:** Mermaid 스타일과 인라인 스크립트를 `//<![CDATA[ ... //]]>` 블록으로 안전하게 래핑하여 XML 엔티티 오류를 완전히 해소함. [FACT]

* **[지식 베이스와 HTML 초안의 수정 불일치 (2026-08 중순 해결)]**
  * **증상:** 피드백이나 코드 수정 시 지식 파일만 고쳐지고 이미 생성된 HTML 본문이 누락되어 구버전 코드가 그대로 발행되는 위험.
  * **원인:** 단방향 지식 갱신 설계.
  * **해결법:** `/2_피드백` 스킬에 `blog_draft_path`를 감지하여 HTML 본문도 함께 맥락에 맞게 수정하는 양방향 동기화 규칙(Rule 1, Rule 4) 확립. [FACT]

---

#### 💬 Experiences & Tips (실전 운영 경험)

* [OPINION] 1세대(단순 텍스트 변환)부터 현재(v3.1+ 분산 아키텍처 및 Mermaid/동적 탭 통합)까지의 시스템 진화는, "AI에게 자율성을 주는 것보다 에이전트의 동작 영역을 엄격한 스킬과 가드레일로 통제하는 것"이 파이프라인의 완성도를 결정한다는 사실을 증명한다.
* [OPINION] 특히 Mermaid 다이어그램의 테마 내재화와 극장형 Lightbox 모달의 도입은, 텍스트와 코드 위주의 기술 블로그에서 복잡한 시스템 설계를 시각적으로 전달하는 생산성을 비약적으로 끌어올렸다.
* [OPINION] 시리즈 네비게이션을 SSOT 원칙(`_series.yaml`/`_series.json`)으로 일원화하고 본문 하드코딩 링크를 영구 제거한 조치는, 장기 연재물(WebTranslator 21편 등)을 관리할 때 발생하던 유지보수 비용을 거의 0에 가깝게 줄여주었다.

---

#### ❓ Missing Info (검증 완료 및 보류 항목)

* [x] **Mermaid 다이어그램 모바일 뷰포트(360px~412px) 실기기 렌더링 검증** — Verified 2026-09-01 (580px 표준 너비 및 92vw×88vh Lightbox 모달의 모바일 실기기 정상 렌더링 확인 완료)
* [ ] **제2호 블로그 개설 시 `/5_블로그추가` 스킬 엔드투엔드 검증** — 보류 (2호 블로그 개설 시점까지 연기)

---

#### 🏷️ Tags

Antigravity, 블로그-자동화, 시스템-진화, Mermaid, SSOT, 동적-네비게이션, 양방향-동기화, 분산-아키텍처, 가드레일, Blogger, Median-UI, Lightbox-모달

===== KNOWLEDGE PACKAGE END =====

---
## 📝 Feedback History

### 2026-09-01 — Test Result: PASS
* **환경:** Windows 11, Antigravity IDE v2.0 (Gemini 3.7 Flash), 모바일 실기기 (360px~412px 뷰포트)
* **검증된 단계:**
  1. Mermaid 다이어그램 렌더링 규격(580px 표준 너비 및 극장형 Lightbox 모달)의 모바일 실기기(360px~412px) 렌더링 정상 동작 확인.
  2. v3 로드맵 예정 항목(5개 항목)의 실천 현황 비교 분석 정리 반영.
* **보류된 항목:** 제2호 블로그 개설 및 `/5_블로그추가` 실전 엔드투엔드 검증 (향후 2호 블로그 개설 시점으로 연기).
* **Status 변경:** Experimental → Verified
