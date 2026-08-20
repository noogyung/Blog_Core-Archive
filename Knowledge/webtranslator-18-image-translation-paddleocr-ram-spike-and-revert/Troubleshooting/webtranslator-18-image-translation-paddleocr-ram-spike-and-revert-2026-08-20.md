---
topic: webtranslator-18-image-translation-paddleocr-ram-spike-and-revert
title_kr: 이미지 번역 R&D 3차: PaddleOCR 메모리 폭증과 v1.0 탑재 철회
category: Troubleshooting
sub_category: Image-OCR-PaddleOCR
version: 1.0.0
status: Verified
created_date: 2026-08-20
last_modified: 2026-08-20
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, ImageTranslation, 이미지번역, OCR, PaddleOCR, ONNXRuntime, WASM, WebAssembly, MemorySpike, 롤백, 트러블슈팅]
sources_count: 5
blog_draft_path: Blog_Posts/2026-08/webtranslator-18-image-translation-paddleocr-ram-spike-and-revert.html
blog_draft_date: 2026-08-20
blog_id: core-archive
blog_published: true
series_id: webtranslator
---

# Knowledge File: webtranslator-18-image-translation-paddleocr-ram-spike-and-revert
## Category: Troubleshooting (Image-OCR-PaddleOCR)
## Date: 2026-08-20

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-18-image-translation-paddleocr-ram-spike-and-revert
* **Title_KR:** 이미지 번역 R&D 3차: PaddleOCR 메모리 폭증과 v1.0 탑재 철회
* **Category:** Troubleshooting
* **Sub-Category:** Image-OCR-PaddleOCR
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-20
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 18편 개발 및 트러블슈팅 기획 문서 (`docs/blog/18_image_translation_paddleocr_ram_spike_and_revert.md`)
* [★★★★★] WebTranslator 패키지 및 모듈 종속성 설정 (`package.json`, `node_modules/ppu-paddle-ocr/`, `@paddleocr/paddleocr-js`)
* [★★★★★] ONNX Runtime Web (`onnxruntime-web`) WebAssembly Heap 확장 및 브라우저 메모리 관리 공식 기술 문서
* [★★★★☆] WebTranslator 실험 모듈 격리 아키텍처 (`modules/image_translator/`, `src/api/_archive/vision_ocr.js`)
* [★★★★☆] PP-OCRv5 Mobile 모델 텍스트 검출(Detection) 및 인식(Recognition) 이중 파이프라인 명세

---

#### 🔑 Core Concepts (핵심 개념)

* **[온디바이스 전문 OCR (PaddleOCR / PP-OCRv5) 3차 R&D 도입 배경]:**
  * 1차 Tesseract.js의 비정형/세로쓰기 텍스트 미검출과 2차 Multimodal Vision AI의 좌표 오차 및 지속적인 API 호출 비용 문제를 해결하기 위해, 무료 오픈소스 기반의 고성능 온디바이스 OCR 모델인 PaddleOCR(PP-OCRv5 Mobile)을 브라우저 WebAssembly(ONNX Runtime WASM) 환경에 직접 탑재하려 시도함. [FACT]
  * 브라우저 탭 내에서 텍스트 검출(Detection) 모델로 글자 영역 사각형 좌표를 정밀하게 추출하고, 인식(Recognition) 모델로 원문을 판독하여 기존 번역 파이프라인과 결합하는 것을 목표로 설정함. [FACT]
* **[ONNX Runtime Web WASM의 브라우저 탭 메모리 점유 메커니즘]:**
  * WebAssembly 선형 메모리(Linear Memory Heap)는 동작 특성상 런타임 피크 시점에 확장된 후 브라우저 가비지 컬렉터(GC)에 의해 자동으로 축소/반환되지 않음. [FACT]
  * 수십 MB 크기의 ONNX 가중치 파일 2종(Detection + Recognition)을 브라우저 탭 컨텍스트에서 직접 로드하면서, 초기 모델 역직렬화 및 텐서 할당에 8~10초가 소요되고 탭당 500MB 이상의 심각한 RAM 점유율 폭증(Memory Spike)이 발생함. [FACT]
