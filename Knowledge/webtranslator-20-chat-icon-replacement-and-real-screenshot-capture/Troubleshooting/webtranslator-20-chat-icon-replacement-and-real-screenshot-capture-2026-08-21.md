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
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
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
* [★★★★★] Chrome Web Store Developer Program Policies (Misleading Metadata, Permissions Justification, Single Purpose)
* [★★★★☆] Google Chrome Extensions Documentation (Manifest Icons & Store Listing Specifications)

---

#### 🔑 Core Concepts (핵심 개념)

* **[크롬 웹 스토어 정식 출시 필수 요건 총망라 (Store Publish Checklist)]:**
  * 크롬 확장 프로그램을 스토어에 정식 배포하기 위해서는 코드 구현뿐만 아니라 다음 5대 영역의 필수 요건을 빈틈없이 갖추어야 함. [FACT]
    1. **개발자 계정 등록 및 인증:** 구글 계정 2단계 인증(2FA), 개발자 등록비($5 일회성), 게시자 연락처 이메일(Publisher Email) 등록 및 인증.
    2. **배포 패키지 구성 (MV3):** `manifest_version: 3` 준수, 다국어 메시지(`_locales/`), 불필요한 파일(`.git`, 임시 파일)이 배제된 순수 배포용 `.zip` 패키징.
    3. **그래픽 에셋 규격:** 공식 아이콘 3종(16x16, 48x48, 128x128 PNG), 스토어 스크린샷(1280x800px 또는 640x400px, 1~5장), 프로모션 타일(선택/권장: 440x280px).
    4. **스토어 등록 정보 (Metadata):** 확장 프로그램 이름(최대 45자), 요약 설명(최대 132자), 상세 설명(기능, 단축키, 지원 엔진 목록), 카테고리 설정.
    5. **개인정보 보호 및 심사 정책 (Privacy & Policy):** 단일 목적 설명(Single Purpose), 권한 소명서(Permissions Justification), 사용자 데이터 비수집 인증, 개인정보처리방침 URL.
* **[번역 도구의 시각적 정체성과 아이콘 교체의 현실적 접근]:**
  * 초기 임시로 적용되었던 '말풍선' 형태의 아이콘은 번역기보다는 채팅/메신저 앱으로 인식될 위험이 큼. [FACT]
  * 아이콘 디자인에 지나치게 많은 시간을 소모하지 않으면서도, 사용자가 직관적으로 번역 도구임을 인지할 수 있도록 지구본과 언어 전환 기호(A/文)가 결합된 16px, 48px, 128px 고해상도 PNG 아이콘으로 신속히 교체하여 개발 효율을 확보함. [OPINION]
* **[스토어 스크린샷 심사 정책과 실물 캡처 기반 AI 합성]:**
  * 구글 크롬 웹 스토어는 실제 UI와 무관한 과장된 AI 생성 그래픽을 '오해 소지가 있는 메타데이터(Misleading Metadata)'로 간주하여 심사를 반려함. [FACT]
  * 따라서 완전한 가상 AI 이미지를 전면 배제하고, 실제 웹사이트(Steam, MDN 등)에서 동작하는 화면들을 직접 캡처한 뒤, 여러 캡처본을 조화롭게 하나의 1280x800 이미지로 구성/배치하는 데에만 AI를 보조 도구로 활용함. [FACT]
* **[시리즈 연속성: 20편(출시 필수요건 총정리) → 21편(권한 소명 및 최종 출시 대처)]:**
  * 이번 20편에서는 크롬 웹 스토어 출시에 필요한 모든 준비 사항과 그래픽 에셋의 구축 과정을 총정리함. [FACT]
  * 다음 21편에서는 구글 개발자 대시보드 등록 과정에서 마주치는 가장 큰 난관인 '최소 권한 원칙(Least Privilege)'과 '권한 소명서 작성', 실제 심사 승인 및 출시 버튼을 누르기까지의 실전 대처 과정을 다룰 예정임. [FACT]

---

#### 🛠️ Procedures (절차)

1. **`manifest.json` 내 아이콘 선언 및 규격별 매핑:** [★★★★★] ✅ Verified 2026-08-21
   - 16x16(파비콘/컨텍스트 메뉴), 48x48(확장 프로그램 관리 페이지 `chrome://extensions`), 128x128(웹 스토어 및 설치 화면) 규격의 투명 배경 PNG 아이콘을 `icons/` 및 `action.default_icon`에 연동.
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
     - `screenshot_1_fullpage_1280x800.png`: Steam 상점 페이지 1:1 인라인 번역 및 원본 레이아웃 완벽 보존 화면.
     - `screenshot_2_popup_1280x800.png`: 단어 드래그 시 0.8초 만에 표시되는 발음/품사/예문 사전 카드.
     - `screenshot_3_options_1280x800.png`: 다양한 번역 엔진(Google, Gemini, OpenAI, Claude, Ollama) 및 다크모드 적응 설정 화면.
     - `screenshot_4_toolbar_1280x800.png`: 툴바 빠른 설정 팝업을 통한 번역 토글 및 글자 크기 실시간 조절.

