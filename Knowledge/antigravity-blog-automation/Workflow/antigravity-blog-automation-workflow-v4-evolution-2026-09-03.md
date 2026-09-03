---
topic: antigravity-blog-automation-workflow-v4
title_kr: "[블로그 포스팅 자동화 구축기 #4] 아키텍처 진화와 Waline 댓글 시스템 연동"
category: Workflow
sub_category: System-Evolution-Comparison
version: 2026-09-03
status: Verified
created_date: 2026-09-01
last_modified: 2026-09-03
language: KR
tags: [Antigravity, 블로그-자동화, 시스템-진화, Mermaid, 동적-네비게이션, 시리즈-카탈로그, 양방향-동기화, 분산-아키텍처, Waline, 댓글시스템, Vercel, Neon-PostgreSQL, Cloudflare-Turnstile, Blogger, Median-UI]
sources_count: 10
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: antigravity-blog-automation
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** antigravity-blog-automation-workflow-v4
* **Title_KR:** [블로그 포스팅 자동화 구축기 #4] 아키텍처 진화와 Waline 댓글 시스템 연동
* **Category:** Workflow
* **Sub-Category:** System-Evolution-Comparison
* **Version:** 2026-09-03
* **Status:** Verified
* **Date:** 2026-09-03
* **Language:** KR

---

#### 📚 Sources & Confidence

* [★★★★★] BlogDocs v3.1+ 아키텍처 및 소스 코드: `.agents/skills/` (1~5), `.agents/rules/`, `Instructions/Personas/`
* [★★★★★] 이전 파이프라인 지식 문서:
  - 1세대: `antigravity-blog-automation/Workflow/antigravity-blog-automation-2026-06-19.md`
  - 2세대: `antigravity-blog-automation/Workflow/antigravity-blog-automation-v2-pipeline-2026-08-17.md`
  - 3세대: `antigravity-blog-automation/Workflow/antigravity-blog-automation-v3-architecture-2026-08-18.md`
* [★★★★★] 시리즈 네비게이션 가이드 및 중앙 카탈로그: `Instructions/series_navigation_plan.md`, `Knowledge/_series.yaml`, `Knowledge/_series.json`
* [★★★★★] Median UI 테마 XML 및 동적 스크립트: `Instructions/Personas/Core-Archive_Theme_Median-UI-v1.7.0-Custom.xml` (Mermaid.js 1:1 자연 배율 및 Lightbox 줌 모달, Waline 댓글 위젯 및 Turnstile 핫픽스, 메인 슬라이더 연동 커밋)
* [★★★★★] Waline v3 공식 문서 및 배포 템플릿: `https://waline.js.org`, `@waline/client@v3.3.0`
* [★★★★★] Vercel Serverless Function, Neon Serverless PostgreSQL 콘솔, Cloudflare Turnstile 보안 설정
* [★★★★★] 사용자 실전 검증 스크린샷 15종: `images/2026-09/antigravity-blog-automation-workflow-v4-scr01~15.png`
* [★★★★★] 최근 실전 포스팅 구축 및 검증 이력: `windows-speaker-changer` (2026-09-01), `unity6-urp-rendergraph` (2026-08-31)
* [★★★★★] 사용자 실전 검증: Mermaid 다이어그램 1:1 자연 배율 및 모바일 실기기 검증 완료, Waline 댓글 인프라 배포·테이블 생성·Turnstile 무한대기 핫픽스·다크모드 CSS 커스텀·Gmail 실시간 알림 엔드투엔드 검증 완료

---

#### 🔑 Core Concepts (핵심 개념)

* **[세대별 파이프라인 진화 총괄 비교 (v1 → v2 → v3 → 현재 상태)]:**
  * **1세대 (v1, 2026-06-19):** NotebookLM 연동 실패 후 Antigravity 단독 루프로 전환. 리서치→추출→HTML 생성 3단계 단순 텍스트 파이프라인. 지시서 체계 및 하네스가 미흡하여 자연어 프롬프트에 의존했고, 이미지 수동 복붙으로 로컬-블로그 상태 불일치 발생.
  * **2세대 (v2, 2026-08-17):** 4대 프롬프트 지시서(`01`~`04`) 및 4대 `@멘션` 트리거(`@주제`, `@피드백`, `@게시글`, `@발행완료`) 도입. GitHub + jsDelivr CDN 자동 이미지 연동 및 캐시 Purge 구축. 하지만 프롬프트 지시서 상시 로드로 인한 토큰 낭비, 자연어 오발동, 미완성 구상 메모로 인한 AI의 `Next Slug` 날조(404 에러) 및 단일 레포의 확장성 한계가 드러남.
  * **3세대 (v3, 2026-08-18):** Control Plane(`BlogDocs`)과 Data Plane(`Blog_Core-Archive`) 분산 아키텍처 확립. Antigravity 2.0 Native Skills(5대)와 슬래시 커맨드(`/1_주제` ~ `/5_블로그추가`)로 전면 개편하여 오발동 원천 차단. 상시 Rules 경량화 vs Skills 온디맨드 스펙 분리. Blogger Feed 기반 동적 탭 네비게이션 도입.
  * **현재 상태 (v3.1+, 2026-09-03):** Mermaid.js 동적 다이어그램 렌더링 엔진 통합(1:1 자연 배율과 92vw×88vh 극장형 모달), `_series.yaml`/`_series.json` 중앙 집중식 카탈로그 정착, `/2_피드백` 지식-HTML 양방향 동기화, 이미지 버전 관리(`_v1`, `_v2`) 및 톤앤매너 계승, 캐시 버스팅 정밀화에 더해 **Waline 기반 독립형 서버리스 댓글 시스템과 무인 봇 방어 및 슬라이더 연동까지 테마에 완벽히 내재화**. [FACT/USER VERIFIED]

* **[v3 로드맵 예정 항목 실천 현황 비교 분석]:**
  1. **테마 XML 레거시/하드코딩 요소 정리:** Mermaid.js 통합 시 CDATA 래핑, XML 엔티티 파서 호환, viewBox 스케일링 패치 완료. Waline 주입 시 Blogger XML 주석(`--`) 충돌을 피하기 위해 `<b:includable>` 블록 ID 스위칭 기법 적용.
  2. **`/5_블로그추가` 2호 블로그 엔드투엔드 실전 검증:** 제2호 블로그 개설 계획이 아직 확정되지 않아 실전 테스트를 보류하고 향후 개설 시점으로 연기 유지. [USER VERIFIED]
  3. **가이드라인(Rules/Skills) 지속 고도화:** `always-link-files`, `always-use-primitive-output`, `blog-harness-rules` 3대 초경량 가드레일 정착. 이미지 버전 히스토리(`_v1`, `_v2`), `ImagePaths` 톤 계승, `?v=` 캐시버스팅 명문화 완료. [FACT]
  4. **구글 Blogger 기본 댓글 한계 극복 및 독립형 댓글 연동:** 기본 Blogger 댓글의 구글 로그인 강제 및 게스트 불가 한계를 극복하기 위해 **Waline 기반 독립형 서버리스 댓글 시스템(Vercel + Neon + Cloudflare Turnstile)**을 성공적으로 도입하고 테마 내재화 완료. [FACT/USER VERIFIED]
  5. **본문 보조 이미지 및 다이어그램 추천 파이프라인:** Mermaid.js 동적 렌더링 엔진 내재화를 통해 복잡한 아키텍처/흐름도를 텍스트 코드로 즉시 생성·시각화하는 방식으로 보조 이미지 수요를 대폭 대체 및 고도화 완료. [FACT]

* **[Mermaid.js 동적 다이어그램 & 극장 스케일 Lightbox 줌 모달 통합]:**
  * **기존 한계:** 복잡한 시스템 구조나 아키텍처를 설명할 때 외부 그래픽 툴로 이미지를 일일이 생성하거나 AI 썸네일에 의존해야 했고, 본문 내 텍스트/도식의 가독성과 수정 편의성이 떨어졌음.
  * **현재 구축된 아키텍처:**
    1. **Mermaid.js 동적 렌더링:** HTML 본문에 표준 Markdown/HTML `<pre class="mermaid">` 블록을 작성하면 브라우저 클라이언트가 즉시 SVG 벡터로 렌더링.
    2. **1:1 자연 배율(Natural Scale) 및 반응형 렌더링:** 기존의 가로폭 580px 강제 고정 방식을 탈피하여, 본문 너비에 맞춰 억지로 늘어나지 않고 다이어그램 고유 크기(`width: auto !important`, `max-width: 100% !important`)를 자연스럽게 유지하도록 테마 스타일 패치 완료.
    3. **극장형 Lightbox 모달 (92vw × 88vh):** 복잡하거나 가로가 긴 다이어그램 클릭 시 화면의 92vw × 88vh 크기 뷰어로 1:1 고해상도 벡터 확대되어 데스크톱 및 모바일(360px~412px 뷰포트) 실기기 모두에서 쾌적하게 열람 가능. [FACT/USER VERIFIED]

* **[중앙 집중식 카탈로그 기반 동적 시리즈 네비게이션 체계]:**
  * **v2까지의 결함:** 개별 HTML 본문에 이전/다음 글 링크를 수동 삽입하여 연재 도중 순서 변경이나 미래 글 작성 시 전체 글을 수동 수정해야 했음.
  * **현재 체계:**
    1. 본문에는 오직 `<nav class="series-nav"></nav>` 컨테이너만 선언.
    2. 지식 베이스의 단일 기준 파일인 `_series.yaml`과 동기화된 `_series.json` 및 Blogger Label 피드를 테마 JS가 실시간 조회.
    3. '📌 시리즈 목록' 탭(정규 순서 이전/다음 카드 + 챕터별 아코디언 목차)과 '✨ 관심이 있을 만한 글' 탭을 동적으로 분리 렌더링.
    4. 개별 문서에는 `Next Slug`, `series_total` 등 미래 예측 메타데이터를 일절 작성하지 않고 오직 `series_id`만 유지. [FACT]

* **[양방향 동기화 피드백 시스템 (`/2_피드백`)]:**
  * **기존 한계:** 피드백이 Knowledge 파일에만 반영되거나, 이미 생성된 HTML 초안을 별도로 수동 수정해야 하여 지식 베이스와 블로그 게시글 내용 간 불일치가 발생.
  * **현재 체계:** `/2_피드백` 실행 시 타겟 `Knowledge/` 파일뿐만 아니라 이미 작성된 `Blog_Posts/` 하위 HTML 파일도 동시 감지하여 양방향으로 동기화 수정.
  * **경험 충실성 원칙:** 사용자의 실제 피드백에 수식어를 붙여 자의적으로 과대포장하지 않고 진술된 사실 그대로만 담백하게 기술. [FACT]

* **[이미지 라이프사이클 관리 및 캐시 버스팅 고도화]:**
  * **버전 보존:** 이미지 수정 시 기존 파일을 덮어쓰지 않고 `{slug}-img1_v1.jpg`, `_v2.jpg` 형태로 버전 히스토리를 보존.
  * **톤앤매너 계승 (`ImagePaths`):** 시리즈 포스팅 생성 시 직전 편의 메인 이미지 경로를 `generate_image`의 `ImagePaths`로 전달하여 3D 테크 에디토리얼 스타일의 시각적 일관성 유지.
  * **캐시 버스팅:** `<img> src` 뒤에 `?v={YYYYMMDD_HHmm}` 쿼리스트링을 자동 부여하고 jsDelivr Purge API를 호출하여 블로그스팟 및 브라우저 캐시 고착을 방지. [FACT]

* **[Waline 기반 독립형 서버리스 댓글 시스템 아키텍처 및 테마 내재화]:**
  * **도입 배경:** 구글 기본 댓글의 구글 로그인 강제, 게스트 덧글 불가, 스팸 취약성 문제를 해결하고 방문자 참여 장벽을 완화하기 위해 서버리스 Waline v3를 도입.
  * **서버리스 인프라 구성:**
    1. **백엔드 (Compute):** Vercel Serverless Function (무료 티어, 공식 example 템플릿 배포).
    2. **데이터베이스 (Storage):** Neon Serverless PostgreSQL (외부 연결 독립 클라우드 DB).
    3. **무인 봇/스팸 방어:** Cloudflare Turnstile (`TURNSTILE_KEY`, `TURNSTILE_SECRET`, `SECURE_DOMAINS` 연동). 퍼즐 풀기 없이 백그라운드 무인 챌린지 수행.
    4. **관리자 실시간 알림:** Gmail SMTP 연동 (`noog.sub@gmail.com`)으로 새 댓글 작성 시 즉각 이메일 수신.
  * **Median UI 1.7 테마 완전 일체화:**
    1. **다크/라이트 모드 자동 연동:** 테마의 다크모드 식별자(`body.nB[data-theme="dark"]`)에 맞춰 테마 내장 CSS 변수(`--contentBg`, `--contentBg-alt`, `--contentC`, `--contentBd-color`, `--themeLink` 등)를 Waline CSS 변수(`--waline-bgcolor`, `--waline-color` 등)에 1:1 매핑하여 이질감 없는 자동 테마 전환 구현.
    2. **미니멀 UI 커스텀 (Concept 1):** 게스트 입력 폼(Nickname, E-Mail, Website)의 상단 라벨 텍스트를 숨기고 가로 3분할 배치 후 `MutationObserver`로 내부 Placeholder 주입. 45px 아바타 크기 고정, 대댓글 점선 인용선(`.wl-card .wl-quote`) 제거, OS/브라우저 메타 정보(`.wl-meta`) 숨김 처리. 포커스 시 배경 투명 유지로 흰색 번짐 버그 원천 차단.
    3. **Turnstile 플로팅 팝오버 (방안 A):** 150px×140px 크기의 정사각형 Turnstile 위젯이 툴바를 아래로 밀어내지 않도록 절대좌표(`position: absolute`) 플로팅 카드로 전환하여 Submit 버튼 상단에 띄우고 빈 상태에서는 완전 은닉 처리.
  * **블로그 전역 위젯 및 메인 슬라이더 동적 연동:**
    1. **메인 슬라이더 4번 하이브리드 자동 승격:** Waline API로 최근 글 댓글 수를 비동기 조회하여 댓글이 1개 이상 달린 글이 있으면 **`화제의 글`**로 자동 승격 노출. 댓글이 모두 0개일 때는 0개 글을 억지로 노출하지 않고 최근 30일 조회수 2위 글을 **`추천글`** 뱃지로 유연하게 전환(fallback). 통신 지연 방지를 위해 800ms 타임아웃 방어 코드 적용.
    2. **인기글 집계 기간 30일(`LAST_MONTH`) 통일:** 메인 슬라이더 및 사이드바 인기글 위젯의 집계 기간을 블로그스팟 공식 파라미터 `LAST_MONTH`로 일원화.
    3. **슬라이더 3종 중복 방지 알고리즘:** 최신글과 인기글 1위가 겹치면 인기글 슬라이드는 2위 글을, 추천글 슬라이드는 3위 글을 순차 배정하여 3개 슬라이드가 항상 고유한 글을 유지하도록 처리.
  * **관리자 콘솔 및 로딩 정책:**
    1. **관리자 기능 복구:** 어드민 로그인 시에만 작동하는 댓글 수정/삭제 버튼 및 승인/대기 관리 바 복구, 댓글 영역 상단에 Vercel 어드민 콘솔(`/ui`) 바로가기 버튼 조건부 렌더링.
    2. **초기 즉시 로딩 정책 확정:** Vercel/Neon 무료 서버리스 환경 특유의 콜드 스타트(1~3초)를 감안하여, 스크롤 지연 로딩(Lazy Load) 대신 페이지 오픈 즉시 초기화를 시작하여 방문자가 댓글 로딩 지연을 겪지 않도록 조치. [FACT/USER VERIFIED]

---

#### 🛠️ Procedures (실전 워크플로우 및 댓글 시스템 구축 절차)

##### 1. 현재 v3.1+ 실전 워크플로우 종합 비교

| 단계 | 스킬 커맨드 | 1~2세대 방식 | 현재(v3.1+) 실전 처리 절차 |
|:---:|---|---|---|
| **0단계** | `/5_블로그추가` | 수동 레포 생성 및 설정 | 신규 블로그 페르소나(`Instructions/Personas/{blog}.md`) 생성, 4대 정적 페이지(About, Privacy, Contact, Terms) 테마 중립적 자동 렌더링, Data Plane 초기화 및 Git 커밋 |
| **1단계** | `/1_주제` | 자연어 검색 및 단일 파일 저장 | 웹 리서치(공식문서 우선), 타겟 블로그 `Knowledge/`에 `Experimental` 패키지 생성, `_index.md` 및 `_series.yaml`/`_series.json` 자동 갱신, Missing Info 보고 후 Git Push |
| **2단계** | `/2_피드백` | 지식 파일만 텍스트 갱신 | 사용자 테스트 결과 반영, `Verified` 상태 승격, **지식 베이스 + HTML 초안 양방향 실시간 동기화**, Git Push |
| **3단계** | `/3_게시글` | 수동 이미지 복붙 + 인라인 스타일 | Median UI 네이티브 클래스 적용, 본문 인라인 스타일 배제, `<nav class="series-nav">` 삽입, **Mermaid 다이어그램(1:1 자연 배율) 지원**, 3D 메인 이미지 자동 생성(`ImagePaths` 일관성 유지) 및 버전 관리, CDN Purge API 호출 |
| **4단계** | `/4_발행완료` | 단순 텍스트 상태 변경 | 블로그스팟 발행 후 `blog_published: true` 갱신, 구조화된 커밋 메시지(`feat(publish): ...`) 생성 및 Git Push |

##### 2. Waline 댓글 시스템 인프라 구축 및 테마 내재화 7단계 실전 절차

1. **Cloudflare Turnstile 위젯 생성:**
   - Cloudflare 대시보드에서 Turnstile 위젯 생성 후 `Site Key`와 `Secret Key` 발급.
   - Domain(s) 항목에 실제 블로그 도메인(`blog.noog.kim`)과 함께 Vercel 대표 도메인(`blog-waline.vercel.app`)을 동시 등록.
2. **Vercel 원클릭 서버리스 배포:**
   - Waline 전체 소스코드(모노레포)를 통째로 가져오면 빌드 실패가 발생하므로, 서버 배포 전용 공식 example 템플릿 레포지토리로 Vercel 프로젝트 생성.
3. **Neon PostgreSQL 스토리지 연동 및 계정 권한 확보:**
   - Vercel 내장 생성 시 발생하는 가상 계정 격리를 방지하기 위해, [neon.tech](https://neon.tech/)에서 직접 GitHub 외부 연결 로그인을 수행하여 실제 스토리지 접근권 및 제어권 확보.
   *(관련 실전 스크린샷: `images/2026-09/antigravity-blog-automation-workflow-v4-scr01-vercel-storage.png`, `images/2026-09/antigravity-blog-automation-workflow-v4-scr03-neon-shadow-account.png`, `images/2026-09/antigravity-blog-automation-workflow-v4-scr04-neon-github-oauth.png`)*
4. **Neon SQL Editor 테이블 수동 초기화:**
   - Waline은 PostgreSQL 연결 시 테이블을 자동 생성하지 않으므로, Neon SQL Editor에서 3개 테이블(`wl_comment`, `wl_counter`, `wl_users`) 및 시퀀스 DDL 쿼리를 일괄 실행(Run).
5. **Vercel 환경 변수 등록 및 재배포(Redeploy):**
   - Vercel `[Settings] -> [Environment Variables]`에 필수 변수 등록:
     - DB: `POSTGRES_URL` (단일 연결 문자열), `PG_SSL=true`
     - 봇 방어: `TURNSTILE_KEY`, `TURNSTILE_SECRET`, `SECURE_DOMAINS` (블로그 도메인, Vercel 고정 도메인)
     - 알림: `SMTP_SERVICE=Gmail`, `SMTP_USER=noog.sub@gmail.com`, `SMTP_PASS=(16자리 앱비밀번호)`, `AUTHOR_EMAIL=noog.sub@gmail.com`
   - 변수 저장 후 Deployments 탭에서 Redeploy 수행.
6. **최초 관리자(Admin) 계정 등록:**
   - Vercel 대표 도메인의 관리자 경로(`https://.../ui`)에 접속하여 최초 회원가입 수행 (최초 가입자가 최고 관리자로 자동 지정됨).
7. **Blogger 테마 XML(`Core-Archive_Theme_Median-UI-v1.7.0-Custom.xml`) 주입:**
   - 테마의 `commentPicker` 블록에 Waline 컨테이너 및 ESM 스크립트 주입.
   - Median UI 전용 다크/라이트 모드 CSS 매핑, Turnstile 중복 렌더링 핫픽스, 플로팅 팝오버(방안 A), 슬라이더 4번 실시간 비동기 연동 및 중복 방지 스크립트 반영 후 테마 저장.
   *(관련 실전 스크린샷: `images/2026-09/antigravity-blog-automation-workflow-v4-scr07-waline-dark-initial.png`, `images/2026-09/antigravity-blog-automation-workflow-v4-scr08-waline-light-initial.png`, `images/2026-09/antigravity-blog-automation-workflow-v4-scr13-concept1-desktop.png`, `images/2026-09/antigravity-blog-automation-workflow-v4-scr14-concept1-mobile.png`)* [FACT/USER VERIFIED]

---

#### 📷 실전 첨부 스크린샷 카탈로그 (게시글 삽입 매핑 가이드)

블로그 포스팅 생성 시 본문 각 맥락에 맞춰 삽입할 검증 스크린샷 15종 목록:

| 번호 | 이미지 파일명 | 설명 및 본문 배치 위치 |
|:---:|---|---|
| **scr01** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr01-vercel-storage.png` | Vercel 내장 Storage 탭에서 Neon DB 생성 시 초기 화면 |
| **scr02** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr02-turnstile-blocked.png` | Cloudflare Turnstile에 Vercel 도메인 미등록 시 발생하는 봇 차단 에러 |
| **scr03** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr03-neon-shadow-account.png` | Vercel 'Open in Neon' 클릭 시 인증 메일 미수신 및 가상 계정 격리 현상 |
| **scr04** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr04-neon-github-oauth.png` | Neon 공식 콘솔에 GitHub 외부 연동 계정으로 직접 로그인하여 스토리지 제어권 확보한 화면 |
| **scr05** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr05-waline-light-glare.png` | 초기 Waline 연동 시 다크 테마와 맞지 않게 하얗게 번지는 댓글창 UI |
| **scr06** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr06-waline-theme-mismatch.png` | Median UI 다크모드 선택자(`body.nB[data-theme='dark']`) 불일치로 인한 스타일 미적용 현상 |
| **scr07** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr07-waline-dark-initial.png` | 테마 변수 매핑 직후의 초기 다크모드 댓글창 렌더링 화면 |
| **scr08** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr08-waline-light-initial.png` | 테마 변수 매핑 직후의 초기 라이트모드 댓글창 렌더링 화면 |
| **scr09** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr09-avatar-align-issue.png` | 프로필 아이콘과 닉네임 정렬 불일치 및 배지 겹침 이슈 화면 |
| **scr10** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr10-guest-inputs-meta.png` | 게스트 3분할 입력칸 및 브라우저/OS 메타 정보 표시 이슈 화면 |
| **scr11** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr11-admin-ui-exposed.png` | 일반 사용자 화면에 관리자용 승인/수정/삭제 버튼이 노출되던 화면 |
| **scr12** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr12-focus-white-bug.png` | 댓글 텍스트박스 클릭(포커스) 시 흰색 박스로 변하는 버그 및 레이아웃 틀어짐 화면 |
| **scr13** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr13-concept1-desktop.png` | Concept 1 미니멀리스트 적용 후 데스크톱 최종 레이아웃 |
| **scr14** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr14-concept1-mobile.png` | Concept 1 미니멀리스트 적용 후 모바일/다크 테마 최종 레이아웃 |
| **scr15** | `images/2026-09/antigravity-blog-automation-workflow-v4-scr15-guest-label-and-email.png` | 게스트 폼 라벨 숨김 및 Placeholder 주입 요구사항 검토 화면 |

---

#### 🐛 Errors & Solutions (버전별 주요 장애 및 극복 내역)

* **[Mermaid 다이어그램 폰트 왜곡 및 뷰포트 스케일링 (2026-08 말 해결)]**
  * **증상:** Median UI 테마에서 Mermaid 다이어그램이 컨테이너 가로폭(800px)에 맞춰 억지로 늘어나면서 폰트 크기가 불균일해지거나 텍스트가 잘리는 현상 발생.
  * **원인:** SVG viewBox와 CSS width 간의 비율 불일치 및 강제 가로폭 고정 스타일.
  * **해결법:** 임시 가로폭 고정 방식을 탈피하여 다이어그램 고유 1:1 자연 배율(`width: auto !important`, `max-width: 100% !important`)로 정규화하고, 본문 폰트를 상속하도록 정리. 클릭 시 92vw×88vh 극장형 고해상도 Lightbox 모달에서 원본 벡터로 확대되도록 테마 스크립트 패치 완료. 모바일 뷰포트(360px~412px) 실기기에서도 정상 렌더링 검증 완료. [FACT/USER VERIFIED]

* **[Blogger XML 엔티티 파싱 오류 및 Mermaid CDATA 충돌 (2026-08 말 해결)]**
  * **증상:** Blogger 테마 XML 에디터에서 Mermaid 스크립트 및 스타일 내부의 특수문자(`<`, `>`, `&`)로 인해 템플릿 저장 시 파싱 에러 발생.
  * **원인:** Blogger 템플릿 엔진의 엄격한 XML 구문 검사.
  * **해결법:** Mermaid 스타일과 인라인 스크립트를 `//<![CDATA[ ... //]]>` 블록으로 안전하게 래핑하여 XML 엔티티 오류를 완전히 해소함. [FACT]

* **[지식 베이스와 HTML 초안의 수정 불일치 (2026-08 중순 해결)]**
  * **증상:** 피드백이나 코드 수정 시 지식 파일만 고쳐지고 이미 생성된 HTML 본문이 누락되어 구버전 코드가 그대로 발행되는 위험.
  * **원인:** 단방향 지식 갱신 설계.
  * **해결법:** `/2_피드백` 스킬에 `blog_draft_path`를 감지하여 HTML 본문도 함께 맥락에 맞게 수정하는 양방향 동기화 규칙(Rule 1, Rule 4) 확립. [FACT]

* **[Vercel 모노레포 통짜 빌드 시 `dist` 누락 에러 (2026-09-01 해결)]**
  * **증상:** `Error: No Output Directory named "dist" found after the Build completed.`
  * **원인:** Waline 전체 소스코드(모노레포)를 Vercel에 직접 빌드하여 서버리스 산출물 경로를 찾지 못함.
  * **해결법:** 서버 배포용으로 정돈된 공식 example 템플릿 전용 배포 경로로 교체하여 빌드 과정 없이 즉시 배포 성공. [FACT]

* **[Vercel 내장 Neon 스토리지의 가상 섀도우 계정 격리 문제 (2026-09-01 해결)]**
  * **증상:** Vercel 대시보드에서 원클릭으로 생성된 Neon DB에 대해 'Open in Neon' 클릭 시 인증 메일이 오지 않고 계정 콘솔 접근이 불가능해짐. *(스크린샷: `images/2026-09/antigravity-blog-automation-workflow-v4-scr03-neon-shadow-account.png`)*
  * **원인:** Vercel이 플랫폼 내부에서 사용자 이메일과 무관한 GitHub OAuth 가상(Shadow) 계정으로 DB를 프로비저닝하여 소유권 매핑 실패.
  * **해결법:** 사용자가 [neon.tech](https://neon.tech/) 공식 홈페이지에서 GitHub 외부 연동 로그인을 직접 수행하여 실제 스토리지를 확인하고 콘솔 제어권을 정상 확보함. *(스크린샷: `images/2026-09/antigravity-blog-automation-workflow-v4-scr04-neon-github-oauth.png`)* [FACT/USER VERIFIED]

* **[Vercel 어드민 회원가입 시 403 Forbidden 차단 에러 (2026-09-01 해결)]**
  * **증상:** `/ui` 관리자 가입 화면에서 Register 클릭 시 403 Forbidden 에러 발생.
  * **원인:** `SECURE_DOMAINS` 및 Turnstile 허용 도메인에 블로그 도메인만 등록하여, 정작 가입 요청이 발생하는 Vercel 고정 도메인의 API 접근을 보안 위반으로 차단. *(스크린샷: `images/2026-09/antigravity-blog-automation-workflow-v4-scr02-turnstile-blocked.png`)*
  * **해결법:** Vercel 고정 대표 도메인(Production URL)을 `SECURE_DOMAINS` 환경 변수와 Turnstile Domain 목록에 추가하여 해결. [FACT]

* **[PostgreSQL 테이블 스키마 미생성으로 인한 500 에러 (2026-09-01 해결)]**
  * **증상:** `500: relation "wl_users" does not exist` 발생하며 가입 불가.
  * **원인:** Waline 최신 버전은 PostgreSQL 연동 시 테이블을 자동으로 생성(Auto-sync)하지 않고 수동 생성을 요구함.
  * **해결법:** Neon 대시보드의 SQL Editor에서 3개 테이블(`wl_comment`, `wl_counter`, `wl_users`) 및 시퀀스 생성 SQL 쿼리를 일괄 실행하여 스키마 초기화 완료. [FACT]

* **[Blogger 테마 XML 내 HTML 주석(`--`) 파싱 에러 (2026-09-02 해결)]**
  * **증상:** `SAXParseException; The string "--" is not permitted within comments` 에러로 테마 저장 거부.
  * **원인:** Waline 코드를 백업하기 위해 `<!-- ... -->` 주석을 사용할 때 내부에 포함된 CSS 변수(`--waline...`)의 연속 대시(`--`)가 XML 주석 구문 규칙을 위반함.
  * **해결법:** 주석 대신 `<b:includable id='commentPicker_Waline_Backup'>` 및 `<b:includable id='commentPicker_Blogger_Default'>` 형태로 블록 ID를 전환하여 템플릿 에러 없이 안전하게 기능을 스위칭/백업함. [FACT]

* **[Cloudflare Turnstile 중복 렌더링 거부 및 1~2분 무한 대기 (2026-09-03 해결)]**
  * **증상:** 첫 댓글 작성 후 두 번째 작성 시 `Turnstile has already been rendered in this container` 발생하며 연속 작성 거부. 가로채기 핫픽스 적용 시 `Turnstile already has been loaded` 경고와 함께 1~2분 타임아웃 지연 발생.
  * **원인:** Waline 컴포넌트 갱신 시 이전 Turnstile 인스턴스를 정상 소멸시키지 않음. 또한 `Object.defineProperty`를 사용한 무리한 가로채기 코드가 Cloudflare `api.js` 내부 초기화를 방해하여 토큰 콜백이 유실됨.
  * **해결법:**
    1. `Object.defineProperty` 가로채기를 전면 삭제.
    2. `<script data-loaded='true' id='cf-turnstile-script' src='...'>` 사전 정적 로드로 중복 주입 경고 차단.
    3. 렌더링 호출 직전 공식 API인 `turnstile.remove(widgetId)`를 호출하여 이전 위젯을 정상 소멸시킨 후 새 위젯을 마운트하는 안전한 훅 구현으로 연속 덧글 즉시 작성 성공. [FACT/USER VERIFIED]

* **[Turnstile 사각형 박스의 툴바 레이아웃 파괴 (2026-09-03 해결)]**
  * **증상:** 150px×140px 크기의 Turnstile 캡차 상자가 인라인으로 삽입되면서 댓글 입력창 하단 툴바(아이콘, 글자 수, 전송 버튼)의 높이를 과도하게 확장하고 레이아웃을 망가뜨림.
  * **원인:** 인라인 플로우에 배치된 캡차 컨테이너의 고정 크기.
  * **해결법:** `.wl-captcha-container`에 `position: absolute`를 부여하여 Submit 버튼 상단에 떠 있는 다크 테마 플로팅 팝오버(방안 A)로 띄우고, 빈 상태에서는 숨김 처리하여 툴바 원형을 온전히 보존함. [FACT/USER VERIFIED]

* **[Blogger 템플릿 기간 설정 유효성 파싱 오류 (2026-09-03 해결)]**
  * **증상:** `The widget settings in widget with id PopularPosts00 is not valid` 에러 발생.
  * **원인:** 인기글 위젯의 30일 기간 파라미터로 비공식 명칭인 `LAST_30_DAYS`를 입력함.
  * **해결법:** 구글 블로그스팟 공식 스키마 파라미터인 `LAST_MONTH`로 수정하여 저장 정상화. [FACT]

---

#### 💬 Experiences & Tips (실전 운영 경험)

* [OPINION] 1세대(단순 텍스트 변환)부터 현재(v3.1+ 분산 아키텍처 및 Mermaid/동적 탭/Waline 통합)까지의 시스템 진화는, "AI에게 자율성을 주는 것보다 에이전트의 동작 영역을 엄격한 스킬과 가드레일로 통제하는 것"이 파이프라인의 완성도를 결정한다는 사실을 증명한다.
* [OPINION] Blogger의 기본 댓글 체계를 버리고 Vercel + Neon + Cloudflare Turnstile 기반의 Waline으로 전환한 것은 방문자의 진입 장벽(익명/게스트 허용)을 낮추면서도 무인 봇 스팸을 효과적으로 방어하는 최적의 홈랩형 서버리스 블로그 솔루션임을 확인했다.
* [OPINION] 특히 테마 XML 내부에서 CSS 변수 1:1 매핑을 통한 네이티브 다크모드 동기화와, 메인 슬라이더 4번의 실시간 Waline 카운트 기반 '화제의 글 / 추천글' 하이브리드 자동 승격 알고리즘은 외부 댓글 위젯 특유의 이질감을 지우고 블로그 전체의 완성도를 크게 끌어올렸다.
* [OPINION] 서버리스 환경(Vercel/Neon)의 콜드 스타트 지연을 고려하여 지연 로딩(Lazy Load) 대신 초기 즉시 로딩을 고수한 결정은, 방문자에게 "댓글 기능이 정상 동작하고 있다"는 신뢰감을 주는 중요한 UX 경험이었다.
* [OPINION] 시리즈 네비게이션을 중앙 집중식 카탈로그(`_series.yaml`/`_series.json`)로 일원화하고 본문 하드코딩 링크를 영구 제거한 조치는, 장기 연재물(WebTranslator 21편 등)을 관리할 때 발생하던 유지보수 비용을 거의 0에 가깝게 줄여주었다.

---

#### ❓ Missing Info (검증 완료 및 보류 항목)

* [x] **Mermaid 다이어그램 모바일 뷰포트(360px~412px) 실기기 렌더링 검증** — Verified 2026-09-01 (1:1 자연 배율 및 92vw×88vh Lightbox 모달의 모바일 실기기 정상 렌더링 확인 완료)
* [x] **독립형 서버리스 댓글 시스템(Waline + Vercel + Neon + Turnstile) 구축 및 테마 연동 완료** — Verified 2026-09-03 (인프라 배포, DB 테이블 초기화, Turnstile 핫픽스, Median UI 다크모드 커스텀, 슬라이더 동적 연동, 관리자 이메일 알림 연동 완료)
* [ ] **제2호 블로그 개설 시 `/5_블로그추가` 스킬 엔드투엔드 검증** — 보류 (2호 블로그 개설 시점까지 연기 유지)

---

#### 🏷️ Tags

Antigravity, 블로그-자동화, 시스템-진화, Mermaid, 동적-네비게이션, 시리즈-카탈로그, 양방향-동기화, 분산-아키텍처, Waline, 댓글시스템, Vercel, Neon-PostgreSQL, Cloudflare-Turnstile, Blogger, Median-UI

===== KNOWLEDGE PACKAGE END =====

---
## 📝 Feedback History

### 2026-09-03 — Test Result: PASS
* **환경:** Windows 11, Antigravity IDE (Gemini 3.8 Flash), Vercel Serverless, Neon PostgreSQL, Cloudflare Turnstile, Median UI v1.7.0
* **검증된 단계:**
  1. Waline v3 독립형 서버리스 댓글 시스템 인프라 구축 (Vercel + Neon PostgreSQL) 및 최초 관리자 권한 획득 완료.
  2. Cloudflare Turnstile 봇 방어 연동 및 중복 렌더링(`already been rendered`)·1~2분 타임아웃 핫픽스 완료.
  3. Median UI 1.7 다크/라이트 모드 테마 CSS 변수 완전 일체화 및 미니멀 UI(Concept 1, 3분할 폼, 아바타 교정, 플로팅 팝오버) 적용 완료.
  4. 메인 슬라이더 4번 Waline API 실시간 댓글 연동 및 하이브리드 자동 승격(`화제의 글` / 최근 30일 `추천글` fallback) 정상 동작 확인.
  5. 블로그스팟 인기글 집계 기간 최근 30일(`LAST_MONTH`) 통일 및 슬라이더 3종 중복 방지 알고리즘 검증 완료.
  6. 관리자 이메일(`noog.sub@gmail.com`) 실시간 댓글 알림(Gmail SMTP) 수신 검증 완료.
  7. 테마 XML 파일(`Core-Archive_Theme_Median-UI-v1.7.0-Custom.xml`) 수정 사항 GitHub 원격 푸시 완료.
  8. Mermaid 다이어그램 규격 정정: 580px 강제 고정 방식을 탈피하여 테마 구현에 맞춘 1:1 자연 배율(Natural Scale) 및 유연한 뷰포트 반응형 구조로 정착.
  9. 용어 정비: 'SSOT' 줄임말을 배제하고 '중앙 집중식 카탈로그(`_series.yaml`/`_series.json`) 기반 동적 시리즈 네비게이션'으로 알기 쉽게 서술.
  10. 실전 스크린샷 15종(`images/2026-09/antigravity-blog-automation-workflow-v4-scr01~15.png`) 아카이빙 및 게시글 삽입 매핑 가이드라인 구축.
* **보류된 항목:** 제2호 블로그 개설 및 `/5_블로그추가` 실전 엔드투엔드 검증 (향후 2호 블로그 개설 시점으로 연기 유지).
* **Status 변경:** Verified 유지

### 2026-09-01 — Test Result: PASS
* **환경:** Windows 11, Antigravity IDE v2.0 (Gemini 3.7 Flash), 모바일 실기기 (360px~412px 뷰포트)
* **검증된 단계:**
  1. Mermaid 다이어그램 렌더링 규격(1:1 자연 배율 및 극장형 Lightbox 모달)의 모바일 실기기(360px~412px) 렌더링 정상 동작 확인.
  2. v3 로드맵 예정 항목(5개 항목)의 실천 현황 비교 분석 정리 반영.
* **보류된 항목:** 제2호 블로그 개설 및 `/5_블로그추가` 실전 엔드투엔드 검증 (향후 2호 블로그 개설 시점으로 연기).
* **Status 변경:** Experimental → Verified
