---
topic: antigravity-blog-automation
title_kr: Antigravity를 이용한 블로그 포스팅 자동화 워크플로우
category: Workflow
sub_category: Automation-Setup
version: 2026-06-19
status: Verified
created_date: 2026-06-19
last_modified: 2026-06-19
language: KR
tags: [Antigravity, 블로그-자동화, 워크플로우, NotebookLM-한계, 지식-관리, 블로그스팟, Blogger, 테크-블로깅, 에이전트-루프]
sources_count: 5
blog_draft_path: Blog_Posts/core-archive/2026-06/antigravity-blog-automation-workflow.html
blog_draft_date: 2026-06-19
blog_id: core-archive
blog_published: true
series_id: antigravity-blog-automation
series_title: Antigravity 블로그 포스팅 자동화 구축기
series_part: 1
series_prev_slug: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** antigravity-blog-automation
* **Title_KR:** Antigravity를 이용한 블로그 포스팅 자동화 워크플로우
* **Category:** Workflow
* **Sub-Category:** Automation-Setup
* **Version:** 2026-06-19
* **Status:** Verified
* **Date:** 2026-06-19
* **Language:** KR

---

#### 📚 Sources & Confidence

* [★★★★★] 로컬 워크플로우 지시서: Instructions/ (File2, File3, File4 및 README.md)
* [★★★★★] 사용자 실제 검증 및 피드백: NotebookLM 연동 실패 분석 및 Antigravity 에이전트 단독 통합 경험

---

#### 🔑 Core Concepts (핵심 개념)

* **[NotebookLM의 한계와 Antigravity의 차별점]:** 구글 NotebookLM은 정밀한 프롬프트 지시를 준수하지 못하며, 정리 결과가 무작위적(Random)이고, 정보 수집 과정에서 엉뚱하거나 잘못된 문서를 가져오는 문제가 심각했다. 반면 Antigravity는 파일 시스템 제어, 웹 검색, 터미널 실행 등 구체적인 도구(Tools)를 활용해 단계별로 지식을 추출·검증하므로 신뢰도 높은 자동화 제어가 가능하다. [FACT/OPINION]

* **[4단계 블로그 자동화 파이프라인]:**
  1. **리서치 및 지식 추출/저장 (File 2):** 웹 리서치 결과를 기반으로 FACT/OPINION/CONFLICT 정보를 분류하여 로컬 지식 문서 생성.
  2. **검증 및 피드백 반영 (File 3):** 사용자가 실제 실행 및 테스트를 마친 결과를 입력하여 지식 문서의 검증 상태(`status`)를 변경.
  3. **HTML 블로그 초안 생성 (File 4):** 로컬 지식 문서와 블로그 페르소나를 기반으로 구글 블로그스팟 친화적인 HTML 초안 생성.
  4. **발행 및 동기화:** 실제 발행 완료 시 지식 베이스의 발행 여부(`blog_published: true`) 상태 동기화. [FACT]

* **[검증된 지식 기반 글쓰기(Verified-driven Writing)]:** AI가 단순히 그럴듯한 글을 임의로 지어내는(Hallucination) 위험을 방지하기 위해, 반드시 팩트가 검증된 로컬 지식 베이스(Knowledge Base)를 필터링(Verified 상태만 포스팅 허용)하고 여기에 필자의 실제 경험을 얹어서 HTML로 가공하는 안전한 글쓰기 방식이다. [OPINION]

---

#### 🛠️ Procedures (절차)

1. **[1단계: 지식 수집 및 로컬 문서화]** [★★★★★]
   - 사용자가 "X에 대해 정리해줘"라고 요청하면, Antigravity가 웹 검색(공식 문서 우선)을 수행하여 핵심 지식을 추출한다.
   - 추출된 지식을 `Knowledge/{Topic}/{Category}/{topic}-{version}.md` 경로에 저장하고, 중앙 인덱스 파일(`Knowledge/_index.md`)에 등록한다. 최초 저장 시 상태는 항상 `Experimental`이다.

2. **[2단계: 테스트 및 검증 결과 피드백]** [★★★★★]
   - 사용자가 로컬 가이드대로 실제 동작을 테스트한 후, 테스트 결과(Pass/Fail 등)와 추가 경험을 피드백으로 제출한다.
   - Antigravity는 해당 피드백을 지식 문서에 반영하고 상태를 `Verified` 또는 `Verified (Partial)`로 업그레이드한다.

