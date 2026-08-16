---
topic: webtranslator-05-steam-button-and-dual-renderer
title_kr: 스팀 장바구니 버튼 깨짐과 인라인/블록 듀얼 분기
category: Troubleshooting
sub_category: DOM-Rendering
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, DOM, InlineBlock, DualRenderer, Steam, Layout, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-05-steam-button-crash-and-dual-renderer.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_name: WebTranslator
series_part: 5
series_total: 21
series_prev_slug: webtranslator-04-steam-link-color-and-orphan-text-nodes
series_next_slug: webtranslator-06-selection-translation-no-content-false-alarm
---

# Knowledge File: webtranslator-05-steam-button-and-dual-renderer
## Category: Troubleshooting (DOM-Rendering)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-05-steam-button-and-dual-renderer
* **Title_KR:** 스팀 장바구니 버튼 깨짐과 인라인/블록 듀얼 분기
* **Category:** Troubleshooting
* **Sub-Category:** DOM-Rendering
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] MDN Web Docs: Visual Formatting Model (Inline vs Block Formatting Contexts)
* [★★★★★] Steam 상점 버튼(Add to Cart, Buy Now) 및 가격표 레이아웃 실전 디버깅 로그
* [★★★★★] WebTranslator 개발 로그 (`ea46cbb1`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[서식 컨텍스트(Formatting Context) 불일치 문제]:** `button`, `a`, `span` 등 작은 인라인 컨테이너 내부에 블록 서식 요소(`div` 등)를 강제 삽입하면 줄바꿈이 강제 발생하여 버튼 높이가 비정상적으로 팽창(뚱뚱화)하고 인접 레이아웃을 밀어내는 현상. [FACT]
* **[지능형 듀얼 렌더러 (Dual Renderer)]:** 
  1. 시맨틱 태그 목록(`a, button, span, b, strong, em, i, label`) 또는
  2. 25자 이하의 짧은 UI 텍스트 및 `inline/flex` 계산 스타일인 경우 **인라인 모드**(`wt-translation-inline`)로 렌더링하고, 그 외 긴 문단은 **블록 모드**(`wt-translation-block`)로 분기하는 구조. [FACT]
* **[비파괴적 노드 추가 (Non-destructive Append)]:** `innerHTML`이나 `innerText`로 기존 DOM을 덮어써 내부 SVG 아이콘과 자바스크립트 이벤트 리스너를 파괴하는 대신, 독립된 번역 전용 `<span>` 노드를 생성하여 안전하게 덧붙이는 기법. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: isInlineElement 판별 함수 구현] [★★★★★]
1. 요소의 태그명이 인라인 태그 목록(`["a", "button", "span", "b", "strong", "em", "i", "label"]`)에 포함되는지 확인.
2. 원문 텍스트 길이가 25자 이하이고 `getComputedStyle(el).display`가 `inline` 또는 `flex` 계열인지 검사.

##### [2단계: 듀얼 CSS 클래스 적용 및 렌더링] [★★★★★]
1. `isInlineElement`가 참인 경우: `.wt-translation-inline`(`display: inline !important; margin-left: 6px;`) 부여하여 `Add to Cart (장바구니 추가)` 형태로 나란히 배치.
2. `isInlineElement`가 거짓인 경우: `.wt-translation-block`(`display: block !important; margin-top: 4px;`) 부여하여 원문 하단 단락 블록으로 정렬.

##### [3단계: 기존 이벤트 및 자식 노드 보존] [★★★★★]
1. `document.createElement("span")` 생성 후 `appendChild` 또는 `insertAdjacentElement`로 삽입하여 원본 아이콘/이벤트 100% 보존.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[스팀 장바구니 버튼 팽창 및 주변 가격표 침범]**
  * **증상:** `Add to Cart` 버튼 번역 시 버튼 내부에서 줄바꿈이 일어나 버튼이 거대해지며 가격표와 겹침.
  * **원인:** AI가 모든 번역 결과를 일괄 블록 요소로 생성함.
  * **해결법:** 25자 이하 및 태그 속성을 복합 판정하는 `isInlineElement` 듀얼 렌더러를 도입하여 인라인 렌더링으로 전환. [FACT]
* **[버튼 내부 SVG 아이콘 및 클릭 이벤트 증발]**
  * **증상:** 번역 적용 후 장바구니 버튼 클릭이 작동하지 않거나 카트 아이콘이 사라짐.
  * **원인:** `innerText` 또는 `innerHTML`을 무분별하게 조작하여 기존 자식 노드를 날려버림.
  * **해결법:** 별도 `<span>` 번역 태그를 독립 생성하여 안전하게 덧붙이는 비파괴 DOM 삽입으로 변경. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 번역 확장 프로그램에서 버튼과 메뉴 같은 짧은 UI 텍스트는 블록 렌더링을 하면 웹사이트의 디자인을 무조건 망가뜨리므로, 25자 기준 인라인 분기 처리가 필수적이다.
* [FACT] `innerHTML`이나 `innerText`의 덮어쓰기는 기존 웹사이트의 자바스크립트 이벤트 바인딩을 파괴하므로 반드시 `createElement` + `appendChild` 방식을 고수해야 한다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`ea46cbb1`) 기반 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, DOM, InlineBlock, DualRenderer, Steam, Layout, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
