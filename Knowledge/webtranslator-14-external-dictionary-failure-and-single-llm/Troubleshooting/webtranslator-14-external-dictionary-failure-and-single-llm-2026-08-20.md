---
topic: webtranslator-14-external-dictionary-failure-and-single-llm
title_kr: 무료 외부 사전 R&D 실패와 단일 LLM 고속 사전 파이프라인 전환
category: Troubleshooting
sub_category: Dictionary-Architecture
version: 1.5.0
status: Experimental
created_date: 2026-08-20
last_modified: 2026-08-20
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, Kaikki, DictionaryAPI, LLMPipeline, 단어사전, 프롬프트엔지니어링, 트러블슈팅]
sources_count: 3
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
series_id: webtranslator
---

# Knowledge File: webtranslator-14-external-dictionary-failure-and-single-llm
## Category: Troubleshooting (Dictionary-Architecture)
## Date: 2026-08-20

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-14-external-dictionary-failure-and-single-llm
* **Title_KR:** 무료 외부 사전 R&D 실패와 단일 LLM 고속 사전 파이프라인 전환
* **Category:** Troubleshooting
* **Sub-Category:** Dictionary-Architecture
* **Version:** 1.5.0
* **Status:** Experimental
* **Date:** 2026-08-20
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 사전 프롬프트 및 UI 소스 코드 (`src/api/prompts.js:L41-L54`, `src/content/ui/dictionaryPopup.js`, 커밋 `9c9c63b`)
* [★★★★★] Kaikki 및 Free Dictionary API 연동 테스트 데이터
* [★★★★★] WebTranslator 실전 개발 및 R&D 검증 로그 (`a4e4cbae`, `328191c9`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[무료 외부 사전 API 연쇄 호출의 한계]:** 단어 드래그 시 API 비용을 절감하기 위해 오픈소스 사전(Kaikki, Free Dictionary API)을 도입했으나, 일본어·중국어(CJK) 및 특수 어휘에서 `404 Not Found`가 빈발하고, `영영사전 조회 ➔ 영문 정의 번역기 재호출 ➔ 예문 파싱` 3단 연쇄로 인해 팝업 표시에 **3~5초 이상 지연**되는 치명적 한계. [FACT]
* **[단일 LLM 고속 파이프라인 전환 (Single LLM Pipeline)]:** 3단 연쇄를 전면 폐기하고, 경량 LLM(Gemini Flash, GPT-4o-mini)에 1회 프롬프트 요청으로 원문 분석, 발음 음차(한글 표기), 품사, 핵심 뜻 최대 3개, 예문/번역을 **0.8초 내에 JSON 단일 객체로 반환**받는 구조로 단순화. [FACT]
* **[단어/문장 분기 UI 통합]:** 드래그 텍스트 길이에 따라 단어 모드와 문장 모드로 분리하려던 복잡한 분기 로직을 취소하고, 단일 플로팅 카드(`.wt-dictionary-popup`)에서 단어(품사/뜻/예문)와 문장/구(직접 번역문)를 일관되게 렌더링하는 직관적 UX 구현. [FACT]
* **[스키마 최적화 프롬프트 (`buildDictionaryPrompt`)]:**
  * 단어일 경우 최대 상위 3개 핵심 의미만 제한(각 뜻 2~5단어 간결성 강제).
  * 구나 문장일 경우 불필요한 예문 생성(`null`)을 생략하여 토큰 낭비 및 지연을 최소화. [FACT]

---

#### 🛠️ Procedures (절차)

1. **외부 사전 API 연동 테스트 및 한계 확인:** [★★★★★]
   - Kaikki/FreeDict API 연쇄 호출 프로토타입 작성 후 CJK 404 에러 및 3~5초 응답 지연 데이터 확인.
2. **외부 사전 연쇄 폐기 및 단일 LLM 프롬프트 설계:** [★★★★★]
   ```javascript
   // src/api/prompts.js
   export function buildDictionaryPrompt(word, langName) {
     return `Provide a concise dictionary entry for the input text "${word}" translated into ${langName}.
` +
       `CRITICAL INSTRUCTIONS:
` +
       `1. If the input is a single word: Provide AT MOST THE TOP 3 MOST COMMON definitions.
` +
       `   - Each definition MUST have a short, concise ${langName} meaning (2 to 5 words only).
` +
       `   - Provide a realistic example sentence matching the definition.
` +
       `2. If the input is a PHRASE or FULL SENTENCE (not a single word):
` +
       `   - Set "pos" to "구" (Phrase) or "문장" (Sentence).
` +
       `   - Provide the direct, natural translation of the entire phrase/sentence in the "meaning" field.
` +
       `   - DO NOT generate or invent an "example". Set the "example" field to null.
` +
       `3. "pronunciation": Provide the phonetic pronunciation of the ORIGINAL SOURCE TEXT, transliterated into the characters of the target language (${langName}).
` +
       `4. Return ONLY a valid JSON object matching this schema:
` +
       `{"word":"${word}","pronunciation":"[Phonetic characters in ${langName}]","definitions":[{"pos":"Part of speech","meaning":"Meaning or Translation","example":{"source":"Original example sentence","target":"Translated example sentence"}}]}`;
   }
   ```
3. **단일 플로팅 카드 UI 통합 (`dictionaryPopup.js`):** [★★★★★]
   - 단어와 문장을 단일 팝업 컴포넌트에서 매끄럽게 처리하도록 뷰 렌더러 통합.
4. **다국어 드래그 실전 검증:** [★★★★★]
   - 영어, 일본어, 중국어 단어 드래그 시 0.8초 내 고속 팝업 렌더링 및 발음 음차 검증 완료.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[외부 무료 사전 API의 CJK 및 다국어 단어 `404 Not Found` 오류]**
  * **원인:** 오픈소스 사전 DB의 언어 커버리지 한계 및 불규칙 활용형(Inflected forms) 처리 실패.
  * **해결법:** 외부 사전을 전면 폐기하고 다국어 문맥 이해도가 뛰어난 경량 LLM 단일 호출 파이프라인으로 전환. [FACT]
  * **신뢰도:** [★★★★★]
* **[외부 사전 3단 연쇄 호출로 인한 3~5초 극심한 UI 지연]**
  * **원인:** 영영 사전 조회 후 정의를 다시 번역하고 예문을 파싱하는 다단계 네트워크 지연 누적.
  * **해결법:** 1회의 정밀 JSON 프롬프트로 정의, 발음, 예문을 일괄 반환받아 0.8초대로 단축. [FACT]
  * **신뢰도:** [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] API 비용을 아끼기 위해 무료 외부 서비스를 복잡하게 연쇄시키는 것보다, 토큰 효율적인 경량 LLM 프롬프트 1회 호출로 해결하는 것이 속도, 다국어 품질, 유지보수 측면에서 압도적으로 유리하다.
* [FACT] 단어 모드와 문장 모드로 UI를 파편화하기보다, 단일 팝업 카드에서 입력 길이에 따라 자연스럽게 뷰 형태를 맞추는 통합 UX가 사용자 피로도를 줄인다.

---

#### ❓ Missing Info (검증 필요 항목)

* [ ] 오프라인 환경에서 최근 검색한 단어 사전 캐시(Chrome Storage) 재활용 구조 점검
* [ ] 1,000자 이상 장문 드래그 시 사전 프롬프트 대신 전체 문단 번역 모드로 자동 전환하는 임계점 검증

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, Kaikki, DictionaryAPI, LLMPipeline, 단어사전, 프롬프트엔지니어링, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====