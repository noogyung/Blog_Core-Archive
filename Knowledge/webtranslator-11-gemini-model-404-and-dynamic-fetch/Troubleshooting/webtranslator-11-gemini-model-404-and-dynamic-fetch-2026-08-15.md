---
topic: webtranslator-11-gemini-model-404-and-dynamic-fetch
title_kr: gemini-2.5-flash 404 사태와 실시간 가용 모델 동적 탐색
category: Troubleshooting
sub_category: API-Resilience
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, Gemini, DynamicModelFetch, 404Error, SelfHealing, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-11-gemini-model-404-and-dynamic-model-fetch.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: false
series_id: webtranslator
series_title: WebTranslator 개발기
series_part: 11
series_prev_slug: webtranslator-10-gemini-rate-limits-and-prompt-builder
---

# Knowledge File: webtranslator-11-gemini-model-404-and-dynamic-fetch
## Category: Troubleshooting (API-Resilience)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-11-gemini-model-404-and-dynamic-fetch
* **Title_KR:** gemini-2.5-flash 404 사태와 실시간 가용 모델 동적 탐색
* **Category:** Troubleshooting
* **Sub-Category:** API-Resilience
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] Google Generative AI API Models Reference (`GET /v1beta/models`)
* [★★★★★] WebTranslator Gemini 어댑터 소스 코드 (`src/background/engines/gemini.js`)
* [★★★★★] WebTranslator 실전 개발 및 디버깅 로그 (`55e0e726-ace7-4d89-80a2-5d9abb7eb7ad`, 커밋 `6abe2db`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[모델 하드코딩의 404 Not Found 위험]:** `models/gemini-2.5-flash`처럼 특정 모델명을 코드 내에 고정 문자열로 박아두면, 구글이 실험용 모델을 지원 중단(Deprecated)하거나 이름을 바꿀 때 모든 API 호출이 404 에러로 마비되는 결함. [FACT]
* **[임시 하드코딩 땜질의 한계]:** `gemini-1.5-flash-latest` 등으로 다른 문자열을 하드코딩하는 방식은 다음 버전업 시 똑같이 404 에러를 유발하는 임시방편에 불과함. [FACT]
* **[실시간 가용 모델 동적 탐색 (`getValidGeminiModel`)]:** 사용자의 API Key로 구글 엔드포인트(`GET /v1beta/models`)를 호출하여, 현재 사용 가능한 모델 중 `supportedGenerationMethods`에 `generateContent`가 포함되고 `flash` 계열인 최신 모델을 실시간으로 자동 선별하는 자가 치유(Self-Healing) 아키텍처. [FACT]
* **[메모리 캐싱 및 안전 Fallback]:** 한번 조회한 유효 모델명은 세션 메모리에 캐싱하여 불필요한 네트워크 왕복을 제거하고, API 조회 실패 시 안전 기본값(`gemini-1.5-flash`)으로 Fallback 처리. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: getValidGeminiModel 메모리 캐시 검사] [★★★★★]
1. `cachedModelName`이 존재하면 즉시 반환.

##### [2단계: GET /v1beta/models 실시간 조회] [★★★★★]
1. `https://generativelanguage.googleapis.com/v1beta/models?key=${apiKey}` 호출.
2. `data.models` 배열에서 `generateContent` 지원 및 `flash` 키워드를 포함하는 최신 모델 탐색.

##### [3단계: 모델명 정제 및 캐싱] [★★★★★]
1. `models/` 접두사를 제거하고 `cachedModelName`에 저장 후 최종 반환.

##### [4단계: 네트워크 실패 시 안전 Fallback] [★★★★★]
1. 조회 실패 시 기본 안전 모델(`gemini-1.5-flash`)을 반환하여 중단 방지.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[구글 모델 지원 중단으로 인한 404 Not Found 전면 중단]**
  * **증상:** `Error: 404 This model models/gemini-2.5-flash is no longer available to new users` 에러로 번역 마비.
  * **원인:** 특정 모델명을 소스 코드에 고정 문자열로 하드코딩함.
  * **해결법:** `getValidGeminiModel` 동적 탐색기를 구축하여 실시간 API 목록 조회 기반 자동 모델 선별 및 캐싱 도입. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 클라우드 AI 서비스(구글, OpenAI 등)는 모델 라이프사이클이 매우 빠르므로, 클라이언트 사이드에서 특정 모델명을 하드코딩하지 말고 메타데이터 엔드포인트를 통해 동적으로 탐색하는 것이 유지보수에 필수적이다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`55e0e726`, 커밋 `6abe2db`) 기반 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, Gemini, DynamicModelFetch, 404Error, SelfHealing, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
