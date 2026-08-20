---
topic: webtranslator-17-image-translation-tesseract-and-vision-bbox
title_kr: 이미지 번역 R&D 1·2차: Tesseract 미검출과 Vision AI Bounding Box 좌표 붕괴
category: Troubleshooting
sub_category: Image-OCR-VisionAI
version: 1.0.0
status: Experimental
created_date: 2026-08-20
last_modified: 2026-08-20
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, ImageTranslation, 이미지번역, OCR, Tesseract, VisionAI, GeminiVision, GPT4oVision, BoundingBox, 캔버스오버레이, 트러블슈팅]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: webtranslator
---

# Knowledge File: webtranslator-17-image-translation-tesseract-and-vision-bbox
## Category: Troubleshooting (Image-OCR-VisionAI)
## Date: 2026-08-20

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-17-image-translation-tesseract-and-vision-bbox
* **Title_KR:** 이미지 번역 R&D 1·2차: Tesseract 미검출과 Vision AI Bounding Box 좌표 붕괴
* **Category:** Troubleshooting
* **Sub-Category:** Image-OCR-VisionAI
* **Version:** 1.0.0
* **Status:** Experimental
* **Date:** 2026-08-20
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 17편 블로그 기획 문서 (`docs/blog/17_image_translation_tesseract_and_vision_bbox_failure.md`)
* [★★★★★] WebTranslator 백그라운드 이미지 Fetch 및 Base64 변환 파이프라인 (`src/background/imageService.js:L1-L103`)
* [★★★★★] WebTranslator Vision LLM 레이아웃 분석 및 Bounding Box 추출 API (`src/api/vision.js:L1-L237`, `src/api/_archive/vision_ocr.js`)
* [★★★★★] Google AI Gemini Vision Bounding Box 정규화(0~1000) 및 Spatial Grounding 공식 문서
* [★★★★☆] Tesseract.js WebAssembly OCR 아키텍처 및 복합 레이아웃/세로쓰기 한계 분석 기술 문서

---

#### 🔑 Core Concepts (핵심 개념)

* **[DOM 텍스트 순회의 한계와 이미지 내 텍스트 번역 요구]:**
  * 웹페이지 내 이미지 요소(`<img>`, `<canvas>`, `background-image`)나 해외 만화 말풍선, 게임 패치 일러스트, 쇼핑몰 배너는 DOM 텍스트 노드가 아니므로 기존 DOM 순회 파서(`TreeWalker`)로 추출할 수 없음. [FACT]
  * 이미지 내 외국어 텍스트를 감지하여 원본 텍스트 영역을 지우고 번역문을 자연스럽게 캔버스 오버레이로 렌더링하는 전용 이미지 번역 파이프라인 R&D가 요구됨. [FACT]
* **[1차 시도: Tesseract.js (WASM 온디바이스 OCR)의 검출 실패]:**
  * 브라우저 내에서 외부 서버 통신 없이 동작하는 WebAssembly 기반의 Tesseract.js를 도입하여 온디바이스 문자 인식을 시도함. [FACT]
  * Tesseract OCR 엔진은 표준 인쇄체 횡서(가로쓰기) 문서에 최적화되어 있어, 웹 일러스트의 종서(세로쓰기) 말풍선, 게임 전용 비정형 폰트, 그라데이션/노이즈가 포함된 배경 속 텍스트를 거의 검출하지 못하고 인식 실패(빈 문자열 반환)가 발생함. [FACT]
* **[2차 시도: Multimodal Vision AI (Gemini / GPT-4o Vision)와 Bounding Box 추출]:**
  * Tesseract.js를 폐기하고 이미지를 Base64 인코딩하여 멀티모달 Vision LLM에 전달, 텍스트 인식(OCR)과 함께 글자 영역의 사각형 좌표(`glyphBox`, `eraseBox`, `containerBox`)를 JSON 규격으로 반환받도록 파이프라인을 재설계함. [FACT]
  * Gemini Vision의 경우 이미지 좌상단 `[0, 0]`부터 우하단 `[1000, 1000]`까지의 정규화된 2D Bounding Box 좌표(`[ymin, xmin, ymax, xmax]`)를 반환함. [FACT]
