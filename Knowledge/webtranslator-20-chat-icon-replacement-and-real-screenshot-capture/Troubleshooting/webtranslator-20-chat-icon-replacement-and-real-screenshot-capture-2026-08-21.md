---
topic: webtranslator-20-chat-icon-replacement-and-real-screenshot-capture
title_kr: 채팅 아이콘 교체와 실물 스크린샷 캡처: AI 가짜 이미지 거부와 스토어 심사 대비
category: Troubleshooting
sub_category: Store-Assets-Design
version: 1.0.0
status: Experimental
created_date: 2026-08-21
last_modified: 2026-08-21
language: KR+EN
tags: [WebTranslator, ChromeExtension, ChromeWebStore, ManifestV3, StoreAssets, IconDesign, Screenshot, 심사정책, 트러블슈팅, 에셋준비]
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
* **Title_KR:** 채팅 아이콘 교체와 실물 스크린샷 캡처: AI 가짜 이미지 거부와 스토어 심사 대비
* **Category:** Troubleshooting
* **Sub-Category:** Store-Assets-Design
* **Version:** 1.0.0
* **Status:** Experimental
* **Date:** 2026-08-21
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 20편 개발 및 그래픽 에셋 기획 문서 (`docs/blog/20_chat_icon_replacement_and_real_screenshot_capture.md`)
* [★★★★★] WebTranslator 매니페스트 및 아이콘 설정 파일 (`manifest.json`, `icons/icon*.png`)
* [★★★★★] WebTranslator 실제 스토어 등록용 스크린샷 에셋 (`dist/screenshots/`, `images/`)
* [★★★★★] Chrome Web Store Developer Program Policies (Misleading Metadata and Screenshot Guidelines)
* [★★★★☆] Google Chrome Extensions Documentation (Manifest Icons Format and Sizing Standards)

---

#### 🔑 Core Concepts (핵심 개념)

* **[번역 도구의 시각적 정체성과 아이콘 디자인 직관성]:**
  * 기존에 적용되었던 말풍선 형태의 아이콘은 번역 확장 프로그램보다는 실시간 채팅/메신저 앱으로 오인될 소지가 큼. [FACT]
  * 크롬 툴바 및 확장 프로그램 관리 목록에서 즉각적으로 번역 도구임을 인지할 수 있도록, 지구본과 언어 전환 기호(A/文)가 결합된 직관적인 디자인 에셋으로 16px, 48px, 128px 고해상도 PNG를 구축함. [FACT]
* **[크롬 웹 스토어 심사 정책(Developer Program Policies)과 스크린샷 규격]:**
  * 구글 크롬 웹 스토어는 확장 프로그램의 실제 동작 화면과 무관한 과장된 홍보 그래픽이나 가상 렌더링(AI 생성 이미지 포함)을 "오해 소지가 있는 메타데이터(Misleading Metadata)"로 규정하여 심사를 거부하거나 등록을 취소함. [FACT]
  * 스토어 표준 스크린샷 규격은 1280x800px(또는 640x400px) PNG/JPEG이며, 실제 구동 화면을 1:1로 정직하게 담아내야 심사 승인율과 사용자 신뢰도를 확보할 수 있음. [FACT]
* **[AI 생성 이미지 전면 배제 및 실물 캡처 강제 원칙]:**
  * 개발 지원 AI 에이전트가 가상의 화려한 AI 생성 그래픽을 스크린샷으로 대체하려는 시도를 차단하고, 실제 브라우저에서 확장 프로그램이 작동하는 1280x800 실물 화면 4종을 직접 캡처하여 배포 패키지에 연동함. [FACT]

---

#### 🛠️ Procedures (절차)

1. **`manifest.json` 내 아이콘 선언 및 규격별 매핑:** [★★★★★]
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

