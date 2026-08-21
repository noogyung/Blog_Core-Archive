---
topic: webtranslator-21-least-privilege-and-store-release
title_kr: MV3 최소 권한 다이어트와 크롬 웹 스토어 v1.0.0 정식 심사 제출
category: Troubleshooting
sub_category: Store-Release-Least-Privilege
version: 1.0.0
status: Experimental
created_date: 2026-08-21
last_modified: 2026-08-21
language: KR+EN
tags: [WebTranslator, ChromeExtension, ChromeWebStore, ManifestV3, LeastPrivilege, PermissionsDiet, HostPermissions, StoragePermission, StoreSubmission, InReview]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
series_id: webtranslator
---

# Knowledge File: webtranslator-21-least-privilege-and-store-release
## Category: Troubleshooting (Store-Release-Least-Privilege)
## Date: 2026-08-21

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-21-least-privilege-and-store-release
* **Title_KR:** MV3 최소 권한 다이어트와 크롬 웹 스토어 v1.0.0 정식 심사 제출
* **Category:** Troubleshooting
* **Sub-Category:** Store-Release-Least-Privilege
* **Version:** 1.0.0
* **Status:** Experimental
* **Date:** 2026-08-21
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 21편 개발 및 스토어 제출 기획 문서 (`docs/blog/21_chrome_web_store_least_privilege_release.md`, `docs/CHROME_STORE_PUBLISH_GUIDE.md`)
* [★★★★★] WebTranslator 최종 매니페스트 및 배포 패키지 (`manifest.json`, `dist/web-translator-v1.0.0.zip`)
* [★★★★★] Chrome Web Store Developer Program Policies — Single Purpose & Use of Permissions (Least Privilege Policy)
* [★★★★☆] Google Chrome Extensions Documentation (Permissions & Host Permissions in Manifest V3)
* [★★★★☆] Google Chrome Web Store Review Guidelines (Privacy Practices and Account Registration)

---

#### 🔑 Core Concepts (핵심 개념)

* **[최소 권한의 원칙 (Principle of Least Privilege)과 크롬 웹 스토어 심사 정책]:**
  * 크롬 웹 스토어는 확장 프로그램이 선언한 모든 권한에 대해 엄격한 정당성과 최소 권한 원칙을 요구함. [FACT]
  * 개발 편의를 위해 임의로 추가해 둔 미사용 권한이 매니페스트에 잔존할 경우, '과도한 권한 요청(Excessive Permissions)'으로 분류되어 심사가 지연되거나 반려될 위험이 큼. [FACT]
  * 필수 기능 구현에 필요한 최소한의 권한만 선언하고, 각 권한의 사용 목적을 명확히 증명해야 단일 심사로 통과할 확률을 높일 수 있음. [FACT]
* **[권한 정리 (Permissions Diet): `permissions` vs `host_permissions`]:**
  * **`permissions` 최소화:** UI 설정, API 키, 로컬 단어 캐시 저장을 위한 `storage` 단 1개만 유지하고, 사용하지 않는 `activeTab`, `scripting`, `contextMenus`, `declarativeNetRequest`는 전면 삭제. [FACT]
  * **`host_permissions` 정당성 확보:** 방문 중인 임의의 사이트에서 단축키(Alt+A)를 통한 즉각적인 인라인 번역 감지 및 로컬 LLM(Ollama: `localhost:11434`), 자체 번역 서버(LibreTranslate), 클라우드 번역 API(Google, Gemini, OpenAI, Claude)와의 네트워크 통신을 위해 `<all_urls>` 권한이 필수적임. [FACT]
* **[개인정보 보호 관행 (Privacy Practices) 작성 기준]:**
  * **단일 목적(Single Purpose):** 웹 페이지 인라인 번역 및 드래그 단어 사전 조회라는 명확한 단일 기능 정의. [FACT]
  * **데이터 수집 정책:** 사용자 개인 데이터를 수집하거나 외부로 전송하지 않으며, API 키 및 설정은 브라우저 로컬 저장소(`chrome.storage.sync`)에만 보관한다는 '데이터 비수집' 선언. [FACT]
* **[시리즈 연속성: 20편(출시 요건 총정리) → 21편(최소 권한 다이어트 및 스토어 제출)]:**
  * 20편에서 공식 아이콘 교체, 실물 스크린샷 에셋 정돈 및 5대 심사 체크리스트를 확립함. [FACT]
  * 21편에서는 매니페스트 내 불필요 권한을 모두 걷어내는 권한 다이어트를 거쳐, 호스트 권한 영문 소명서 작성, 순수 배포용 zip 패키징 및 최종 스토어 심사 제출('In Review' 상태 진입)로 v1.0.0 개발 여정을 일단락함. [FACT]

---

#### 🛠️ Procedures (절차)

1. **`manifest.json` 권한 다이어트 적용 및 불필요 권한 제거:** [★★★★★]
   - `permissions` 배열에서 미사용 항목(`activeTab`, `scripting`, `contextMenus`, `declarativeNetRequest`)을 삭제하고 `storage`만 유지.
   - `host_permissions`에 `<all_urls>`를 선언하여 전역 인라인 번역 및 다중 API 통신 지원.
   ```json
   {
     "manifest_version": 3,
     "name": "__MSG_appName__",
     "version": "1.0.0",
     "description": "__MSG_appDescription__",
     "default_locale": "ko",
     "permissions": [
       "storage"
     ],
     "host_permissions": [
       "<all_urls>"
     ],
     "background": {
       "service_worker": "src/background/index.js",
       "type": "module"
     }
   }
   ```