* **[Vision AI의 Spatial Grounding(공간 좌표) 한계와 좌표 붕괴]:**
  * Vision LLM은 텍스트의 맥락적 이해와 번역문 생성 품질은 매우 우수하나, 토큰 기반 공간 분해능의 한계로 인해 픽셀 단위 경계 상자(Bounding Box) 예측에 상당한 오차가 발생함. [FACT]
  * 글자가 없는 허공에 사각형이 지정되거나, 말풍선 영역을 심하게 벗어나 캔버스 오버레이가 엉뚱한 위치에 그려지는 정렬 붕괴 현상이 빈발함. [FACT]
* **[DeclarativeNetRequest 기반 이미지 보안 제약 우회]:**
  * 외부 CDN이나 핫링크 방지가 걸린 이미지를 Background Script에서 `fetch`할 때 CORS 및 Referer 제약으로 다운로드가 차단되는 문제가 발생함. [FACT]
  * Chrome MV3의 `chrome.declarativeNetRequest.updateSessionRules`를 활용하여 세션 단위로 `Referer` 요청 헤더를 원본 웹페이지 URL로 변조한 후 Base64 Data URL로 변환하는 방식을 구현함 (`src/background/imageService.js`). [FACT]
* **[PaddleOCR로의 방향 전환 (지시 번복)]:**
  * 1차(Tesseract)의 인식 불가와 2차(Vision AI)의 좌표 오차를 모두 겪은 후, 정밀 광학 문자 검출 전용 딥러닝 모델인 PaddleOCR(PP-OCRv5)을 브라우저에 임베딩하는 3차 시도로 연구 방향을 전면 수정함. [FACT]

---

#### 🛠️ Procedures (절차)

1. **DeclarativeNetRequest를 활용한 이미지 다운로드 및 Base64 변환 (`src/background/imageService.js`):** [★★★★★]
   ```javascript
   export async function fetchImageAsBase64(imageUrl, refererUrl) {
     const ruleId = 9999;
     if (refererUrl && chrome.declarativeNetRequest) {
       await chrome.declarativeNetRequest.updateSessionRules({
         removeRuleIds: [ruleId],
         addRules: [{
           id: ruleId,
           priority: 1,
           action: {
             type: "modifyHeaders",
             requestHeaders: [{ header: "Referer", operation: "set", value: refererUrl }]
           },
           condition: {
             urlFilter: imageUrl,
             resourceTypes: ["xmlhttprequest", "image", "other"]
           }
         }]
       });
     }
     try {
       const response = await fetch(imageUrl);
       const blob = await response.blob();
       return new Promise((resolve, reject) => {
         const reader = new FileReader();
         reader.onloadend = () => resolve(reader.result);
         reader.onerror = () => reject(new Error("이미지 Base64 변환 실패"));
         reader.readAsDataURL(blob);
       });
     } finally {
       if (chrome.declarativeNetRequest) {
         await chrome.declarativeNetRequest.updateSessionRules({ removeRuleIds: [ruleId] });
       }
     }
   }
   ```

2. **Vision LLM 전용 OCR Layout Analyzer 프롬프트 설계 (`src/api/vision.js`):** [★★★★★]
   - `naturalWidth`, `naturalHeight` 정보와 함께 `glyphBox`, `eraseBox`, `containerBox`(`[ymin, xmin, ymax, xmax]`), `originalText`, `textColor`, `backgroundColor`를 구조화된 JSON으로 반환하도록 System Instruction 정의.

3. **정규화 좌표(0~1000)의 실제 픽셀 매핑 및 캔버스 오버레이 렌더링 시도:** [★★★★★]
   - 모델이 반환한 `[ymin, xmin, ymax, xmax]` 값을 원본 이미지의 픽셀 좌표(`Pixel_X = (xmin / 1000) * naturalWidth`)로 환산하여 캔버스에 사각형 마스크 및 번역 텍스트 드로잉.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[Tesseract.js WASM의 비정형 웹/세로쓰기 이미지 텍스트 미검출]**
  * 증상: 복잡한 일러스트 배너나 세로쓰기 말풍선 이미지를 입력했을 때 텍스트를 감지하지 못하고 빈 배열만 반환함.
  * 원인: Tesseract의 기존 사전 훈련 모델이 표준 문서(300 DPI 가로 인쇄물) 중심이라 웹 특유의 아트 폰트, 세로 문자열, 저해상도 픽셀 구조를 처리하지 못함.
  * 해결법: 브라우저 온디바이스 Tesseract 단독 처리 방식을 중단하고, 시각적 맥락 이해 능력이 뛰어난 멀티모달 Vision LLM으로 전환함. [FACT]
  * 신뢰도: [★★★★★]

