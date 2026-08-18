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

* **[사용자 지정 모델의 가용성/쿼터 소진 한계]:** 초기 하드코딩에서 사용자 직접 모델명 입력 방식으로 전환했으나, 사용자가 지정한 모델의 무료 쿼터가 소진되거나 구글이 해당 프리뷰 모델을 내렸을 때 404 Not Found 및 호출 중단이 발생하는 문제. [FACT]
* **[무분별한 자동 Fallback의 고비용(High-Cost) 과금 리스크]:** 모델 오류 발생 시 시스템이 임의로 다른 상위 모델로 자동 대체할 경우, 특히 유료 모델(GPT-4/5 상위 티어, Gemini Pro 등) 환경에서 예상치 못한 막대한 API 비용 청구 참사가 발생할 위험. [FACT]
* **[고비용 방어형 실시간 모델 검증 (`getValidGeminiModel`)]:** 무조건적인 임의 대체를 금지하고, `GET /v1beta/models` 조회를 통해 사용자가 설정한 계정에서 사용 가능한 저비용/무료 Flash 계열 최신 모델만을 엄격히 선별·검증하여 비용 위험을 원천 차단하는 아키텍처. [FACT]
* **[메모리 캐싱 및 사용자 통제권 보장]:** 검증된 모델명은 세션 메모리에 캐싱하여 지연을 줄이고, 모델 변경 시 사용자에게 명확한 선택권을 보장. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: 사용자 설정 모델 및 캐시 검증] [★★★★★]
1. 사용자가 직접 입력한 모델명을 최우선으로 적용.
2. `cachedModelName` 메모리 캐시 확인.

##### [2단계: GET /v1beta/models 실시간 검증] [★★★★★]
1. `https://generativelanguage.googleapis.com/v1beta/models?key=${apiKey}` 호출.
2. `generateContent` 지원 및 `flash` 키워드가 포함된 안전 가성비 모델 목록 선별.

##### [3단계: 고비용 방어 티어 격리 및 모델 결정] [★★★★★]
1. 고비용 Pro/상위 모델로의 임의 변경을 엄격히 차단하고 안전한 Flash 모델로만 정제 반환.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[직접 입력 모델의 가용성 소진 및 404 Not Found 발생]**
  * **증상:** 사용자가 입력한 프리뷰 모델의 쿼터 소진 또는 구글의 지원 중단으로 번역 중단.
  * **원인:** 모델 수명 주기 변동 및 단순 자동 Fallback 시 유료 모델 고비용 과금 위험 존재.
  * **해결법:** `getValidGeminiModel`을 통해 사용자 제어권을 유지하면서 Flash 계열 내에서만 안전하게 가용성을 검증하는 고비용 방어형 동적 탐색 구축. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 외부 LLM 연동 시 자동 Fallback은 반드시 동일 가격 티어(무료/초저가 Flash) 내로 엄격히 제한해야 하며, 유료 고성능 모델로의 자동 우회는 비용 폭탄을 유발하므로 절대 금지해야 한다.

---

#### 📋 Feedback History
* **2026-08-19:** 사용자 모델 직접 입력 방식 및 유료 모델 고비용 방어 요구사항 반영 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, Gemini, DynamicModelFetch, 404Error, HighCostPrevention, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
