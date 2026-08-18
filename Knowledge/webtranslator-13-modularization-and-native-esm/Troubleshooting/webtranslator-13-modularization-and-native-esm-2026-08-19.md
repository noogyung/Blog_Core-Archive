---
topic: webtranslator-13-modularization-and-native-esm
title_kr: 3,000줄 단일 파일 붕괴와 크롬 MV3 네이티브 ES 모듈 전환
category: Troubleshooting
sub_category: Architecture-Refactoring
version: 1.4.0
status: Experimental
created_date: 2026-08-19
last_modified: 2026-08-19
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, 모듈화, ESModules, ESM, boot_js, ManifestV3, 트러블슈팅]
sources_count: 3
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
series_id: webtranslator
---

# Knowledge File: webtranslator-13-modularization-and-native-esm
## Category: Troubleshooting (Architecture-Refactoring)
## Date: 2026-08-19

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-13-modularization-and-native-esm
* **Title_KR:** 3,000줄 단일 파일 붕괴와 크롬 MV3 네이티브 ES 모듈 전환
* **Category:** Troubleshooting
* **Sub-Category:** Architecture-Refactoring
* **Version:** 1.4.0
* **Status:** Experimental
* **Date:** 2026-08-19
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 부트로더 및 매니페스트 소스 코드 (`src/content/boot.js`, `manifest.json`, 커밋 `23e19f0`)
* [★★★★★] Chrome Extension Manifest V3 ES Modules 공식 개발 명세
* [★★★★★] WebTranslator 실전 개발 및 모듈화 디버깅 로그 (`5e699ff6`, `300bc085`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[3,000줄 거대 단일 파일(Monolith)의 한계]:** 다중 AI 엔진, 듀얼 렌더러, DOM 수집기가 하나의 `content.js`와 `background.js`에 뭉쳐져 3,000줄을 돌파하면서, 사소한 괄호 오타 하나로 확장 프로그램 전체가 `SyntaxError`로 죽어버려 디버깅이 불가능해진 한계. [FACT]
* **[번들러 배제와 노빌드(No-Build) 철학]:** Webpack이나 Vite 같은 무거운 번들러를 도입하면 빌드 파이프라인과 난독화로 인해 실시간 디버깅이 어려워지므로, 브라우저 표준 네이티브 ES 모듈(`import/export`)을 고수하여 '저장 후 즉시 새로고침' 개발 환경을 지향. [FACT]
* **[크롬 MV3 Content Script의 `type: module` 미지원 제약]:** 백그라운드 서비스 워커는 `manifest.json`에서 `"type": "module"`을 지원하지만, 웹페이지에 주입되는 Content Script는 매니페스트에서 이를 직접 지원하지 않아 정적 `import` 사용 시 `SyntaxError: Cannot use import statement outside a module`이 발생함. [FACT]
* **[동적 `import()` 기반의 `boot.js` 부트로더 아키텍처]:** 매니페스트에는 10줄 내외의 경량 `src/content/boot.js`만 등록하고, 내부에서 `import(chrome.runtime.getURL("src/content/index.js"))`를 실행하여 실제 모듈 트리를 비동기 동적 로딩하는 해결책. [FACT]
* **[`web_accessible_resources` 권한 연동]:** 동적으로 로드되는 `src/content/*` 하위 JS 파일들을 브라우저가 웹페이지 컨텍스트에서 접근할 수 있도록 매니페스트에 선언해야 정상 작동함. [FACT]

---

#### 🛠️ Procedures (절차)

1. **디렉토리 구조 모듈화 및 역할 분리:** [★★★★★]
   - `src/api` (엔진 및 프롬프트), `src/background` (서비스 워커), `src/content` (DOM 및 UI), `src/options` (옵션 페이지), `src/optionPopup` (툴바 팝업)으로 분리.
2. **Background Service Worker 모듈 선언:** [★★★★★]
   - `manifest.json`의 `background` 항목에 `"service_worker": "src/background/index.js"`, `"type": "module"` 지정.
3. **Content Script 부트로더(`boot.js`) 구현:** [★★★★★]
   ```javascript
   // src/content/boot.js
   (async () => {
     try {
       const src = chrome.runtime.getURL("src/content/index.js");
       await import(src);
       console.log("[WebTranslator] Content scripts loaded via boot.js");
     } catch (err) {
       console.error("[WebTranslator] Failed to load content scripts:", err);
     }
   })();
   ```
4. **`manifest.json` 리소스 권한 선언:** [★★★★★]
   ```json
   {
     "content_scripts": [
       {
         "matches": ["<all_urls>"],
         "js": ["src/content/boot.js"],
         "run_at": "document_idle"
       }
     ],
     "web_accessible_resources": [
       {
         "resources": ["src/content/*"],
         "matches": ["<all_urls>"]
       }
     ]
   }
   ```
5. **모듈 바인딩 무결성 검증 및 롤백 거부:** [★★★★★]
   - 단일 파일로의 롤백을 단호히 거부하고, 각 모듈 간 누락된 `export`/`import` 참조 에러(`ReferenceError`)를 하나씩 연결하여 완성.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[Content Script에서 `import` 작성 시 `SyntaxError: Cannot use import statement outside a module` 오류]**
  * **원인:** Manifest V3의 `content_scripts` 필드가 `"type": "module"`을 지원하지 않아 정적 `import`를 직접 파싱하지 못함.
  * **해결법:** `boot.js`를 일반 스크립트로 주입한 뒤 `chrome.runtime.getURL()`과 동적 `import()` 함수로 진입점 모듈을 로드하여 네이티브 ESM 구동. [FACT]
  * **신뢰도:** [★★★★★]
* **[모듈화 직후 `ReferenceError: isOurElement is not defined` 연쇄 발생]**
  * **원인:** 거대 단일 파일을 여러 파일로 쪼개면서 전역 유틸리티 함수의 `export`/`import` 연결이 누락됨.
  * **해결법:** 롤백하지 않고 모듈 의존성 트리를 정비하여 필요한 함수들을 명시적으로 내보내고 불러오도록 바인딩 완료. [FACT]
  * **신뢰도:** [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] AI 에이전트가 리팩토링 직후 문법 에러가 쏟아지면 당황하여 이전 정상 코드로 롤백하자고 제안하는 경우가 많으나, 구조 개선을 위해서는 롤백 제안을 단호히 거부하고 에러를 하나씩 추적해 정면 돌파해야 한다.
* [FACT] 번들러 없이 네이티브 ES 모듈을 사용하면 코드 수정 후 `npm run build` 없이 브라우저 새로고침만으로 즉시 디버깅할 수 있어 생산성이 크게 향상된다.

---

#### ❓ Missing Info (검증 필요 항목)

* [ ] 크롬 웹 스토어 심사 시 `web_accessible_resources`의 `src/content/*` 선언이 보안상 최소 권한 지침과 충돌하지 않는지 점검
* [ ] 향후 브라우저(Firefox, Safari) 호환성 확장 시 `boot.js` 동적 import 방식의 작동 여부 확인

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, 모듈화, ESModules, ESM, boot_js, ManifestV3, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====