* **[Vision AI의 Bounding Box 좌표 부정확성으로 인한 캔버스 오버레이 정렬 붕괴]**
  * 증상: Vision LLM이 텍스트 내용은 정확히 읽어냈으나, 반환된 Bounding Box 좌표가 실제 글자 위치와 수십~수백 픽셀씩 어긋나 엉뚱한 허공에 캔버스 번역문이 렌더링됨.
  * 원인: 일반 생성형 멀티모달 LLM은 이미지 전체의 의미를 파악하는 능력은 우수하지만, 픽셀 단위의 정밀한 공간 분해능(Spatial Resolution)이 부족하여 정확한 경계 좌표를 도출하지 못함.
  * 해결법: Vision LLM 단독 좌표 추출을 포기하고, 전문 광학 문자 검출(Detection) 모델인 PaddleOCR(PP-OCRv5)을 브라우저에 연동하는 3차 연구로 방향 전환을 결정함. [FACT]
  * 신뢰도: [★★★★★]

* **[외부 이미지 Fetch 시 CORS 차단 및 Hotlink 보안 에러]**
  * 증상: 타 도메인에 호스팅된 이미지 URL을 Background Script에서 로드할 때 403 Forbidden 또는 CORS 에러 발생.
  * 해결법: Chrome MV3 `declarativeNetRequest` 세션 규칙을 통해 다운로드 순간에만 `Referer`를 원본 페이지 도메인으로 임시 변조한 후 즉시 규칙을 정리하는 안전한 Fetcher 구현. [FACT]
  * 신뢰도: [★★★★★]

* **[Vision LLM 응답 내 마크다운 백틱 래핑으로 인한 JSON 파싱 오류]**
  * 증상: Vision API 응답에 ````json ... ```` 형태의 마크다운 래퍼가 포함되어 `JSON.parse` 실패 발생.
  * 해결법: 정규식(`replace(/^```json\s*/i, "")`)으로 불필요한 마크다운을 제거하는 안전한 파서(`parseVisionJsonResponse`) 도입. [FACT]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] Tesseract.js는 표준 스캔 문서가 아닌 웹상의 다양한 폰트와 세로쓰기 이미지를 온디바이스로 처리하기에는 모델 가중치와 구조적 한계가 뚜렷하다.
* [FACT] 범용 멀티모달 Vision AI(Gemini, GPT-4o)는 이미지 설명이나 번역 자체에는 탁월하지만, 픽셀 단위의 정밀한 Bounding Box 좌표를 요구하는 오버레이 작업에는 단독으로 사용하기 어렵다.
* [OPINION] 복잡한 이미지 번역 파이프라인을 구축할 때는 '문자 위치 검출(Detection)'과 '문자 판독(Recognition)', '맥락 번역(Translation)'을 단일 모델에 모두 의존하기보다 각 영역에 특화된 파이프라인으로 분리하는 것이 안정적이다.

---

#### ❓ Missing Info (검증 필요 항목)

* [ ] Tesseract.js 및 Vision AI에서 발생했던 실제 오차 범위(픽셀 오차 및 텍스트 누락 빈도)에 대한 추가 정량 데이터 확인 필요
* [ ] `fetchImageAsBase64`의 DeclarativeNetRequest 세션 룰 삭제 타이밍 및 동시 요청 시 룰 충돌 가능성 검토
* [ ] 2차 Vision AI 시도에서 프롬프트에 `naturalWidth`/`naturalHeight`를 명시했을 때와 0~1000 정규화 좌표를 사용할 때의 오차 발생 양상 비교

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, ImageTranslation, 이미지번역, OCR, Tesseract, VisionAI, GeminiVision, GPT4oVision, BoundingBox, 캔버스오버레이, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