3. **크롬 웹 스토어 출시를 위한 5대 준비물 체크리스트 확립:** [★★★★★] ✅ Verified 2026-08-21
   - **계정:** 구글 계정 2단계 인증 + 개발자 등록비 $5 결제 + 게시자 이메일 인증.
   - **패키지:** `dist/web-translator-v1.0.0.zip` (최소 파일 포함 빌드).
   - **에셋:** 16/48/128px 공식 아이콘 + 1280x800 실물 스크린샷 4종.
   - **등록 정보:** 한국어/영어 다국어 스토어 설명문 (기능 목록 및 단축키 명시).
   - **정책 소명:** 단일 목적 기술서 및 권한 소명 준비 (다음 편 실전 대처로 연계).

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[채팅 말풍선 아이콘으로 인한 번역 도구 정체성 혼선]**
  * 증상: 크롬 툴바 및 스토어 썸네일에서 확장 프로그램이 번역기가 아닌 메신저/채팅 앱처럼 보여 사용자 직관성이 저하됨.
  * 원인: 초기 템플릿 단계에서 임시로 지정했던 말풍선 형태의 범용 아이콘 유지.
  * 해결법: 과도한 디자인 시간 소모를 지양하고, 지구본과 다국어 문자(A/文)가 결합된 직관적인 번역 아이콘으로 신속히 교체하여 적정 수준의 식별성을 확보함. [USER VERIFIED]
  * 신뢰도: [★★★★★]

* **[AI 생성 가상 스크린샷 등록 시도로 인한 스토어 심사 거절 위험]**
  * 증상: AI 에이전트가 실제 UI와 동떨어진 과장된 AI 생성 그래픽을 스토어 스크린샷으로 등록하려 시도.
  * 원인: 크롬 웹 스토어의 "오해 소지 메타데이터(Misleading Metadata)" 정책 및 실제 작동 UI 필수 제출 규정에 대한 에이전트의 인지 부족.
  * 해결법: 가짜 AI 이미지를 전면 배제하고, 개발자가 직접 캡처한 실물 구동 화면들을 적절히 하나의 1280x800 이미지로 구성/배치하는 데에만 AI를 보조 도구로 활용하여 규정을 준수함. [USER VERIFIED]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] 확장 프로그램 아이콘 디자인에 지나치게 많은 시간을 들이기보다는, 번역 도구라는 정체성을 명확히 전달할 수 있는 적정 수준으로 정리하고 실제 스토어 심사 통과를 위한 정책 문서화에 집중하는 것이 현명하다.
* [FACT] 크롬 웹 스토어 심사관 및 사용자는 과장된 3D 렌더링보다 실제 웹사이트 내에서 어떻게 동작하는지 보여주는 정직한 실물 스크린샷(1280x800)에 훨씬 높은 신뢰를 부여한다.
* [FACT] 스토어 배포 전 체크리스트(개발자 2FA, 게시자 이메일 인증, 아이콘 3종, 1280x800 스크린샷, 단일 목적 설명)를 사전에 완벽히 구비해야 심사 지연과 반려를 방지할 수 있다.

---

#### ❓ Missing Info (검증 필요 항목)

* [x] `icons/icon16.png`, `icons/icon48.png`, `icons/icon128.png`가 툴바 및 확장 프로그램 목록에서 번역 아이콘으로 적절히 식별됨을 확인 — Verified 2026-08-21
* [x] 직접 캡처한 실물 화면을 바탕으로 제작된 1280x800 스크린샷들이 스토어 권장 규격을 완벽히 충족함을 확인 — Verified 2026-08-21
* [x] `manifest.json`의 아이콘 경로와 `dist/` 패키지 구성이 정상 동작함을 확인 — Verified 2026-08-21

---

## 📝 Feedback History

### 2026-08-21 (1차) — Test Result: PASS
* **환경:** Windows 11, Chrome Extension MV3, WebTranslator v1.0.0
* **검증된 단계:** 1단계 ~ 3단계 전체 검증 완료
* **피드백 내용:**
  1. 기존 임시 아이콘(말풍선)을 직관적인 번역 아이콘(지구본+A/文)으로 교체 적용 완료. (과도한 시간 소모 없이 적정 수준으로 타협)
  2. 스토어 스크린샷은 AI 생성 가짜 이미지를 전면 배제하고, 직접 찍은 실제 동작 스크린샷들을 하나의 1280x800 이미지로 구성/배치하는 데에만 AI를 보조 도구로 활용함.
  3. 단순 아이콘/스크린샷 변경을 넘어, 크롬 웹 스토어 출시에 필요한 모든 준비 사항(계정, MV3 패키지, 그래픽 에셋, 메타데이터, 정책/소명)을 총정리하는 지식 구조로 전면 보강.
  4. 다음 21편(권한 소명 및 최소 권한 원칙 실전 대처)과의 유기적 연결 맥락 확립.
* **Status 변경:** Experimental → Verified

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, ChromeWebStore, ManifestV3, StoreAssets, StorePublish, IconDesign, Screenshot, 심사정책, 트러블슈팅, 배포가이드

===== KNOWLEDGE PACKAGE END =====
