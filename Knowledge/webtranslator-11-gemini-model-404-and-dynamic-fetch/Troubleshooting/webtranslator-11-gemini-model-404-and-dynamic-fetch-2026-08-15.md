---
topic: webtranslator-11-gemini-model-404-and-dynamic-fetch
title_kr: 자동 모델 탐색의 과금 위험과 모델 헬퍼 도구 전환
category: Troubleshooting
sub_category: API-Resilience
version: 1.3.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-19
language: KR+EN
tags: [WebTranslator, ChromeExtension, Gemini, GPT, API, 404Error, CostOptimization, ModelHelper, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-11-gemini-model-404-and-dynamic-model-fetch.html
blog_draft_date: 2026-08-19
blog_id: core-archive
blog_published: true
series_id: webtranslator
series_title: WebTranslator 개발기
series_part: 11
series_prev_slug: webtranslator-10-gemini-rate-limits-and-prompt-builder
---

# Knowledge File: webtranslator-11-gemini-model-404-and-dynamic-fetch
## Category: Troubleshooting (API-Resilience)
## Date: 2026-08-19

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-11-gemini-model-404-and-dynamic-fetch
* **Title_KR:** 자동 모델 탐색의 과금 위험과 모델 헬퍼 도구 전환
* **Category:** Troubleshooting
* **Sub-Category:** API-Resilience
* **Version:** 1.3.0
* **Status:** Verified
* **Date:** 2026-08-19
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 모델 설정 및 옵션 헬퍼 소스 코드 (`src/options/model_helper.js`, `src/background/engines/gemini.js`, `src/background/engines/openai.js`)
* [★★★★★] Google Generative AI 무료 쿼터 체계(RPM/TPM/RPD) 및 OpenAI API 토큰 단가 기준표
* [★★★★★] WebTranslator 실전 개발 및 과금 디버깅 로그 (`55e0e726-ace7-4d89-80a2-5d9abb7eb7ad`, 커밋 `6abe2db`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[구형 모델 `gemini-2.5-flash`의 한계]:** 구형 모델로서 일일 무료 허용량(RPD 20)이 Flash-Lite(RPD 500)에 비해 25배나 적었고, 서비스 종료로 404 Not Found가 발생함. [FACT]
* **[Gemini 무료 쿼터 3대 축 (RPM / TPM / RPD)과 티어별 격차]:**
  * **Pro 계열**: 무료 허용량 거의 없음(0/0/0).
  * **Flash 계열 (2.5/3.5/3.6/3.7 Flash)**: RPM 5, TPM 250K, RPD 20 수준으로 일일 허용량(RPD)이 매우 낮음.
  * **Flash-Lite 계열 (3.1/3.5 Flash Lite)**: RPM 15, TPM 250K, RPD 500 수준으로 Flash 대비 RPD가 25배 높아 대용량 번역에 최적.
  * **`gemini-flash-lite-latest`**: 최신 안정화된 Flash-Lite 모델 중 허용량이 가장 넉넉한 버전을 자동으로 가리키는 공식 별칭(Alias). [FACT]
* **[OpenAI 유료 단가 구조와 자동 선택의 과금 위험]:**
  * OpenAI는 무료 플랜이 없으며, 모델별 1M 토큰당 가격(Input/Output) 차이가 극심함:
    * GPT-5.4 Pro ($30.00 / $180.00), GPT-5.4 mini ($0.75 / $4.50), GPT-5.4 nano ($0.20 / $1.25)
    * GPT-5.6 Sol ($5.00 / $30.00), GPT-5.6 Terra ($2.00 / $12.00), GPT-5.6 Luna ($0.20 / $1.20)
    * GPT-4o ($2.50 / $10.00), GPT-4o mini ($0.15 / $0.60)
  * AI 자동 선택기가 초저가 nano/luna 모델 대신 비싼 상위 모델(`gpt-4o`)을 임의 호출하여 선결제 $10 크레딧이 조기 소진되는 문제 발생. [FACT]
* **[자동 선택 폐기 및 사용자 직접 모델 고정]:** 런타임 자동 선택기를 전면 폐기하고, 사용자가 초가성비 모델(`gemini-flash-lite-latest`, `gpt-5.4-nano`/`gpt-5.6-luna`/`gpt-4o-mini`)을 직접 지정하도록 전환. [FACT]
* **[가용 모델 조회의 인스펙터 헬퍼 전환]:** API 메타데이터 조회를 옵션 페이지로 분리하여, 향후 무료 모델은 RPM/TPM/RPD 쿼터 지표를, 유료 모델은 입출력 단가를 시각화하여 안내하는 지능형 모델 인스펙터로 발전시킬 토대 마련. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: 번역 런타임 자동 모델 선택 로직 전면 제거] [★★★★★]
1. `callEngineAPI` 내부의 무조건적 자동 Fallback 및 동적 모델 교체 코드 삭제.
2. 사용자가 설정창에서 입력한 모델명을 최우선 고정 파라미터로 주입.

##### [2단계: RPD 및 단가 최적화 모델 고정 운용] [★★★★★]
1. Gemini: `gemini-flash-lite-latest` 설정으로 일일 요청 수(RPD 500) 한도 내 100% 무료 완주.
2. OpenAI: 초저가 모델(`gpt-5.4-nano` / `gpt-5.6-luna` / `gpt-4o-mini`) 설정으로 토큰 단가 최소화.

##### [3단계: 실시간 가용 모델 조회 API를 옵션 헬퍼 도구로 분리] [★★★★★]
1. `GET /v1beta/models` 조회를 옵션 페이지의 `fetchAvailableModels()` UI로 이동.
2. 쿼터 지표 및 모델명을 사용자에게 시각적으로 안내하는 헬퍼 역할만 수행.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[AI의 실시간 자동 모델 선택으로 인한 Gemini RPD 조기 고갈 및 OpenAI $10 크레딧 조기 소진]**
  * **증상:** Gemini는 RPD 한도가 낮은 Flash 모델(RPD 20)이 선택되어 번역이 조기 마비되고, OpenAI는 단가가 비싼 `gpt-4o`가 선택되어 선결제 $10 크레딧이 조기 소진됨.
  * **원인:** 번역 런타임에 모델별 RPD와 토큰 단가를 모르는 AI 자동 선택기를 넣어 부적절한 상위 모델을 호출함.
  * **해결법:** 자동 모델 선택기를 전면 폐기하고 초가성비 모델(`gemini-flash-lite-latest`, 저단가 GPT 모델)을 직접 고정 입력하도록 수정하였으며, 가용 모델 API는 옵션 페이지의 단순 조회 헬퍼 도구로 분리 격리. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] LLM API 연동 시 런타임 자동 승격(Auto-Escalation)을 허용하면 과도한 비용이 청구되거나 RPD가 조기 고갈되므로, 모델 선택은 사용자의 명시적 설정에 맡겨야 한다.
* [FACT] Gemini의 경우 Pro → Flash → Flash-Lite 순으로 일일 요청 수(RPD) 한도가 비약적으로 증가하므로 대량 번역에는 Flash-Lite 계열을 권장한다.

---

#### 📋 Feedback History
* **2026-08-19:** GPT-5.4 가격표 반영, gemini-2.5-flash 설명 정정, HTML 화살표 엔티티 적용 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, Gemini, GPT, API, 404Error, CostOptimization, ModelHelper, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
