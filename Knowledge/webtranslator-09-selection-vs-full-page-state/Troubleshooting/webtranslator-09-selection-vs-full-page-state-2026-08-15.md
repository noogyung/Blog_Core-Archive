---
topic: webtranslator-09-selection-vs-full-page-state
title_kr: 선택 영역 번역과 전체 번역(Alt+A) 간 상태 충돌
category: Troubleshooting
sub_category: State-Management
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, StateMachine, DOM, Lifecycle, CleanupFirst, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-09-selection-vs-full-page-state-collision.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_name: WebTranslator
series_part: 9
series_total: 21
series_prev_slug: webtranslator-08-word-translation-homogenization-and-specialization
series_next_slug: webtranslator-10-gemini-rate-limits-and-prompt-builder
---

# Knowledge File: webtranslator-09-selection-vs-full-page-state
## Category: Troubleshooting (State-Management)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-09-selection-vs-full-page-state
* **Title_KR:** 선택 영역 번역과 전체 번역(Alt+A) 간 상태 충돌
* **Category:** Troubleshooting
* **Sub-Category:** State-Management
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 상태 관리 및 팝업 매니저 소스 코드 (`src/content/popup_manager.js`, `src/content/index.js`)
* [★★★★★] DOM Mutation & Element Removal Specifications
* [★★★★★] WebTranslator 실전 개발 및 디버깅 로그 (`1ab22664-44c0-4f67-8d47-c854439e95ee`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[상태 충돌 (State Collision)과 팝업 잔존]:** 마우스 드래그 선택 번역 팝업(`.wt-dictionary-popup`, `.wt-selection-card`)이 열린 상태에서 단축키(`Alt+A`)를 누를 때, 기존 팝업이 DOM 트리에 남아있어 전체 번역 텍스트 수집기가 팝업 내부 텍스트까지 긁어가 이중 번역하고 화면 레이어가 뒤엉키는 현상. [FACT]
* **[선행 소탕 (Cleanup-First) 패턴]:** 비동기 전체 페이지 번역 파이프라인이 시작되기 직전 동기적으로 `closeAllPopups()`를 호출하여 화면의 모든 플로팅 요소를 DOM에서 0.001초 만에 강제 제거한 뒤 깨끗한 DOM을 수집하는 구조. [FACT]
* **[독립 공존 라이프사이클]:** 전체 페이지 번역이 활성화된 상태(`translated`)에서도 사용자가 특정 단어를 드래그하면 전체 번역을 해치지 않고 독립된 팝업 카드가 부드럽게 공존하도록 상태 머신을 분리. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: closeAllPopups 선행 제거 함수 구현] [★★★★★]
1. `document.querySelectorAll(".wt-dictionary-popup, .wt-selection-card")`로 활성 팝업 탐색.
2. 모든 팝업 노드를 즉시 `remove()` 처리.

##### [2단계: toggleTranslation 진입점 선행 소탕 배치] [★★★★★]
1. `Alt+A` 단축키 수신 시 1순위로 `closeAllPopups()` 동기 호출.
2. `body.dataset.wtStatus` 상태 검사 후 전체 번역 파서 가동.

##### [3단계: 팝업과 전체 번역의 독립 공존 보장] [★★★★★]
1. 전체 번역 상태에서 단어 드래그 시, 전체 번역 래퍼를 훼손하지 않고 팝업 레이어만 독립 오버레이로 렌더링.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[단축키 입력 시 선택 번역 팝업 잔존 및 팝업 텍스트 재수집 참사]**
  * **증상:** `Alt+A`를 눌렀을 때 단어 사전 팝업이 닫히지 않고, 팝업 안의 글자까지 전체 번역 대상에 포함되어 화면이 엉망으로 깨짐.
  * **원인:** AI가 비동기 번역이 완료된 뒤에 팝업을 닫으려고 시도하여 수집 시점에 팝업이 DOM에 잔존함.
  * **해결법:** 비동기 수집 전 동기적으로 `closeAllPopups()`를 1순위로 호출하는 Cleanup-First 아키텍처로 순서 재배치. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 단일 웹페이지에서 다수의 인터랙션(단축키 전체 번역, 마우스 드래그 팝업)을 지원할 때는 이벤트 발생 시점의 선행 정리(Cleanup-First)를 반드시 보장해야 DOM 오염과 레이스 컨디션을 방지할 수 있다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`1ab22664`) 기반 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, StateMachine, DOM, Lifecycle, CleanupFirst, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
