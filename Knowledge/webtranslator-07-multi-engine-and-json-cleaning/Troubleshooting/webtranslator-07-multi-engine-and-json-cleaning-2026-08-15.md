---
topic: webtranslator-07-multi-engine-and-json-cleaning
title_kr: 구글·Gemini·Libre 다중 엔진 연동과 응답 정제
category: Troubleshooting
sub_category: LLM-Pipeline
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, MultiEngine, Gemini, OpenAI, Claude, Ollama, JSON, Regex, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-07-multi-engine-pipeline-and-json-cleaning.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_name: WebTranslator 개발기
series_part: 7
series_prev_slug: webtranslator-06-selection-translation-no-content-false-alarm
---

# Knowledge File: webtranslator-07-multi-engine-and-json-cleaning
## Category: Troubleshooting (LLM-Pipeline)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-07-multi-engine-and-json-cleaning
* **Title_KR:** 구글·Gemini·Libre 다중 엔진 연동과 응답 정제
* **Category:** Troubleshooting
* **Sub-Category:** LLM-Pipeline
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] Google Gemini 1.5 Flash / Pro API Reference & OpenAI / Anthropic / Ollama API Specifications
* [★★★★★] WebTranslator 다중 엔진 어댑터 소스 코드 (`src/background/translator.js`)
* [★★★★★] WebTranslator 실전 개발 및 디버깅 로그 (`6c086416`, `55808a27`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[다중 엔진 어댑터 패턴 (Multi-Engine Adapter Pattern)]:** Google Translate(기본 무료), Gemini 3.5 Flash, GPT-5.6 Luna, LibreTranslate(자체 호스팅 오픈소스 NMT)를 실전 검증하고, 향후 확장을 위해 Claude 및 Ollama 어댑터 규격을 선반영한 통합 번역 아키텍처. [FACT]
* **[JSON 배열 배치 번역 (Batch Translation)]:** 수십 개의 텍스트 노드를 개별 API로 쏘지 않고 `string[]` 배열 형태로 한 번에 묶어 요청하여 네트워크 오버헤드와 API 호출 비용을 90% 이상 절감. [FACT]
* **[LLM 마크다운 잡음 및 서술형 래핑 결함]:** LLM이 JSON 형식만 출력하도록 지시받아도 ```` ```json ```` 코드 블록이나 앞뒤 친절한 인사말(`Here is your translation:`)을 덧붙여 `JSON.parse`가 SyntaxError로 폭사하는 현상. [FACT]
* **[다단계 견고한 JSON 정제 파이프라인 (`parseAndCleanJson`)]:**
  1. 마크다운 백틱(`^```(?:json)?`, ````$````) 정규식 제거
  2. 첫 번째 `[`와 마지막 `]` 인덱스 슬라이싱을 통한 외부 설명 텍스트 완벽 차단
  3. 따옴표 깨짐 및 ASCII 제어문자(`\u0000-\u001F`) 2차 방어 보정 후 안전한 파싱. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: 다중 엔진 어댑터 인터페이스 구성] [★★★★★]
1. `TranslatorAdapter` 기본 클래스 선언 및 공통 에러 핸들러 구축.
2. 엔진별 API 엔드포인트, 헤더, 인증 토큰 규격에 맞춘 하위 핸들러 구현.

##### [2단계: JSON 배열 배치 요청 생성] [★★★★★]
1. Content Script에서 수집된 텍스트 배열을 `JSON.stringify(texts)`로 패키징.
2. "Translate the following JSON array to Korean. Output ONLY the JSON array without markdown formatting" 시스템 프롬프트 주입.

##### [3단계: parseAndCleanJson 정제 파이프라인 가동] [★★★★★]
1. 회신된 원시 문자열에서 마크다운 코드 블록 제거.
2. `indexOf("[")` ~ `lastIndexOf("]") + 1` 슬라이싱으로 순수 배열 블록 분리.
3. `JSON.parse` 실행 및 1:1 매핑 배열 반환.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[Gemini / GPT 응답 수신 시 SyntaxError 발생으로 번역 실패]**
  * **증상:** LLM API 호출은 200 OK로 성공했으나 번역 결과가 브라우저에 표시되지 않고 콘솔에 `SyntaxError: Unexpected token '`'` 에러 발생.
  * **원인:** LLM이 마크다운 백틱 및 앞뒤 안내 문구를 포함하여 응답함.
  * **해결법:** `parseAndCleanJson` 함수를 구축하여 백틱 제거와 `[` ~ `]` 범위 슬라이싱으로 순수 JSON 데이터만 추출. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 실전 프로덕션에서 LLM의 출력을 JSON 파서에 바로 넣는 것은 매우 위험하며, 반드시 인덱스 슬라이싱과 정규식 기반의 정제 계층을 필수적으로 배치해야 한다.
* [FACT] 다중 LLM 엔진을 지원할 때는 엔진마다 다른 마크다운 서식 습관을 일관되게 정제할 수 있는 통합 파이프라인을 구축해야 유지보수 비용을 줄일 수 있다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`6c086416`, `55808a27`) 기반 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, MultiEngine, Gemini, OpenAI, Claude, Ollama, JSON, Regex, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