3. **[3단계: 블로그 HTML 초안 생성]** [★★★★★]
   - 사용자가 블로그 HTML 작성을 지시하면, Antigravity는 `Instructions/Personas/`에서 매칭되는 페르소나를 가져온다.
   - HTML 작성 전 SEO 분석 결과(제목, 키워드, 메타 설명 등)를 제시하여 사용자의 최종 확인을 받는다.
   - 승인 후 블로그스팟에 즉시 붙여넣을 수 있는 순수 HTML 코드를 작성하여 `Blog_Posts/{blog-id}/{YYYY-MM}/{slug}.html` 경로에 저장한다.

4. **[4단계: 실제 발행 및 상태 동기화]** [★★★★★]
   - 사용자는 드래프트 HTML 소스를 복사하여 블로그스팟에 붙여넣고, 본문 내 이미지 슬롯에 실제 이미지를 매핑하여 발행한다.
   - 발행 후 사용자가 "발행 완료"를 입력하면 해당 지식 문서의 `blog_published` 상태를 `true`로 갱신한다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[NotebookLM을 통한 자동화 파이프라인 연동 실패]**
  * **증상:** NotebookLM이 요약 및 정리 프롬프트 지시를 무시하고 임의의 포맷으로 출력함. 기사 수집 시 키워드 매칭 오류로 원치 않는 잘못된 기사를 가져옴.
  * **원인:** NotebookLM은 대규모 문서 분석 및 정적 대화에는 적합하지만, 실시간 검색 도구 통제 및 로컬 파일 시스템/지시서 규격에 맞춘 엄격한 멀티스텝 프롬프트 수행력이 보장되지 않음.
  * **해결법:** NotebookLM을 수집 단계에서 배제하고, Antigravity 에이전트가 직접 리서치(검색/URL 읽기) 및 추출을 수행하여 로컬 Knowledge 파일로 즉시 저장하도록 `Knowledge Manager v2.0` 통합 구조로 전환함. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] 초기에 "NotebookLM이 자료를 수집·정리하고, Antigravity가 이를 HTML로 가공한다"라는 분리형 설계를 세운 것은 구글의 NotebookLM 브랜드를 과신한 데서 온 시행착오였다. 도구 실행 능력을 보유한 코딩 에이전트(Antigravity)에게 리서치부터 저장까지의 전 과정을 단일 루프로 위임하는 것이 훨씬 통제 가능하고 품질이 우수하다. — 출처: 사용자 직접 경험 [USER VERIFIED]

* [OPINION] AI 블로그 자동화의 흔한 실패 원인은 '검증되지 않은 날것의 정보'를 그대로 포스팅하는 데 있다. 번거롭더라도 에이전트가 수집한 지식을 사용자가 직접 검증(Feedback Loop)하고 `Verified` 라벨을 획득한 문서만 블로그 발행 하네스로 넘어가게끔 설계한 '지식 검증 기반 워크플로우'가 롱런할 수 있는 비결이다. — 출처: 사용자 직접 경험 [USER VERIFIED]

* [OPINION] 작업을 끝마친 후 기억에 의존해 문서를 쓰려고 하면 설치 환경을 다시 재현(초기화)하기 어렵고 사소한 에러 해결 단계를 누락하기 쉽다. 따라서 작업 전에 리서치된 지식 문서를 'Experimental' 상태로 먼저 받아두고, 작업과 검증을 동시에 진행하며 즉시 피드백하는 '실시간 검증 프로세스'가 문서 및 게시글 정합성 확보에 필수적이다. — 출처: 사용자 직접 경험 [USER VERIFIED]

---

#### ❓ Missing Info (검증 필요 항목)

* [x] NotebookLM과 Antigravity 연동 효율성 검증 — Closed 2026-06-19 (연동 품질 저하 확인, Antigravity 단독 통합 워크플로우 전환으로 검증 종료)

---

#### 🏷️ Tags

Antigravity, 블로그-자동화, 워크플로우, NotebookLM-한계, 지식-관리, 블로그스팟, Blogger, 테크-블로깅, 에이전트-루프

===== KNOWLEDGE PACKAGE END =====
