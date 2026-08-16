---
topic: webtranslator-03-custom-dict-and-snapshot
title_kr: v0.1 스냅샷과 사용자 사전 적용 순서 버그
category: Troubleshooting
sub_category: Pipeline-Optimization
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, CustomDictionary, Pipeline, Regex, Snapshot, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-03-v01-snapshot-and-custom-dict-order-bug.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_name: WebTranslator
series_part: 3
series_total: 21
series_prev_slug: webtranslator-02-shortcut-race-condition-and-storage-split
series_next_slug: webtranslator-04-steam-link-color-and-orphan-text-nodes
---

# Knowledge File: webtranslator-03-custom-dict-and-snapshot
## Category: Troubleshooting (Pipeline-Optimization)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-03-custom-dict-and-snapshot
* **Title_KR:** v0.1 스냅샷과 사용자 사전 적용 순서 버그
* **Category:** Troubleshooting
* **Sub-Category:** Pipeline-Optimization
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator v0.1 아키텍처 및 스냅샷 문서 (`WebTranslator_v0.1.md`)
* [★★★★★] WebTranslator 실전 개발 및 디렉팅 로그 (`0e7353fc`, `79b65bfd`)
* [★★★★★] JavaScript Regular Expressions: Unicode Word Boundaries & String Replacement Standards

---

#### 🔑 Core Concepts (핵심 개념)

* **[전처리 선치환(Pre-replacement)의 문맥 파괴 현상]:** 번역 API로 원문을 발송하기 전에 영문 텍스트 내 특정 단어를 한글 사전값으로 치환하면, 번역 엔진이 한/영 혼용 문맥을 오인하여 한글 단어를 다시 영어로 역번역하거나 조사 결합을 붕괴시키는 오류. [FACT]
* **[사후 덧씌우기(Post-replacement) 파이프라인]:** 번역 API에는 온전한 원문 문장을 전달하여 최적의 문맥 번역을 획득하고, 회신받은 번역 결과 텍스트에 사용자 사전을 정규식으로 안전하게 덧씌우는(Post-process) 아키텍처. [FACT]
* **[DOM 자가 오염 방지 (`isOurElement` 가드)]:** 확장 프로그램이 웹페이지에 삽입한 번역 컨테이너(`.wt-translation`)를 텍스트 수집 파서가 다시 탐색하여 번역문이 무한 중첩 증식하는 것을 방지하는 필터링 로직. [FACT]
* **[v0.1 스냅샷 아카이빙]:** MV3 기본 골격, `Alt+A` 비동기 취소 토글, 분리형 스토리지, 사후 사전 치환 파이프라인이 완성된 첫 번째 안정 마일스톤. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: 텍스트 수집 시 자가 요소(`isOurElement`) 필터링] [★★★★★]
1. `collectTextNodes()` 순회 시 대상 요소가 `.wt-translation`, `.wt-wrapper` 또는 `SCRIPT`, `STYLE`인지 검사.
2. 자체 생성 요소인 경우 탐색에서 제외하여 재번역 및 DOM 중첩 오염 방지.

##### [2단계: 온전한 원문 번역 요청] [★★★★★]
1. 사전 치환을 거치지 않고 추출된 순수 원문 텍스트 배열을 Background Service Worker로 전달.
2. 외부 번역 API가 전체 문맥을 온전히 이해한 자연스러운 번역 결과 수신.

##### [3단계: 사후 사용자 사전 덧씌우기(`applyLocalDictionary`)] [★★★★★]
1. `chrome.storage.local`에서 사용자 등록 사전(`userDict`) 로드.
2. 특수문자 이스케이프 후 정규식 `new RegExp(escaped, 'gi')` 생성.
3. 최종 번역 결과물에 대해 사전을 매핑 치환한 뒤 DOM에 렌더링.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[사용자 정의 사전 적용 시 엉뚱한 역번역 및 문장 파괴]**
  * **증상:** `Mod -> 모드`로 사전을 등록했으나, 문장 내에서 `모드`가 다시 `fashion`이나 `mode`로 번역되거나 문맥이 엉망으로 깨짐.
  * **원인:** AI가 번역 API 호출 전 원문에 사전을 먼저 치환(전처리)하여 번역기로 전송함.
  * **해결법:** 번역 파이프라인 순서를 역전시켜, 번역 API 응답 수신 후 사후 덧씌우기(Post-process)로 로직 전면 재배치. [FACT]
* **[번역문 재수집으로 인한 DOM 중복 증식]**
  * **증상:** 단축키를 눌렀을 때 이미 삽입된 번역문까지 텍스트로 인식되어 번역문 아래에 또 번역문이 달리는 현상.
  * **해결법:** `isOurElement()` 가드 함수를 추가하여 확장 프로그램 전용 클래스가 포함된 DOM 요소를 수집 대상에서 원천 배제. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 번역 확장 프로그램에서 단어 사전 기능을 구현할 때 원문 치환 방식은 번역 엔진의 문맥 이해를 완전히 망가뜨리므로, 반드시 사후 치환(Post-processing) 아키텍처를 채택해야 한다.
* [FACT] 확장 프로그램이 삽입하는 모든 DOM 요소에는 일관된 고유 클래스명(예: `.wt-translation`)을 부여하고, 파서가 이를 식별하여 탐색에서 배제하도록 설계해야 자가 오염을 막을 수 있다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`0e7353fc`, `79b65bfd`) 기반 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, CustomDictionary, Pipeline, Regex, Snapshot, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