* **[텍스트 검출(Detection) 파편화와 인식(Recognition) 붕괴]:** *(사용자 검증 수정 — 2026-08-20)*
  * 웹 환경의 다양한 폰트와 이미지 해상도에 대해 Detection 모델이 문장/단어 단위가 아닌 글자·획 단위로 과도하게 잘게 쪼개진 바운딩 박스를 대량 생성함. [FACT]
  * 오류율 필터를 비활성화하여 텍스트를 강제로 추출했을 때에는 텍스트 일부를 가져오는 데 성공하기는 했으나, 실제로는 존재하지 않는 텍스트 오인식(배경 하이라이트나 이미지 노이즈를 글자로 인식하여 임의의 환각 텍스트 생성), 텍스트 인식 퀄리티 및 정확도 저하 등의 문제가 발생함. [USER VERIFIED]
  * 콘솔에는 단순 `[WT PaddleOCR] 텍스트 없음` 외에도 부정확한 텍스트 및 허위 검출 텍스트가 출력되었으며, 오류율이 매우 높아 정상적인 텍스트로 판정되지 않아 최종적으로 텍스트 없음(미검출)으로 처리/드롭됨. [USER VERIFIED]
* **[환각 보고서 폐기 및 v1.0.0 정식 출시를 위한 과감한 지시 번복 & 코드 격리]:**
  * AI가 거짓으로 작성하려던 정상 작동 보고서를 전면 폐기하고, 실사용 불가 상태임을 객관적으로 확인한 즉시 이미지 번역 개발을 전면 중단함. [FACT]
  * 이미지 번역 실험 코드를 모조리 롤백하여 `modules/image_translator/`로 비활성 격리하고, 기존 텍스트 번역(Alt+A), 단어 사전 팝업, 다중 LLM 엔진 연동 파이프라인의 무결성을 복원하여 v1.0.0 완성도를 우선 확보함. [FACT]

---

#### 🛠️ Procedures (절차)

1. **ONNX Runtime Web 및 PaddleOCR 모듈 초기화 구성 시도:** [★★★★★] ✅ Verified 2026-08-20
   - `@paddleocr/paddleocr-js` 및 `ppu-paddle-ocr` 라이브러리를 활용하여 `PP-OCRv5_mobile_det` 및 `PP-OCRv5_mobile_rec` ONNX 가중치를 브라우저 캐시 및 WASM 세션에 로드.
   ```text
   paddle_ocr.js:63 [WT PaddleOCR] 캐시 히트: PP-OCRv5_mobile_det
   paddle_ocr.js:63 [WT PaddleOCR] 캐시 히트: PP-OCRv5_mobile_rec
   ```

2. **캔버스 캡처 데이터의 텐서 변환 및 추론 파이프라인 실행 시도:** [★★★★★] ✅ Verified 2026-08-20
   - 웹 이미지 요소를 캔버스에 렌더링하고 `Float32Array` 텐서로 변환하여 Detection 추론 실행.
   - 검출된 다각형 박스를 직사각형으로 보정하여 크롭 후 Recognition 세션으로 전달.

3. **실패 원인 분석 및 이미지 번역 코드 비활성화 격리 (Rollback):** [★★★★★] ✅ Verified 2026-08-20
   - 브라우저 메인 탭에서의 무거운 WASM 딥러닝 모델 직접 구동 중단.
   - `manifest.json`의 불필요한 WASM/가중치 리소스 선언 및 Background 스크립트의 이미지 번역 라우팅을 정리하고 실험 코드를 `modules/image_translator/`로 안전하게 격리.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[WASM 런타임 모델 로딩으로 인한 브라우저 탭 500MB RAM 폭증 및 프리징]**
  * 증상: 확장 프로그램이 로드되거나 이미지 번역을 시도할 때 탭 메모리가 500MB 이상 급증하고 8~10초간 브라우저가 버벅임.
  * 원인: WASM Linear Memory의 비반환 특성과 메인 스레드 컨텍스트에서의 수십 MB 대형 ONNX 가중치 모델 2종 동시 역직렬화.
  * 해결법: 브라우저 메인 탭에서의 무리한 온디바이스 딥러닝 로딩을 즉시 중단하고, 추후 격리된 Web Worker / WebGPU 전용 구조 또는 서버리스 API 전환 과제로 이관. [FACT]
  * 신뢰도: [★★★★★]