2. **구글 심사용 영문 소명서 및 개인정보 보호 항목 작성:** [★★★★★]
   - **단일 목적 소명 (Single Purpose):**
     > "Web Translator is designed solely to provide inline web page translation and text-selection dictionary lookup using multiple translation engines (Google Translate, Gemini AI, OpenAI, Claude, and local LLMs)."
   - **호스트 권한 (`<all_urls>`) 소명:**
     > "The extension requires `<all_urls>` permission to detect and translate text content across websites the user visits upon pressing the translation shortcut (Alt+A), and to communicate with user-configured translation API endpoints (Google Translate, Gemini API, OpenAI API, Anthropic API, and local Ollama server)."
   - **`storage` 권한 소명:**
     > "Used to save user preferences, custom style options, API keys, and local translation dictionary cache securely in the browser."
   - **데이터 사용 선언:** "아니요, 사용자 데이터를 수집하거나 사용하지 않습니다" 선택 및 정책 준수 서약.

3. **배포 패키지 빌드 및 스토어 제출:** [★★★★★]
   - 불필요한 소스 및 테스트 파일(`.git`, `.md`, 임시 캐시 등)을 배제하고 필수 런타임 파일만 포함된 `dist/web-translator-v1.0.0.zip` 빌드.
   - 크롬 웹 스토어 개발자 대시보드에 업로드 후 '검토를 위해 제출(Submit for Review)' 클릭하여 `In Review(검토 대기)` 상태로 전환.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[개발 초기 방치된 부가 권한으로 인한 심사 반려 위험]**
  * 증상: 매니페스트에 실제 UI에서 쓰이지 않는 `contextMenus`, `scripting`, `declarativeNetRequest` 등의 권한이 남아 있어 개발자 대시보드에서 각 권한별 데이터 사용 소명을 요구받고 심사 거절 위험 발생.
  * 원인: 초기 프로토타입 작성 당시 기능 확장 가능성을 고려하여 선언해 두었던 권한들이 정리되지 않고 방치됨.
  * 해결법: 단축키 및 드래그 팝업 기반으로 설계가 확정되었으므로 우클릭 메뉴 및 동적 스크립트 권한을 전면 삭제하고, 오직 `storage` 단 1개만 `permissions`에 남겨 최소 권한 구조로 정리함. [FACT]
  * 신뢰도: [★★★★★]

* **[호스트 권한 `<all_urls>`의 정당화 사유 부재 시 심사 거절 우려]**
  * 증상: 모든 사이트 접근 권한(`<all_urls>`)은 구글 보안 정책상 고위험 권한으로 분류되어 구체적 사유가 없으면 승인이 거부됨.
  * 원인: 단축키 기반 전체 페이지 인라인 번역 특성상 임의 사이트 감지가 필요하며, 사용자가 로컬(Ollama: `localhost:11434`) 및 다양한 클라우드 API를 커스텀 설정하므로 특정 도메인으로 한정하기 어려움.
  * 해결법: 인라인 번역 단축키 감지와 다중 엔드포인트(로컬 LLM, 자체 호스팅 서버, 공용 API) 통신 목적을 명시한 영문 소명서를 구체적으로 작성하여 개인정보 보호 탭에 제출함. [FACT]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 크롬 확장 프로그램 심사에서 권한 목록은 적을수록 유리하며, 권한 1개가 추가될 때마다 개발자가 작성해야 할 소명과 구글 검토 단계가 기하급수적으로 늘어난다.
* [FACT] `host_permissions`에 `<all_urls>`를 사용할 때는 단순히 "모든 사이트 번역용"이라고 짧게 적기보다, 인라인 번역 트리거 방식과 로컬/외부 API 통신 범위를 구체적으로 명시해야 단일 검토로 승인될 가능성이 높다.
* [OPINION] 배포 zip 패키지에는 빌드에 불필요한 마크다운 문서나 스크린샷 원본, 설정 템플릿이 들어가지 않도록 `manifest.json`, `src/`, `_locales/`, `icons/`, `content.css` 등 런타임 필수 파일만 엄격하게 압축하는 것이 파일 용량과 심사 관점에서 깔끔하다.

---

#### ❓ Missing Info (검증 필요 항목)

* [ ] `dist/web-translator-v1.0.0.zip` 압축 파일 내에 불필요한 임시 파일이나 소스 문서가 포함되지 않고 정상 빌드되었는지 최종 확인
* [ ] 크롬 웹 스토어 개발자 대시보드의 '개인정보 보호 관행' 탭에서 `<all_urls>` 및 `storage` 권한 사유가 정확히 입력되었는지 확인
* [ ] 스토어 상태가 `In Review(검토 대기)`로 정상 전환되었는지 확인

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, ChromeWebStore, ManifestV3, LeastPrivilege, PermissionsDiet, HostPermissions, StoragePermission, StoreSubmission, InReview

===== KNOWLEDGE PACKAGE END =====
