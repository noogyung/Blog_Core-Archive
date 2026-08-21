---
topic: webtranslator-20-chat-icon-replacement-and-real-screenshot-capture
title_kr: 크롬 웹 스토어 출시를 위한 모든 준비: 아이콘 교체, 실물 스크린샷, 심사 필수 요건 총정리
category: Troubleshooting
sub_category: Store-Assets-Design
version: 1.0.0
status: Verified
created_date: 2026-08-21
last_modified: 2026-08-21
language: KR+EN
tags: [WebTranslator, ChromeExtension, ChromeWebStore, ManifestV3, StoreAssets, StorePublish, IconDesign, Screenshot, 심사정책, 트러블슈팅, 배포가이드]
sources_count: 5
blog_draft_path: Blog_Posts/2026-08/webtranslator-20-chat-icon-replacement-and-real-screenshot-capture.html
blog_draft_date: 2026-08-21
blog_id: core-archive
blog_published: true
series_id: webtranslator
---

# Knowledge File: webtranslator-20-chat-icon-replacement-and-real-screenshot-capture
## Category: Troubleshooting (Store-Assets-Design)
## Date: 2026-08-21

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-20-chat-icon-replacement-and-real-screenshot-capture
* **Title_KR:** 크롬 웹 스토어 출시를 위한 모든 준비: 아이콘 교체, 실물 스크린샷, 심사 필수 요건 총정리
* **Category:** Troubleshooting
* **Sub-Category:** Store-Assets-Design
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-21
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 20편 개발 및 스토어 배포 기획 문서 (`docs/blog/20_chat_icon_replacement_and_real_screenshot_capture.md`, `docs/CHROME_STORE_PUBLISH_GUIDE.md`)
* [★★★★★] WebTranslator 매니페스트 및 아이콘 설정 파일 (`manifest.json`, `icons/icon*.png`)
* [★★★★★] WebTranslator 실제 스토어 등록용 스크린샷 에셋 (`dist/screenshots/`, `images/`)
* [★★★★★] Chrome Web Store Developer Program Policies (Misleading Metadata, Privacy Practices, Single Purpose)
* [★★★★☆] Google Chrome Extensions Documentation (Manifest Icons & Store Listing Specifications)

---

#### 🔑 Core Concepts (핵심 개념)

* **[크롬 웹 스토어 정식 출시 필수 요건 총망라 (Store Publish Checklist)]:**
  * 크롬 확장 프로그램을 스토어에 정식 배포하기 위해 준비해야 하는 5대 영역. [FACT]
    1. **개발자 계정 등록 및 인증:** 구글 계정 2단계 인증(2FA), 개발자 등록비($5 일회성), 게시자 연락처 이메일(Publisher Email) 등록 및 인증 링크 확인.
    2. **배포 패키지 구성 (MV3):** `manifest_version: 3` 준수, 다국어 메시지(`_locales/`), 불필요한 파일(`.git`, 임시 파일)이 배제된 순수 배포용 `.zip` 패키징.
    3. **그래픽 에셋 규격:** 공식 아이콘 3종(16x16, 48x48, 128x128 PNG), 스토어 스크린샷(1280x800px 또는 640x400px, 1~5장), 프로모션 타일(선택: 440x280px).
    4. **스토어 등록 정보 (Metadata):** 확장 프로그램 이름(최대 45자), 요약 설명(최대 132자), 상세 설명(기능, 단축키, 지원 엔진 목록), 카테고리 설정.
    5. **개인정보 보호 관행 (Privacy Practices):** 단일 목적 설명(Single Purpose), 선언된 권한별 사유 작성, 사용자 데이터 비수집 인증, 개인정보처리방침 URL.
* **[권한 수와 개인정보 보호 항목 작성 부담의 상관관계]:**
  * 실제 WebTranslator에 필요한 권한은 오직 `storage`와 `host_permissions`(`<all_urls>`) 2개뿐임. [FACT]
  * 매니페스트에 선언된 권한이 늘어날수록 개발자 대시보드의 '개인정보 보호 관행' 탭에서 각 권한마다 데이터 수집 여부와 사유를 개별 작성해야 하므로 심사 작성 항목이 크게 증가함. [FACT]
  * 개발 과정에서 AI가 임의로 추가해 둔 불필요한 권한들을 정리하고 실제 사용하는 2개 권한만 남겨야 함. [FACT]
