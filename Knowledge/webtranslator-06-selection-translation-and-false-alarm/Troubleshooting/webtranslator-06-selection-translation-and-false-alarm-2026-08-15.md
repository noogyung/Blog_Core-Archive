---
topic: webtranslator-06-selection-translation-and-false-alarm
title_kr: 선택 영역 번역 도입과 "내용 없음" 오탐 버그 해결
category: Troubleshooting
sub_category: Selection-Parser
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, SelectionAPI, UX, Alert, Popup, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-06-selection-translation-no-content-false-alarm.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_name: WebTranslator 개발기
series_part: 6
series_prev_slug: webtranslator-05-steam-button-crash-and-dual-renderer
---

# Knowledge File: webtranslator-06-selection-translation-and-false-alarm
## Category: Troubleshooting (Selection-Parser)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-06-selection-translation-and-false-alarm
* **Title_KR:** 선택 영역 번역 도입과 "내용 없음" 오탐 버그 해결
* **Category:** Troubleshooting
* **Sub-Category:** Selection-Parser
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] MDN Web Docs: Selection API (`window.getSelection`) & Range Interface
* [★★★★★] WebTranslator 실전 개발 및 디렉팅 로그 (`a0140cae-5285-4e4d-89fd-977583d33279`)
* [★★★★★] Chrome Extension Selection Translation UX Design Guidelines

---

#### 🔑 Core Concepts (핵심 개념)

* **[선택 영역 번역 (Selection Translation)의 도입 배경]:** 웹페이지 전체를 번역(`Alt+A`)하지 않고, 기술 문서의 특정 설명이나 코드 주석 등 사용자가 마우스로 드래그한 특정 문단/단어만 가볍고 빠르게 번역하는 핀포인트 인터랙션 기능. [FACT]
* **[과도한 부모 DOM 검사로 인한 오탐 (False Alarm)]:** 선택 영역의 상위 컨테이너(`parentElement`) 내부에 과거 번역된 `.wt-translation` 태그가 하나라도 남아있으면, 사용자가 새로 드래그한 영문 텍스트까지 전부 "이미 번역됨"으로 오판하여 거부하는 결함. [FACT]
* **[순수 선택 문자열 기반 정밀 판정]:** 부모 요소의 DOM 상태를 일절 조회하지 않고, `window.getSelection().toString().trim()`으로 추출된 순수 텍스트의 유효성(공백 제외 길이 > 0)만 직접 검증하는 아키텍처. [FACT]
* **[50자 기준 팝업/툴팁 듀얼 분기]:** 300자 이상의 긴 문단을 드래그했을 때 좁은 인라인으로 찌그러지는 레이아웃 파괴를 막기 위해, 50자 초과 텍스트는 문단형 팝업 카드(`wt-selection-card`)로 표출하고 짧은 단어는 미니 툴팁으로 분기. [FACT]
* **[브라우저 alert() 전면 제거]:** 사용자 작업 흐름을 강제로 가로막는 네이티브 `alert()` 대화상자를 전면 금지하고 무반응(Silent return) 처리. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: 마우스 드래그 감지 및 순수 텍스트 추출] [★★★★★]
1. `document.addEventListener("mouseup", handleSelection)` 등록.
2. `const text = window.getSelection().toString().trim()`으로 순수 문자열 추출.
3. 텍스트가 비어있거나 `selection.isCollapsed`인 경우 즉시 조용히 반환.

##### [2단계: 부모 DOM 조회 배제 및 백그라운드 전송] [★★★★★]
1. 부모 DOM 검사 없이 순수 텍스트와 길이 판별 플래그(`isLongParagraph: text.length > 50`)를 Service Worker로 전송.
2. 번역 API로부터 고품질 번역 결과 수신.

##### [3단계: 50자 기준 문단형 팝업/단어 툴팁 렌더링] [★★★★★]
1. `selection.getRangeAt(0).getBoundingClientRect()`로 드래그 좌표 계산.
2. 50자 이하: 원문 옆 미니 툴팁 표시.
3. 50자 초과: 자동 줄바꿈과 스크롤이 지원되는 독립 플로팅 카드 렌더링.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **["번역할 내용이 없습니다" alert() 팝업 오탐]**
  * **증상:** 분명히 영문 문단을 정상적으로 드래그했음에도 "번역이 이미 되어있거나 번역할 내용이 없습니다"라는 엉뚱한 경고창이 뜨며 번역이 실패함.
  * **원인:** AI가 부모 컨테이너 내의 `.wt-translation` 존재 여부를 검사하여 영문 원문까지 번역 완료로 오판함.
  * **해결법:** 부모 DOM 조회를 전면 제거하고 `getSelection().toString().trim()` 순수 문자열 길이만 검사하도록 수정. [FACT]
* **[긴 문단 드래그 시 인라인 찌그러짐 현상]**
  * **증상:** 300자 이상의 긴 문단을 드래그했을 때 한 줄의 인라인으로 처리되어 문장 끝이 브라우저 화면 밖으로 탈출함.
  * **해결법:** 50자를 기준으로 팝업 카드 너비와 서식을 분기하는 문단 렌더러 도입. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 선택 영역 번역에서 DOM의 부모/자식 상태를 기반으로 번역 가능 여부를 판정하면 오탐이 발생하기 쉬우므로, 반드시 사용자가 긁은 순수 문자열(String) 자체만을 기준으로 삼아야 한다.
* [FACT] 확장 프로그램에서 브라우저 기본 `alert()` 창을 사용하는 것은 사용자 경험을 극도로 저해하므로 절대 금지해야 한다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`a0140cae`) 기반 검증 및 기능 도입 맥락 보강 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, SelectionAPI, UX, Alert, Popup, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
