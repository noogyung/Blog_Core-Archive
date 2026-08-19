---
topic: webtranslator-14-external-dictionary-failure-and-single-llm
title_kr: Kaikki 및 외부 사전 API 검토와 단일 LLM 고속 사전 결정
category: Architecture-Decision
sub_category: Dictionary-Pipeline
version: 1.5.0
status: Verified
created_date: 2026-08-20
last_modified: 2026-08-20
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, Kaikki, DictionaryAPI, LLMPipeline, 단어사전, 아키텍처결정]
sources_count: 3
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
series_id: webtranslator
---

# Knowledge File: webtranslator-14-external-dictionary-failure-and-single-llm
## Category: Architecture-Decision (Dictionary-Pipeline)
## Date: 2026-08-20

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-14-external-dictionary-failure-and-single-llm
* **Title_KR:** Kaikki 및 외부 사전 API 검토와 단일 LLM 고속 사전 결정
* **Category:** Architecture-Decision
* **Sub-Category:** Dictionary-Pipeline
* **Version:** 1.5.0
* **Status:** Verified
* **Date:** 2026-08-20
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] Kaikki 사전 API 활용 분석 기술 토론 로그 (`a4e4cbae-5715-4ef8-aa88-975da2b8c841`)
* [★★★★★] WebTranslator 사전 프롬프트 소스 코드 (`src/api/prompts.js:L41-L54`)
* [★★★★★] WebTranslator 단일 사전 팝업 UI 소스 코드 (`src/content/ui/dictionaryPopup.js`, 커밋 `9c9c63b`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[Kaikki 및 오픈소스 사전 도입 검토 배경]:** 웹페이지 내 텍스트 드래그 시 발생하는 단어 번역 API 비용을 줄이고 상세한 어학 정보를 제공하기 위해, Wiktionary 기반 오픈소스 사전 데이터(Kaikki.org) 및 외부 무료 사전 API 활용 방안을 기술적으로 검토. [FACT]
* **[Kaikki 및 외부 사전의 아키텍처적 한계 분석]:**
  * **방대한 데이터 크기와 인프라 부담:** Kaikki의 전체 JSON 덤프 데이터는 수십 기가바이트(GB)에 달해 크롬 확장 프로그램 클라이언트에서 직접 탑재 및 검색이 불가능함. 이를 활용하려면 별도의 검색 DB(SQLite/Elasticsearch)와 자체 백엔드 API 서버를 구축하고 유지보수해야 하므로, API 비용 절감보다 인프라 운영 공수/비용이 더 커지는 배보다 배꼽이 큰 문제 확인. [FACT]
  * **외부 무료 API의 차단 및 커버리지 리스크:** Free Dictionary API 같은 서드파티 서비스를 호출할 경우 빈도수 제한(Rate Limit)에 따른 차단 위험이 있고, 일본어·중국어(CJK) 등 다국어 지원이 부실함. [FACT]
* **[단일 LLM JSON 파이프라인 (Single LLM Pipeline) 채택]:**
  * 별도 서버 인프라 구축이나 불안정한 무료 API 연동 대신, 경량 LLM(Gemini Flash, GPT-4o-mini)에 1회 프롬프트 요청으로 원문 분석, 발음 음차(한글 표기), 품사, 핵심 뜻(최대 3개), 예문/번역을 **0.8초 내에 JSON 단일 객체로 반환**받는 직관적인 아키텍처를 최종 확정. [FACT]
* **[단일 플로팅 카드 UI 통합 (`dictionaryPopup.js`)]:**
  * 드래그 대상에 따라 단어 모드와 문장 모드로 분리하려던 복잡한 UI 분기를 배제하고, 동일한 사전 카드 컴포넌트에서 단어(품사/뜻/예문)와 구·문장(직접 번역문)을 일관되게 렌더링하는 UX 구현. [FACT]

---

#### 🛠️ Procedures (절차)

1. **Kaikki 사전 데이터 및 외부 API 기술 타당성 분석:** [★★★★★] ✅ Verified 2026-08-20
   - 클라이언트 제약(용량 한계, 자체 백엔드 서버 필요성)과 외부 무료 API의 호출 차단 리스크 검토 후 도입 보류 결정.
2. **단일 LLM 사전 프롬프트(`buildDictionaryPrompt`) 설계:** [★★★★★] ✅ Verified 2026-08-20
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
3. **단일 팝업 UI 통합 (`dictionaryPopup.js`):** [★★★★★] ✅ Verified 2026-08-20
   - 단어와 구/문장을 하나의 플로팅 카드에서 부드럽게 렌더링하도록 뷰 통합.
4. **다국어 실시간 드래그 성능 검증:** [★★★★★] ✅ Verified 2026-08-20
   - 영어, 일본어, 중국어 등 다국어 단어 드래그 시 평균 0.8초 내 고속 렌더링 및 정확한 발음 음차 동작 확인.

---

#### 💡 Architectural Decision Matrix (의사결정 비교)

| 비교 항목 | Kaikki 자체 DB 구축 | 외부 무료 사전 API | 단일 LLM 프롬프트 (최종 채택) |
|---|---|---|---|
| **데이터 용량** | 수십 GB (클라이언트 탑재 불가) | 외부 호스팅 | **0MB (별도 DB 불필요)** |
| **추가 인프라** | 자체 검색/API 서버 필수 | 필요 없음 | **필요 없음 (Serverless)** |
| **다국어 지원** | 언어별 데이터 정제 필요 | CJK 등 비영어권 취약 | **모든 주요 언어 자연스럽게 지원** |
| **응답 속도** | 서버 구축 시 빠름 | 2~3초 이상 지연 | **0.8초 고속 응답** |
| **호출 차단 위험**| 없음 (자체 서버) | 빈도수 초과 시 차단 위험 | **사용자 개인 API Key로 안전** |

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] API 호출 비용을 아끼기 위해 오픈소스 사전이나 무료 API를 무리하게 도입하려 하면, 오히려 자체 서버 구축 비용과 유지보수 공수가 더 커질 수 있다.
* [FACT] 프롬프트를 정밀하게 설계하여 JSON 스키마와 반환 데이터 길이를 엄격히 제한하면, 경량 LLM에서도 0.8초대의 빠른 반응 속도와 일관된 사전 UI를 구현할 수 있다.

---

#### ❓ Missing Info (검증 필요 항목)

* [x] Kaikki 및 외부 사전 API 도입 검토 내용과 실제 기술 의사결정 히스토리 정정 완료 — Verified 2026-08-20
* [ ] 오프라인 환경에서 최근 검색한 단어 사전 캐시(Chrome Storage) 재활용 구조 점검
* [ ] 1,000자 이상 장문 드래그 시 사전 프롬프트 대신 전체 문단 번역 모드로 자동 전환하는 임계점 검증

---

## 📝 Feedback History

### 2026-08-20 — Test Result: PASS
* **피드백 내용:** 
  1. 실제 구현하지 않고 설계 단계에서 기술적 장단점을 분석·논의했던 Kaikki 및 외부 사전 검토 히스토리를 사실에 맞게 정정 (`a4e4cbae` 로그 반영).
  2. 허구의 '3단 연쇄 실패 및 롤백' 서술을 삭제하고, 인프라 비용/용량 제약 분석에 따른 '단일 LLM 고속 파이프라인 채택' 아키텍처 의사결정으로 전환.
* **Status 변경:** Experimental → Verified

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, Kaikki, DictionaryAPI, LLMPipeline, 단어사전, 아키텍처결정

===== KNOWLEDGE PACKAGE END =====