2. **크롬 웹 스토어 등록용 1280x800 실물 스크린샷 4종 준비 및 구조화:** [★★★★★]
   - AI 생성 가상 이미지를 배제하고, 실제 웹사이트에서 동작하는 고해상도 실물 캡처 에셋 구축:
     - `screenshot_1_fullpage_1280x800.png`: Steam 상점 페이지 1:1 인라인 번역 및 레이아웃 보존 화면.
     - `screenshot_2_popup_1280x800.png`: 단어 드래그 시 0.8초 만에 표시되는 발음/품사/예문 사전 카드.
     - `screenshot_3_options_1280x800.png`: 옵션 페이지의 엔진 선택 및 다크모드 적응 렌더링 설정.
     - `screenshot_4_toolbar_1280x800.png`: 툴바 팝업을 통한 빠른 번역 토글 및 글자 크기 실시간 조절.

3. **`dist/` 배포 패키징 및 그래픽 에셋 무결성 검증:** [★★★★★]
   - 압축 빌드(`dist/web-translator-v1.0.0.zip`)에 불필요한 고용량 임시 이미지나 가상 생성물이 포함되지 않도록 정리하고, 스토어 업로드 규격 준수 상태 확인.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[채팅 말풍선 아이콘으로 인한 번역 도구 정체성 혼선]**
  * 증상: 크롬 툴바 및 스토어 썸네일에서 확장 프로그램이 번역기가 아닌 메신저/채팅 앱처럼 보여 사용자 직관성이 저하됨.
  * 원인: 초기 템플릿 단계에서 임시로 지정했던 말풍선 형태의 범용 아이콘 유지.
  * 해결법: 지구본과 다국어 문자(A/文)가 결합된 직관적인 번역 전용 아이콘을 16px, 48px, 128px 크기의 투명 PNG로 신규 제작하여 매니페스트에 반영함. [FACT]
  * 신뢰도: [★★★★★]

* **[AI 생성 가상 스크린샷 등록 시도로 인한 스토어 심사 거절 위험]**
  * 증상: AI 에이전트가 실제 UI와 동떨어진 과장된 AI 생성 그래픽을 스토어 스크린샷으로 등록하려 시도.
  * 원인: 크롬 웹 스토어의 "오해 소지 메타데이터(Misleading Metadata)" 정책 및 실제 작동 UI 필수 제출 규정에 대한 에이전트의 인지 부족.
  * 해결법: AI 생성 스크린샷을 전면 번복 및 거부하고, 개발자가 직접 1280x800 해상도로 캡처한 실제 Steam 번역 및 사전 팝업 실물 화면 4종으로 교체 등록함. [FACT]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] 확장 프로그램의 아이콘은 작은 크기(16x16)에서도 형태가 뭉개지지 않도록 단순하고 굵은 실루엣의 대비가 강한 벡터 기반 디자인을 사용하는 것이 유리하다.
* [FACT] 크롬 웹 스토어 심사관 및 잠재 사용자는 화려한 마케팅용 CG보다 실제 브라우저 내에서 어떻게 표시되는지 보여주는 실물 스크린샷(1280x800)에서 훨씬 높은 신뢰도를 갖는다.

---

#### ❓ Missing Info (검증 필요 항목)

* [ ] `icons/icon16.png`, `icons/icon48.png`, `icons/icon128.png`가 윈도우 다크모드 툴바 및 고해상도(HiDPI) 디스플레이에서 시인성이 충분한지 확인 필요.
* [ ] 1280x800 스크린샷 4종이 크롬 웹 스토어 개발자 대시보드 미리보기에서 왜곡 없이 선명하게 노출되는지 확인 필요.
* [ ] `manifest.json`의 `default_icon`과 `icons` 설정이 브라우저 로드 시 정상 인식되는지 확인 필요.

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, ChromeWebStore, ManifestV3, StoreAssets, IconDesign, Screenshot, 심사정책, 트러블슈팅, 에셋준비

===== KNOWLEDGE PACKAGE END =====
