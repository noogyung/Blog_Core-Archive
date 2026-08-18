---
topic: webtranslator-11-gemini-model-404-and-dynamic-fetch
title_kr: 자동 모델 탐색이 부른 과금 참사와 모델 헬퍼 도구 전환
category: Troubleshooting
sub_category: API-Resilience
version: 1.1.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-19
language: KR+EN
tags: [WebTranslator, ChromeExtension, Gemini, GPT, API, 404Error, CostOptimization, ModelHelper, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-11-gemini-model-404-and-dynamic-model-fetch.html
blog_draft_date: 2026-08-19
blog_id: core-archive
blog_published: false
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
* **Title_KR:** 자동 모델 탐색이 부른 과금 참사와 모델 헬퍼 도구 전환
* **Category:** Troubleshooting
* **Sub-Category:** API-Resilience
* **Version:** 1.1.0
* **Status:** Verified
* **Date:** 2026-08-19
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 모델 설정 및 옵션 헬퍼 소스 코드 (`src/options/model_helper.js`, `src/background/engines/gemini.js`, `src/background/engines/openai.js`)
* [★★★★★] Google Generative AI & OpenAI API Model Pricing/Quota Reference
* [★★★★★] WebTranslator 실전 개발 및 과금 디버깅 로그 (`55e0e726-ace7-4d89-80a2-5d9abb7eb7ad`, 커밋 `6abe2db`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[구형 하드코딩 모델의 한계]:** 초기 `gemini-2.5-flash` 모델은 구형으로 무료 허용량이 매우 적었고, 서비스 종료로 404 Not Found가 발생함. [FACT]
* **[AI의 자동 모델 선택이 초래한 과금 참사]:** 404 해결을 위해 AI가 제안한 '실시간 가용 모델 자동 탐색 및 자동 적용' 모듈이 모델별 단가와 쿼터 정책을 무시하고 고비용 상위 모델을 임의 선택함. Gemini는 쿼터가 순식간에 고갈되어 다시 404가 터지고, OpenAI는 선결제 $10 크레딧이 고비용 `gpt-4o`로 인해 순식간에 소진되는 참사 발생. (초저가 `gpt-5.4-nano` 누락) [FACT]
* **[자동 선택 폐기 및 사용자 직접 모델 고정]:** 번역 런타임의 자동 모델 선택기를 전면 폐기하고, 사용자가 초가성비 모델(`gemini-flash-lite-latest`, `gpt-5.4-nano`)을 직접 지정하여 고정 사용하도록 개편. 10편의 배치 큐/지수 백오프와 결합하여 Gemini 무료 쿼터 완주 및 GPT 10만 자당 $0.01 수준의 경제적 운영 달성. [FACT]
* **[실시간 가용 모델 조회의 헬퍼 도구 전환]:** AI가 작성했던 모델 메타데이터 조회 API를 번역 실행 루프에서 떼어내어, 옵션 설정창에서 "현재 사용 가능한 모델 목록을 검색하고 추천해 주는 보조 도구(Model Helper)"로 변형 재활용. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: 번역 런타임 자동 모델 선택 로직 전면 제거] [★★★★★]
1. `callEngineAPI` 내부의 무조건적 자동 Fallback 및 동적 모델 교체 코드 삭제.
2. 사용자가 설정창에서 입력한 모델명을 최우선 고정 파라미터로 주입.

##### [2단계: 초가성비 모델 고정 운용] [★★★★★]
1. Gemini: `gemini-flash-lite-latest` 설정으로 15 RPM 무료 쿼터 내 100% 완주.
2. OpenAI: `gpt-5.4-nano` 설정으로 10만 자당 $0.01 초저비용 번역.

##### [3단계: 실시간 가용 모델 조회 API를 옵션 헬퍼 도구로 분리] [★★★★★]
1. `GET /v1beta/models` 조회를 옵션 페이지의 `fetchAvailableModels()` 버튼/자동완성 UI로 이동.
2. 사용자가 직접 모델명을 복사/선택할 수 있도록 안내 헬퍼 역할만 수행.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[AI의 실시간 자동 모델 선택으로 인한 무료 쿼터 고갈 및 OpenAI $10 선결제금 증발]**
  * **증상:** 번역 실행 시 비싼 상위 모델이 임의 할당되어 Gemini 무료 쿼터가 수 초 만에 마비되고 OpenAI 선결제 크레딧 $10가 증발함.
  * **원인:** 번역 런타임에 모델별 가격을 모르는 AI 자동 선택기를 넣어 고비용 모델을 무차별 호출함.
  * **해결법:** 자동 모델 선택기를 전면 폐기하고 초가성비 모델(`gemini-flash-lite-latest`, `gpt-5.4-nano`)을 직접 고정 입력하도록 수정하였으며, 가용 모델 API는 옵션 페이지의 단순 조회 헬퍼 도구로 분리 격리. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] LLM API 연동 시 번역 실행 루프에서 모델을 임의로 자동 변경(Auto-Escalation)하게 두면 과금 폭탄을 맞을 수 있으므로, 모델 선택은 반드시 사용자가 직접 통제해야 한다.
* [FACT] API 공급자의 모델 목록 엔드포인트는 런타임 자동 선택용이 아닌, 사용자 UI의 모델 추천/안내 헬퍼 도구로 활용하는 것이 가장 안전하다.

---

#### 📋 Feedback History
* **2026-08-19:** 11편 작업 순서 및 실제 과금 참사/모델 헬퍼 전환 사실관계 전면 반영 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, Gemini, GPT, API, 404Error, CostOptimization, ModelHelper, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