* **[번역 도구의 시각적 정체성과 아이콘 교체의 현실적 접근]:**
  * 초기 임시로 적용되었던 말풍선 형태의 아이콘은 번역기보다는 채팅/메신저 앱으로 인식될 위험이 큼. [FACT]
  * 아이콘 디자인에 과도한 시간을 쓰지 않으면서 번역 도구임을 직관적으로 알 수 있도록 지구본과 언어 전환 기호(A/文)가 결합된 16px, 48px, 128px PNG 아이콘으로 교체함. [OPINION]
* **[스토어 스크린샷 정책과 실물 캡처 기반 AI 합성]:**
  * 구글 크롬 웹 스토어는 실제 UI와 무관한 과장된 AI 생성 그래픽을 오해 소지가 있는 메타데이터(Misleading Metadata)로 간주하여 심사를 반려함. [FACT]
  * 완전한 가상 AI 이미지를 배제하고, 실제 웹사이트에서 동작하는 화면들을 직접 캡처한 뒤 여러 캡처본을 하나의 1280x800 이미지로 정돈/배치하는 데에만 AI를 보조 도구로 사용함. [FACT]
* **[시리즈 연속성: 20편(출시 요건 총정리) → 21편(권한 정리 및 최종 출시)]:**
  * 이번 20편에서는 크롬 웹 스토어 출시에 필요한 모든 준비 사항과 에셋 구성 과정을 정리함. [FACT]
  * 다음 21편에서는 AI가 추가했던 불필요한 권한들을 제거하고 실제 필요한 `storage`와 `host_permissions` 2개만 남겨 개인정보 보호 항목을 작성하고 출시 버튼을 누르기까지의 과정을 다룸. [FACT]

---

#### 🛠️ Procedures (절차)

1. **`manifest.json` 내 아이콘 선언 및 규격별 매핑:** [★★★★★] ✅ Verified 2026-08-21
   - 16x16(파비콘/컨텍스트 메뉴), 48x48(확장 프로그램 관리 페이지), 128x128(웹 스토어) 규격의 투명 배경 PNG 아이콘을 `icons/` 및 `action.default_icon`에 연동.
   ```json
   {
     "icons": {
       "16": "icons/icon16.png",
       "48": "icons/icon48.png",
       "128": "icons/icon128.png"
     },
     "action": {
       "default_title": "__MSG_appName__",
       "default_popup": "optionPopup.html",
       "default_icon": {
         "16": "icons/icon16.png",
         "48": "icons/icon48.png",
         "128": "icons/icon128.png"
       }
     }
   }
   ```

2. **크롬 웹 스토어 등록용 1280x800 실물 스크린샷 제작 (실제 캡처 + AI 배치 보조):** [★★★★★] ✅ Verified 2026-08-21
   - 가짜 AI 그래픽을 배제하고, 직접 캡처한 실제 프로그램 구동 화면을 1280x800 규격의 이미지로 구성:
     - `screenshot_1_fullpage_1280x800.png`: Steam 상점 페이지 인라인 번역 화면.
     - `screenshot_2_popup_1280x800.png`: 단어 드래그 사전 카드 화면.
     - `screenshot_3_options_1280x800.png`: 번역 엔진 및 옵션 설정 화면.
     - `screenshot_4_toolbar_1280x800.png`: 툴바 팝업 화면.

3. **크롬 웹 스토어 출시를 위한 5대 준비물 체크리스트 확립:** [★★★★★] ✅ Verified 2026-08-21
   - **계정:** 구글 계정 2단계 인증 + 개발자 등록비 $5 결제 + 게시자 이메일 인증.
   - **패키지:** `dist/web-translator-v1.0.0.zip` (빌드 파일만 압축).
   - **에셋:** 16/48/128px 공식 아이콘 + 1280x800 실물 스크린샷 4종.
   - **등록 정보:** 한국어/영어 다국어 스토어 설명문.
   - **개인정보 보호 관행:** 필수 권한 2개(`storage`, `host_permissions`) 확정 및 사유 작성 준비.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[채팅 말풍선 아이콘으로 인한 번역 도구 정체성 혼선]**
  * 증상: 크롬 툴바 및 스토어 썸네일에서 확장 프로그램이 번역기가 아닌 메신저/채팅 앱처럼 보여 사용자 직관성이 저하됨.
  * 원인: 초기 템플릿 단계에서 임시로 지정했던 말풍선 형태의 범용 아이콘 유지.
  * 해결법: 과도한 디자인 시간 소모를 피하고, 지구본과 언어 전환 기호(A/文)가 결합된 직관적인 번역 아이콘으로 교체함. [USER VERIFIED]
  * 신뢰도: [★★★★★]