* **[글자 단위 박스 난잡화 및 Recognition 인식률 붕괴와 허위 텍스트 생성]** *(사용자 검증 수정 — 2026-08-20)*
  * 증상: 바운딩 박스가 무의미하게 미세 단위로 쪼개져 렌더링되고, 콘솔에 `[WT PaddleOCR] 텍스트 없음` 외에도 배경 하이라이트 오인식으로 인한 임의의 허위 텍스트 및 부정확한 텍스트가 출력됨.
  * 원인: 웹 환경의 다양한 비정형 폰트/해상도에 대한 Detection 임계값 미세조정 실패, 크롭 영역 전처리 텐서 정규화 불일치, 배경 노이즈/하이라이트를 텍스트로 오인식. 오류율이 극도로 높아 프로덕션 필터링 기준에서 유효한 텍스트로 통과되지 못함.
  * 해결법: 실사용 수준의 품질 달성이 불가능함을 판단하고, 환각 보고서를 폐기한 뒤 이미지 번역 파이프라인 개발을 전면 중단하고 실험 모듈을 안전하게 격리 롤백함. [USER VERIFIED]
  * 신뢰도: [★★★★★]

* **[미완성 실험 기능으로 인한 기존 텍스트 번역 안정성 저해 위험]**
  * 증상: 미완성 이미지 번역 모듈이 전역 스크립트에 엮여 기존 텍스트 번역 및 단어 사전 기능에 잠재적 오류를 유발할 위험성 상존.
  * 해결법: v1.0.0 정식 출시를 위해 이미지 번역 코드를 `modules/image_translator/`에 완전 격리하고 기존 텍스트 번역 파이프라인 롤백 및 베이스라인 복원. [FACT]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 브라우저 내에서 무거운 온디바이스 딥러닝 모델(OCR 등)을 구동할 때는 WebAssembly 힙 메모리 누수 방지를 위해 메인 스레드가 아닌 생명주기 제어가 가능한 Web Worker나 WebGPU 환경이 필수적이다.
* [OPINION] 릴리즈 일정이 정해진 프로젝트에서는 불완전한 신규 기능을 억지로 밀어붙이기보다, 과감한 기능 롤백과 격리를 통해 핵심 기능의 품질과 안정성을 지키는 판단이 엔지니어링 관점에서 훨씬 중요하다.

---

#### ❓ Missing Info (검증 필요 항목)

* [x] PaddleOCR의 탭 램 점유율(500MB+)과 초기화 지연(8~10초) 수치가 실제 테스트 환경에서 기록된 수치와 일치함을 확인 — Verified 2026-08-20
* [x] `modules/image_translator/`로 격리된 코드 외에 기존 `src/` 내 텍스트 번역 코드에 잔여 의존성이 완전히 제거되었음을 확인 — Verified 2026-08-20
* [x] v1.0.0 출시 버전에서 이미지 번역 제외 후 텍스트 번역 파이프라인이 정상 동작함을 확인 — Verified 2026-08-20

---

## 📝 Feedback History

### 2026-08-20 (1차) — Test Result: PASS
* **환경:** Windows 11, Chrome Extension MV3, WebTranslator v1.0.0
* **검증된 단계:** 1단계 ~ 3단계 전체 검증 완료
* **피드백 내용:**
  1. PaddleOCR 탭 램 점유율(500MB+) 및 초기화 지연(8~10초) 측정 수치 일치 확인.
  2. `modules/image_translator/` 격리 후 기존 `src/` 내 잔여 의존성 완전 제거 및 텍스트 번역 파이프라인 정상 동작 검증 완료.
  3. Detection/Recognition 실패 양상 상세화: 오류율 무시 시 텍스트 추출 자체는 일부 성공했으나, 바운딩 박스 세분화 파편화, 배경 하이라이트/노이즈를 글자로 오인식하여 허위 텍스트를 생성하는 문제, 텍스트 정확도 저하 확인. 오류율이 높아 유의미한 텍스트로 판정되지 않아 최종 '텍스트 없음'으로 드롭 처리되었음을 명시.
* **Status 변경:** Experimental → Verified

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, ImageTranslation, 이미지번역, OCR, PaddleOCR, ONNXRuntime, WASM, WebAssembly, MemorySpike, 롤백, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
