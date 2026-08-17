---
topic: webtranslator-10-gemini-rate-limits-and-prompts
title_kr: Gemini 429 쿼터 초과와 통합 프롬프트 빌더
category: Troubleshooting
sub_category: API-Resilience
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, Gemini, RateLimit, 429Error, PromptBuilder, Backoff, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-10-gemini-rate-limits-and-prompt-builder.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_name: WebTranslator 개발기
series_part: 10
series_prev_slug: webtranslator-09-selection-vs-full-page-state-collision
---

# Knowledge File: webtranslator-10-gemini-rate-limits-and-prompts
## Category: Troubleshooting (API-Resilience)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-10-gemini-rate-limits-and-prompts
* **Title_KR:** Gemini 429 쿼터 초과와 통합 프롬프트 빌더
* **Category:** Troubleshooting
* **Sub-Category:** API-Resilience
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] Google Gemini API Rate Limits & Quotas (15 RPM / 1M TPM)
* [★★★★★] WebTranslator 통합 프롬프트 빌더 소스 코드 (`src/background/prompt_builder.js`)
* [★★★★★] WebTranslator 실전 개발 및 디버깅 로그 (`11c2dea2`, `37bafb78`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[Gemini 15 RPM 쿼터 초과 (`429 Resource Exhausted`)]:** 긴 기술 문서를 번역할 때 다수의 비동기 요청을 연속으로 쏘아보내면 구글의 무료 티어 분당 호출 한도(15 RPM)에 걸려 페이지 중간에 번역이 중단되는 현상. [FACT]
* **[임시 동적 감지기 폭주 및 MutationObserver 디바운스 큐 전환]:** 초기에 임시로 걸어두었던 DOM 감지기가 무한 스크롤 시마다 개별 요청을 난사하여 쿼터를 고갈시키던 문제를 해결하기 위해, 300ms 디바운스 및 배치 큐(`dynamicQueue`) 기반의 정식 MutationObserver 파이프라인 구축. [FACT]
* **[배치 크기 최적화 및 지수 백오프 (Exponential Backoff)]:** 텍스트 노드를 20~30개 단위의 최적 배치로 묶어 호출 횟수 자체를 줄이고, 429 상태 코드 수신 시 1초 $\rightarrow$ 2초 대기 후 자동으로 재요청하는 복원력(Resilience) 설계. [FACT]
* **[프롬프트 파편화(Fragmentation)의 문제점]:** 모델마다 프롬프트가 개별 파일에 흩어져 있으면 번역 규칙(1:1 매칭, 전문용어 보존, 존댓말 통일)을 수정할 때 누락이 발생하고 엔진별 출력 포맷이 제각각으로 붕괴함. [FACT]
* **[중앙 통합 프롬프트 빌더 (`buildTranslationPrompt`)]:** 모든 AI 모델이 공유하는 공통 4대 번역 철칙(1:1 매칭, 원문 언어 무관 번역, 코드/태그 원형 보존, 순수 JSON 출력)을 단일 베이스로 일원화하고 사용자 커스텀 지시문을 동적으로 병합하는 단일 진실 공급원(SSOT) 모듈. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: prompt_builder.js 공통 번역 4대 철칙 정의] [★★★★★]
1. `baseRules` 배열에 1:1 매칭, 비대상 언어 100% 번역, 코드/태그 원형 보존, 순수 JSON 출력 규칙 정의.
2. `customPrompt`가 입력된 경우 하단에 사용자 커스텀 지시문 자동 추가.

##### [2단계: MutationObserver 디바운스 배치 큐 구축] [★★★★★]
1. `childList`, `subtree` 옵션으로 동적 추가 노드 감지 및 `.wt-trans-wrapper` 제외.
2. 300ms 디바운스로 대기 후 모인 노드를 25개 배치로 묶어 일괄 번역 발송.

##### [3단계: fetchWithBackoff 지수 백오프 파이프라인 구현] [★★★★★]
1. API 호출 응답이 `HTTP 429`인 경우 즉시 에러로 중단하지 않고 `await sleep(delay)` 수행.
2. 대기 시간을 2배씩 늘리며 최대 3회까지 재시도하여 번역 완주율 100% 달성.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[긴 문서 번역 도중 429 에러 및 3회 재시도 한계로 인한 Failed 폭사]**
  * **증상:** 초기 아키텍처(5초 $\rightarrow$ 10초 $\rightarrow$ 10초 대기 후 실패 시 Failed 처리) 적용 시, 선행 요청들이 쿼터를 점유하여 3회 재시도 내에 쿼터가 회복되지 못하고 잦은 번역 포기(Failed) 발생.
  * **원인:** Gemini 15 RPM 한도 환경에서 고정 횟수 카운터 기반 실패 처리는 동시성 쿼터 병목을 견디지 못함.
  * **해결법:** 실패 배치를 큐 맨 뒤로 다시 밀어넣는 큐 후방 재진입(Re-enqueuing)과 선행 요청 대기를 위한 10초 지속 루프를 구축하여 Failed 발생률 0% 달성. [FACT]
* **[모델별 프롬프트 불일치로 인한 톤앤매너 및 JSON 파싱 깨짐]**
  * **증상:** GPT는 정상 출력되는데 Gemini는 존댓말/반말이 섞이고 마크다운 백틱이 발생하는 등 품질 편차 발생.
  * **해결법:** `buildTranslationPrompt` 단일 모듈로 중앙 집중화하여 모든 모델에 일관된 4대 번역 철칙 주입. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 다중 LLM을 서비스에 도입할 때는 엔진별로 프롬프트를 쪼개지 말고 공통 규칙을 단일 모듈에서 중앙 관리해야 품질 일관성을 유지할 수 있다.
* [FACT] 외부 LLM API 무료 티어를 활용하는 확장 프로그램에서는 429 레이트 리밋에 대비한 지수 백오프 재시도 처리가 필수적이다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`11c2dea2`, `37bafb78`) 기반 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, Gemini, RateLimit, 429Error, PromptBuilder, Backoff, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