* **[AI 생성 가상 스크린샷 등록 시도로 인한 스토어 심사 거절 위험]**
  * 증상: AI 에이전트가 실제 UI와 다른 과장된 AI 생성 그래픽을 스토어 스크린샷으로 등록하려 시도.
  * 원인: 크롬 웹 스토어의 오해 소지 메타데이터(Misleading Metadata) 정책 미준수.
  * 해결법: 가짜 AI 이미지를 배제하고, 개발자가 직접 캡처한 실물 구동 화면들을 하나의 1280x800 이미지로 구성/배치하는 데에만 AI를 보조 도구로 활용함. [USER VERIFIED]
  * 신뢰도: [★★★★★]

* **[AI가 임의로 추가한 불필요한 권한들로 인한 개인정보 보호 항목 작성 부담 증가]** *(사용자 검증 추가 — 2026-08-21)*
  * 증상: 개발자 대시보드 등록 시 각 권한별로 데이터 사용 목적과 소명을 일일이 작성해야 하여 심사 작성 항목이 불필요하게 늘어남.
  * 원인: 실제 필요한 권한은 `storage`와 `host_permissions` 2개뿐인데, AI가 개발 과정에서 불필요한 권한들을 임의로 추가해 둠.
  * 해결법: 사용하지 않는 권한들을 모두 제거하고 실제 필요한 `storage`와 `host_permissions` 2개만 남겨 개인정보 보호 항목을 최소화함. [USER VERIFIED]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] 아이콘 디자인에 시간을 과도하게 쓰기보다, 번역 도구라는 정체성을 명확히 전달할 수 있는 적정 수준으로 정리하고 기능과 출시 문서 준비에 집중하는 것이 효율적이다.
* [FACT] 스토어 스크린샷은 실제 브라우저 캡처 화면(1280x800)을 사용해야 구글 심사 정책을 준수할 수 있다.
* [FACT] 크롬 확장 프로그램 권한은 선언된 권한마다 개인정보 보호 관행 탭에서 개별 사유를 작성해야 하므로, 실제로 사용하는 최소한의 권한(`storage`, `host_permissions`)만 선언해야 작성 부담을 줄이고 심사 통과를 원활하게 할 수 있다.

---

#### ❓ Missing Info (검증 필요 항목)

* [x] `icons/icon16.png`, `icons/icon48.png`, `icons/icon128.png`가 툴바 및 확장 프로그램 목록에서 번역 아이콘으로 적절히 식별됨을 확인 — Verified 2026-08-21
* [x] 직접 캡처한 실물 화면을 바탕으로 제작된 1280x800 스크린샷들이 스토어 권장 규격을 충족함을 확인 — Verified 2026-08-21
* [x] 실제 필요한 권한이 `storage`와 `host_permissions` 2개임을 확인하고 불필요한 권한 정리 방향 확정 — Verified 2026-08-21

---

## 📝 Feedback History

### 2026-08-21 (2차) — Test Result: PASS
* **환경:** Windows 11, Chrome Extension MV3, WebTranslator v1.0.0
* **검증된 내용:**
  1. 실제 필요한 권한은 `storage`와 `host_permissions`(`<all_urls>`) 2개뿐임을 확인.
  2. 과장된 미사여구를 걷어내고, 권한 수에 따라 '개인정보 보호 관행' 탭에서 작성해야 하는 항목이 늘어나는 구조적 이유를 명시.
  3. AI가 개발 과정에서 임의로 추가했던 불필요한 권한들을 정리해야 하는 원인과 대처 방향을 명확히 반영.
* **Status:** Verified 유지

### 2026-08-21 (1차) — Test Result: PASS
* **환경:** Windows 11, Chrome Extension MV3, WebTranslator v1.0.0
* **피드백 내용:** 임시 말풍선 아이콘 교체, 실물 스크린샷 AI 배치 보조, 스토어 출시 5대 필수 요건 총정리 지식 구조화.
* **Status 변경:** Experimental → Verified

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, ChromeWebStore, ManifestV3, StoreAssets, StorePublish, IconDesign, Screenshot, 심사정책, 트러블슈팅, 배포가이드

===== KNOWLEDGE PACKAGE END =====
