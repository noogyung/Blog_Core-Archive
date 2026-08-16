---
topic: webtranslator-04-steam-link-color-and-orphan-nodes
title_kr: 스팀 <a> 링크 색상 오추출과 고아 텍스트 노드 래핑
category: Troubleshooting
sub_category: DOM-Rendering
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, DOM, DisplayContents, Steam, Layout, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-04-steam-link-color-and-orphan-text-nodes.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_name: WebTranslator
series_part: 4
series_total: 21
series_prev_slug: webtranslator-03-v01-snapshot-and-custom-dict-order-bug
series_next_slug: webtranslator-05-steam-button-crash-and-dual-renderer
---

# Knowledge File: webtranslator-04-steam-link-color-and-orphan-nodes
## Category: Troubleshooting (DOM-Rendering)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-04-steam-link-color-and-orphan-nodes
* **Title_KR:** 스팀 <a> 링크 색상 오추출과 고아 텍스트 노드 래핑
* **Category:** Troubleshooting
* **Sub-Category:** DOM-Rendering
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] MDN Web Docs: CSS `display: contents` & Window.getComputedStyle()
* [★★★★★] Steam 상점 및 커뮤니티 페이지 Flex/Grid DOM 구조 실전 디버깅 로그
* [★★★★★] WebTranslator 개발 로그 (`ea46cbb1`, Commit `c9b9cf4`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[고아 텍스트 노드(Orphan Text Node)의 레이아웃 충돌]:** `<div>`나 `<p>` 내부에 별도의 인라인 태그 없이 직접 매달린 텍스트 노드를 감싸기 위해 일반 `<span>` 태그를 삽입하면, 부모가 `display: flex` 또는 `grid`일 때 새로운 Flex Item으로 인식되어 요소 배치가 뒤틀리고 줄바꿈이 일어나는 현상. [FACT]
* **[CSS `display: contents` 가상 래퍼]:** 요소 자체의 박스(Box) 모델을 렌더 트리에서 완전히 생략하여, 부모 레이아웃 엔진에는 래퍼가 존재하지 않는 것처럼 투명하게 동작하면서 자식 텍스트 노드들을 안전하게 그룹화하는 기법. [FACT]
* **[직속 부모 색상 정밀 추출 (`computedStyle`)]:** 상위 컨테이너 내부의 `<a>` 태그 색상이 엉뚱하게 일반 텍스트 노드로 상속·전이되는 것을 방지하기 위해, 텍스트 노드의 직속 `parentElement`의 계산된 스타일(`color`)만 1:1로 추출하는 방식. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: childNodes 순회를 통한 유효 텍스트 노드 감지] [★★★★★]
1. `element.childNodes`를 배열로 변환하여 순회.
2. `nodeType === Node.TEXT_NODE` 및 공백이 아닌 실제 텍스트가 포함된 노드 선별.

##### [2단계: display: contents 가상 래퍼 삽입] [★★★★★]
1. `document.createElement("span")` 생성 후 `.wt-text-wrapper` 클래스 부여.
2. 인라인 스타일 및 CSS에 `display: contents !important;` 지정.
3. `parentNode.insertBefore(wrapper, node)`로 텍스트 노드를 가상 래퍼 내부로 이동.

##### [3단계: 직속 부모 기준 1:1 글자 색상 보존] [★★★★★]
1. `window.getComputedStyle(node.parentElement).color`를 조회하여 정확한 원본 글자 색상 확인.
2. 번역 컨테이너에 해당 색상을 상속하여 주변 텍스트와 완벽한 시각적 일체감 유지.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[Flex/Grid 부모 하위 텍스트 노드 래핑 시 레이아웃 붕괴]**
  * **증상:** 스팀 상점의 가격표, 구매 버튼, 사이드바 메뉴 등에서 텍스트 노드를 감싸자마자 버튼 위치가 밀려나거나 세로로 찢어짐.
  * **원인:** 일반 `<span>`이 부모의 Flex Item으로 강제 등록되어 레이아웃 계산을 교란함.
  * **해결법:** 래퍼에 `display: contents`를 적용하여 브라우저 레이아웃 엔진이 래퍼를 무시하고 텍스트만 렌더링하도록 수정. [FACT]
* **[컨테이너 내 <a> 링크 색상이 일반 번역문 전체로 전이되는 버그]**
  * **증상:** 한 문단 내에 링크가 하나만 있어도 문단 전체의 번역문이 파란색 링크 글씨로 칠해짐.
  * **원인:** AI가 `querySelector("a")`로 색상을 상위에서 일괄 검색하여 엉뚱한 색상을 상속함.
  * **해결법:** 텍스트 노드의 직속 `parentElement` 색상만 엄격히 추출하도록 로직 변경. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 웹페이지의 DOM을 비파괴적으로 조작할 때, 부모 컨테이너의 Flex/Grid 상태를 깨뜨리지 않으면서 텍스트 노드를 감싸는 가장 안전하고 우아한 표준 CSS 기법은 `display: contents`이다.
* [FACT] 복합 UI에서 글자 색상을 상속할 때는 상위 DOM을 탐색하지 말고 반드시 해당 텍스트 노드의 직속 부모 `parentElement`의 계산된 스타일만 읽어와야 한다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`ea46cbb1`) 기반 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, DOM, DisplayContents, Steam, Layout, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
