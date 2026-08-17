---
topic: webtranslator-01-inception-and-csp
title_kr: 웹 번역 확장 프로그램 기획과 CSP 차단 극복
category: Troubleshooting
sub_category: MV3-Architecture
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, ManifestV3, CSP, ServiceWorker, MessagePassing]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-01-inception-and-csp-bypass.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_id: webtranslator
series_title: WebTranslator 개발기
series_part: 1
series_prev_slug: null
---

# Knowledge File: webtranslator-01-inception-and-csp
## Category: Troubleshooting (MV3-Architecture)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-01-inception-and-csp
* **Title_KR:** 웹 번역 확장 프로그램 기획과 CSP 차단 극복
* **Category:** Troubleshooting
* **Sub-Category:** MV3-Architecture
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] Chrome Extensions Documentation: Manifest V3 Architecture & Message Passing
* [★★★★★] MDN Web Docs: Content Security Policy (CSP) & Cross-Origin Resource Sharing (CORS)
* [★★★★★] WebTranslator 실전 개발 및 디렉팅 로그 (`77cc2ad9-b95c-40d9-8e3e-f640d42635c2`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[Chrome Manifest V3 보안 격리 모델]:** Content Script는 웹페이지의 DOM에 접근할 수 있지만, 실행 컨텍스트는 방문 중인 웹사이트의 CSP(Content Security Policy) 정책에 직접 종속된다. [FACT]
* **[Background Service Worker의 네트워크 특권]:** Background Service Worker는 웹페이지 DOM과 분리된 독립 컨텍스트에서 실행되며, `manifest.json`의 `host_permissions`에 정의된 외부 API 엔드포인트와 CSP/CORS 제약 없이 통신할 수 있다. [FACT]
* **[비동기 Message Passing 파이프라인]:** `chrome.runtime.sendMessage`와 `chrome.runtime.onMessage.addListener`를 이용하여 UI 렌더링을 담당하는 Content Script와 외부 API 네트워크 통신을 전담하는 Service Worker를 분리 결합하는 패턴. [FACT]
* **[비파괴적(Non-destructive) DOM 렌더링]:** 기존 구글 번역처럼 원본 DOM 트리를 파괴하거나 치환하지 않고, 원본 텍스트 노드 아래에 독립된 번역 컨테이너를 삽입하여 레이아웃 깨짐을 방지하는 설계. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: Manifest V3 권한 및 Service Worker 선언] [★★★★★]
1. `manifest.json` 파일에 `manifest_version: 3`을 명시.
2. Background Service Worker 등록: `"background": { "service_worker": "src/background/index.js", "type": "module" }`.
3. 통신할 번역 API 호스트 권한을 `host_permissions`에 선언.

##### [2단계: Background Service Worker 메시지 리스너 구현] [★★★★★]
1. `chrome.runtime.onMessage.addListener`를 등록하고 비동기 응답 처리를 위해 핸들러에서 `return true;`를 반환.
2. Content Script로부터 전달받은 텍스트 페이로드를 외부 번역 API(Google/Gemini 등)로 `fetch()` 요청.
3. 응답 데이터를 파싱하여 `sendResponse({ success: true, data: result })`로 회신.

##### [3단계: Content Script 단축키 및 비파괴적 렌더링 구현] [★★★★★]
1. `Alt+A` 키보드 이벤트 리스너를 등록하여 번역 토글 트리거 구성.
2. 대상 웹페이지 내 텍스트 노드를 순회 수집하여 `chrome.runtime.sendMessage`로 Background에 전송.
3. 수신된 번역 텍스트를 원문 노드 하단에 별도 컨테이너로 삽입.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[Content Script에서 외부 API 직접 fetch 시 CSP 차단 오류]**
  * **증상:** GitHub, Steam, MDN 등 보안 정책이 엄격한 사이트에서 번역 시도시 콘솔에 `Refused to connect to 'https://...' because it violates the following Content Security Policy directive` 에러가 발생하며 통신 두절.
  * **원인:** AI 에이전트가 MV3 보안 모델을 무시하고 웹페이지 DOM 컨텍스트에서 실행되는 `content.js` 내부에 직접 `fetch()` 코드를 작성함.
  * **해결법:** Content Script에서의 직접 네트워크 호출을 전면 금지하고, 네트워크 요청을 Background Service Worker가 전담하여 중계하도록 통신 파이프라인을 전면 재설계. [FACT]
  * **신뢰도:** [★★★★★] (실제 환경 검증 완료)

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] 기존 상용 번역 확장 프로그램들의 잦은 과금 유도 및 회원가입 요구로 인한 사용성 저하와, 생성형 AI의 발달로 인한 1인 확장 프로그램 개발 진입장벽 완화가 프로젝트 기획 및 착수의 직접적인 계기가 됨.
* [FACT] AI 코딩 어시스턴트에게 초기 Chrome Extension 작성을 지시하면 단순 웹 개발 방식으로 접근하여 Content Script에 직접 외부 API `fetch()`를 작성하는 실수를 높은 확률로 범한다.
* [FACT] MV3 환경에서는 반드시 설계 초기부터 'DOM 조작(Content Script) ↔ 비동기 메시징 ↔ 네트워크 통신(Background Service Worker)' 구조를 명시적으로 지시해야 한다.
* [OPINION] 원문과 번역문을 함께 보여주는 번역기는 레이아웃 보존이 핵심이므로, 원본 DOM 요소를 직접 치환하는 브라우저 내장 번역 방식보다 비파괴적 인라인/블록 삽입 방식이 개발 문서 및 커뮤니티 정독에 훨씬 유리하다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 실전 개발 로그(`77cc2ad9`) 기반 초기 작성 및 실전 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, ManifestV3, CSP, ServiceWorker, MessagePassing, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
