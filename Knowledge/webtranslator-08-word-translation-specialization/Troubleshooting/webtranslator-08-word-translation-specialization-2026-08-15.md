---
topic: webtranslator-08-word-translation-specialization
title_kr: 단어 번역 획일화 문제와 LLM 사전 파이프라인 특화
category: Troubleshooting
sub_category: LLM-Pipeline
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, WordDictionary, LLM, Gemini, PromptEngineering, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-08-word-translation-homogenization-and-specialization.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_name: WebTranslator 개발기
series_part: 8
series_prev_slug: webtranslator-07-multi-engine-pipeline-and-json-cleaning
---

# Knowledge File: webtranslator-08-word-translation-specialization
## Category: Troubleshooting (LLM-Pipeline)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-08-word-translation-specialization
* **Title_KR:** 단어 번역 획일화 문제와 LLM 사전 파이프라인 특화
* **Category:** Troubleshooting
* **Sub-Category:** LLM-Pipeline
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 단어 사전 라우터 소스 코드 (`src/background/dictionary.js`)
* [★★★★★] IPA (International Phonetic Alphabet) & Lexical Data Schema Standards
* [★★★★★] WebTranslator 실전 개발 및 디버깅 로그 (`5ff6d87d`, `7aedead8`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[단어 번역 획일화 결함의 원인]:** 단어를 마우스로 드래그했을 때, AI가 내부 라우터에서 일반 문장 번역용 함수(`translateSingleText`)를 그대로 호출하여 고성능 LLM(Gemini, GPT)을 선택했음에도 NMT 기계 번역과 똑같이 단순 1줄 뜻만 반환하던 문제. [FACT]
* **[NMT 기계 번역의 본질적 한계]:** Google Translate와 LibreTranslate는 문장 대 문장 번역 엔진이므로 단어의 발음기호, 품사, 다의어 빈도순 분기, 예문 등 사전적 메타데이터를 자체 생성할 수 없음. [FACT]
* **[LLM 전용 구조화 사전 빌더 (`fetchLLMDictionary`)]:** LLM 엔진 전용 프롬프트(`buildDictionaryPrompt`)를 통해 IPA 발음기호(`pronunciation`), 품사(`pos`), 핵심 뜻 3개(`definitions`), 예문 및 한국어 해석(`examples`)이 포함된 구조화된 JSON 객체를 반환하도록 특화한 파이프라인. [FACT]
* **[지능형 듀얼 라우팅 (`lookupWord`)]:** 엔진의 성격에 따라 LLM 기반 고품질 사전 모드와 NMT 기반 경량 번역 Fallback 모드로 명확히 분기하여 리소스와 사용자 경험을 극대화. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: lookupWord 진입 시 엔진 특성 식별] [★★★★★]
1. `settings.engine`을 조회하여 LLM 엔진(`gemini`, `openai`, `claude`, `ollama`)과 NMT 엔진(`google`, `libre`)을 구분.

##### [2단계: LLM 구조화 사전 요청 (`buildDictionaryPrompt`)] [★★★★★]
1. LLM 선택 시 단어 전용 사전 프롬프트 구성:
   `Output JSON: { word, pronunciation, pos, definitions: string[], examples: { en, ko }[] }`
2. `parseAndCleanJson` 정제 파이프라인을 통과시켜 순수 사전 객체 획득.

##### [3단계: NMT 엔진 Fallback 처리] [★★★★★]
1. NMT 선택 시 기본 텍스트 번역 결과만 `definitions: [simpleTrans]` 형태로 래핑하여 초고속 경량 팝업 반환.

##### [4단계: 클라이언트 팝업 카드 렌더링] [★★★★★]
1. 팝업 UI에 `[ɡeɪm] | 명사 | 1. 경기 2. 게임` 및 예문 카드를 시각적으로 깔끔하게 렌더링.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[LLM 엔진 선택 시에도 단순 1줄 번역만 나오는 버그]**
  * **증상:** Gemini나 GPT-5.6을 선택하고 단어를 드래그했으나 무료 구글 번역과 똑같이 단순 텍스트만 출력됨.
  * **원인:** AI가 `lookupWord` 내부에서 일반 문장 번역 함수를 호출하여 LLM의 사전 생성 능력을 활용하지 못함.
  * **해결법:** `fetchLLMDictionary` 전용 파이프라인을 분리하고 구조화된 JSON 프롬프트 주입 및 파싱을 구현. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 단어 번역과 문장 번역은 사용자의 정보 요구 수준이 완전히 다르므로, LLM을 활용할 때는 일반 번역 파이프라인과 사전 생성 파이프라인을 엄격히 분리해야 한다.
* [FACT] NMT 엔진은 사전적 메타데이터 생성이 불가능하므로, 억지로 가공하려 하지 말고 경량 Fallback으로 설계하는 것이 안정적이다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`5ff6d87d`, `7aedead8`) 기반 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, WordDictionary, LLM, Gemini, PromptEngineering, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
