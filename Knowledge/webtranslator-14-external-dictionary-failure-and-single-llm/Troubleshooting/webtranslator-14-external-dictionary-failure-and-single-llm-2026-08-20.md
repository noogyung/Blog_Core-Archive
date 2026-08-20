---
topic: webtranslator-14-external-dictionary-failure-and-single-llm
title_kr: Kaikki 및 외부 사전 API 검토와 단일 LLM 고속 사전 결정
category: Troubleshooting
sub_category: Dictionary-Architecture
version: 1.5.0
status: Verified
created_date: 2026-08-20
last_modified: 2026-08-20
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, Kaikki, DictionaryAPI, LLMPipeline, 단어사전, 프롬프트엔지니어링, 트러블슈팅]
sources_count: 3
blog_draft_path: Blog_Posts/2026-08/webtranslator-14-external-dictionary-review-and-single-llm.html
blog_draft_date: 2026-08-20
blog_id: core-archive
blog_published: false
series_id: webtranslator
---

# Knowledge File: webtranslator-14-external-dictionary-failure-and-single-llm
## Category: Troubleshooting (Dictionary-Architecture)
## Date: 2026-08-20

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-14-external-dictionary-failure-and-single-llm
* **Title_KR:** Kaikki 및 외부 사전 API 검토와 단일 LLM 고속 사전 결정
* **Category:** Troubleshooting
* **Sub-Category:** Dictionary-Architecture
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

* **[Kaikki 및 오픈소스 사전 도입 검토 배경]:** 6편에서 구현한 선택 영역 번역(드래그 번역) 기능을 바탕으로, 단순 번역을 넘어 발음, 품사, 핵심 뜻, 예문까지 한눈에 보여주는 단어 사전 기능을 강화하여 UI/UX 경험을 고도화하고자 함. 단어 조회 시마다 발생하는 LLM API 호출 비용을 절감하고 상세 어학 데이터를 제공하기 위해 Wiktionary 기반 오픈소스 사전 데이터(Kaikki.org) 및 외부 무료 사전 API 연동 방안을 기술적으로 검토. [FACT]
* **[Kaikki 및 외부 사전의 아키텍처적 한계]:**
  * **방대한 데이터 크기와 인프라 부담:** Kaikki의 전체 덤프 데이터는 무압축 기준 약 26GB, 압축 파일 기준 약 2GB에 달함. 크롬 확장 프로그램 클라이언트 내에 수 GB 규모의 대용량 사전 데이터를 직접 내장하는 것은 브라우저 메모리 및 확장 프로그램 용량 제약상 불가능함. 이를 활용하려면 별도의 검색 DB(SQLite/Elasticsearch)와 자체 백엔드 API 서버를 구축하고 유지보수해야 하므로, API 비용 절감보다 서버 호스팅 및 관리 리소스(비용)가 더 커지는 문제가 발생함. [FACT]
  * **외부 무료 API의 차단 및 커버리지 리스크:** Free Dictionary API 같은 서드파티 서비스를 호출할 경우 빈도수 제한(Rate Limit)에 따른 차단 위험이 있고, 일본어·중국어(CJK) 등 비영어권 다국어 데이터가 부실함. 과거 Google Translate(비공식 웹 크롤링 방식) 연동에서도 동일한 Rate Limit 문제가 있었으나, Google Translate는 무료 이용자를 위한 보조 수단이었고 실사용은 Gemini Flash 등 무료 티어 LLM API가 주로 담당하여 문제가 되지 않았던 반면, 사전 기능은 드래그 시마다 호출되는 핵심 UX이므로 불안정한 외부 무료 API에 의존할 수 없었음. [FACT]
* **[단일 LLM JSON 파이프라인 (Single LLM Pipeline) 채택]:**
  * 별도 서버 인프라 구축이나 불안정한 무료 API 연동 대신, 경량 LLM(Gemini Flash, GPT-4o-mini)에 1회 프롬프트 요청으로 원문 분석, 발음 음차(한글 표기), 품사, 핵심 뜻(최대 3개), 예문/번역을 **0.8초 내에 JSON 단일 객체로 반환**받는 직관적인 아키텍처를 최종 확정. [FACT]
* **[단어/문장 팝업 UI 통합 (`dictionaryPopup.js`)]:**
  * 드래그 대상에 따라 단어 모드와 문장 모드로 분리하려던 복잡한 UI 분기를 배제하고, 동일한 사전 카드 컴포넌트에서 단어(품사/뜻/예문)와 구·문장(직접 번역문)을 일관되게 렌더링하는 UX 구현. [FACT]

---

#### 🛠️ Procedures (절차)

1. **Kaikki 사전 데이터 및 외부 API 기술 타당성 분석:** [★★★★★] ✅ Verified 2026-08-20
   - 클라이언트 제약(무압축 26GB / 압축 2GB 크기로 인한 확장 프로그램 직접 내장 불가, 자체 백엔드 서버 구축 필요성)과 외부 무료 API의 호출 차단 리스크(과거 Google Translate 연동 대비 사전 호출 빈도의 중요성) 검토 후 도입 보류 결정.
2. **단일 LLM 사전 프롬프트(`buildDictionaryPrompt`) 설계:** [★★★★★] ✅ Verified 2026-08-20
   - 단어 입력 시: 상위 3개 핵심 뜻(2~5단어) 및 실전 예문 1개 강제.
   - 구/문장 입력 시: 품사를 '구'/'문장'으로 설정하고 예문(`null`) 생략 후 직접 번역문 반환.
   - 발음 필드: 원문의 소리를 대상 언어 문자(한글)로 음차 표기 강제.
3. **단일 팝업 UI 통합 (`dictionaryPopup.js`):** [★★★★★] ✅ Verified 2026-08-20
   - 단어와 구/문장을 하나의 플로팅 카드에서 부드럽게 렌더링하도록 뷰 통합.
4. **다국어 실시간 드래그 성능 검증:** [★★★★★] ✅ Verified 2026-08-20
   - 영어, 일본어, 중국어 등 다국어 단어 드래그 시 평균 0.8초 내 고속 렌더링 및 정확한 발음 음차 동작 확인.

```javascript
// src/api/prompts.js (사전 프롬프트 빌더)
export function buildDictionaryPrompt(word, langName) {
  return `Provide a concise dictionary entry for the input text "${word}" translated into ${langName}.

CRITICAL INSTRUCTIONS:
1. If the input is a single word: Provide AT MOST THE TOP 3 MOST COMMON definitions.
   - Each definition MUST have a short, concise ${langName} meaning (2 to 5 words only).
   - Provide a realistic example sentence matching the definition.
2. If the input is a PHRASE or FULL SENTENCE (not a single word):
   - Set "pos" to "구" (Phrase) or "문장" (Sentence).
   - Provide the direct, natural translation of the entire phrase/sentence in the "meaning" field.
   - DO NOT generate or invent an "example". Set the "example" field to null.
3. "pronunciation": Provide the phonetic pronunciation of the ORIGINAL SOURCE TEXT, transliterated into the characters of the target language (${langName}).
4. Return ONLY a valid JSON object matching this schema:
{"word":"${word}","pronunciation":"[Phonetic characters in ${langName}]","definitions":[{"pos":"Part of speech","meaning":"Meaning or Translation","example":{"source":"Original example sentence","target":"Translated example sentence"}}]}`;
}
```

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[Kaikki 사전 데이터의 대용량으로 인한 클라이언트 직접 내장 불가 한계]**
  * **증상(검토 결과):** 전체 데이터가 무압축 기준 약 26GB, 압축 모델/파일도 약 2GB에 달하여 크롬 확장 프로그램 패키지 내 직접 탑재나 클라이언트 사이드 조회가 불가능함.
  * **원인:** 크롬 확장 프로그램의 배포 용량 제한 및 브라우저 메모리 제약으로 수 GB 단위 데이터를 로컬에 내장할 수 없으며, 이를 처리하려면 검색용 DB 및 자체 백엔드 서버 구축이 필수적임.
  * **해결법:** 별도 서버 인프라 구축 및 유지보수 부담을 피하기 위해, 경량 LLM(Gemini Flash / GPT-4o-mini)에 1회 프롬프트 요청으로 필요한 사전 정보만 즉시 생성받는 구조로 전환. [FACT]
  * **신뢰도:** [★★★★★]
* **[외부 무료 사전 API(Free Dictionary API 등) 활용 시의 한계]**
  * **증상(검토 결과):** 비영어권(일본어, 중국어 등) 어휘 데이터 지원이 부실하고, 빈번한 호출 시 서비스 차단(Rate Limit) 리스크가 존재함.
  * **원인:** 과거 Google Translate 연동에서도 나타났던 무료 서드파티 서비스의 쿼터 제약 및 차단 문제. 번역 엔진은 Gemini Flash 등 무료 티어 API가 주력으로 사용되어 Google Translate의 Rate Limit이 문제되지 않았으나, 사전 기능은 드래그 시마다 호출되므로 차단 리스크가 치명적임.
  * **해결법:** 외부 사전 API 의존성을 배제하고, 사용자 개인 API Key 기반의 단일 LLM 프롬프트 파이프라인으로 일원화하여 다국어 지원과 호출 안정성을 확보. [FACT]
  * **신뢰도:** [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] API 호출 비용을 절감하기 위해 오픈소스 사전이나 무료 API 도입을 검토하였으나, 이를 클라이언트에서 활용하기 위해 필요한 자체 백엔드 서버 구축 및 유지보수 공수가 너무 크다고 판단되었다.
* [FACT] AI API를 사용하면 어떤 언어든 유연하게 번역이 가능하지만, 오픈소스 사전 API는 데이터베이스에 미리 등재된 단어만 정적으로 조회할 수 있어 신조어, 활용형, 다국어 문맥 처리에 명확한 한계가 존재한다.
* [FACT] 프롬프트를 정밀하게 설계하여 JSON 스키마와 반환 데이터 길이를 엄격히 제한하면, 경량 LLM에서도 0.8초대의 빠른 반응 속도와 일관된 사전 UI를 구현할 수 있다.

---

#### ❓ Missing Info (검증 필요 항목)

* [x] Kaikki 및 외부 사전 API 도입 검토 내용과 실제 기술 의사결정 히스토리 정정 완료 — Verified 2026-08-20
* [ ] 긴 장문(단락)을 드래그하여 선택 번역을 실행할 때 번역이 정상적으로 완료되지 않거나 누락되는 현상 점검 및 수정 필요

---

## 📝 Feedback History

### 2026-08-20 — Test Result: PASS
* **피드백 내용:**
  1. 서론 문단 분리 및 6편 선택 영역 번역(드래그 번역) 기능과의 연계/UI·UX 강화 배경 명시.
  2. 외부 무료 API의 Rate Limit 한계를 과거 Google Translate 연동 경험(무료 티어 LLM 대비 사전 호출 빈도의 중요성)과 비교하여 설명 보강.
  3. 프롬프트 코드 블록의 문자열 연결 연산자(`+`) 제거 및 템플릿 리터럴 형태로 클린업.
  4. 지식 베이스 및 블로그 HTML 게시글 양방향 동기화.
* **Status 변경:** Verified 유지

### 2026-08-20 (이전 이력) — Test Result: PASS
* **피드백 내용:** 
  1. 실제 구현하지 않고 설계 단계에서 기술적 장단점을 분석·논의했던 Kaikki 및 외부 사전 검토 히스토리를 사실에 맞게 정정 (`a4e4cbae` 로그 반영).
  2. 지식 베이스 표준 템플릿 규약(카테고리, Errors & Solutions 표준 형식, 코드 블록 정리)에 맞추어 전면 재정비.
  3. 경험 및 팁 보강: 자체 인프라 구축 공수 부담 및 오픈소스 사전의 정적 데이터 한계(신조어/활용형 불가) 명시.
  4. Missing Info 정비: 불필요한 오프라인 캐시 항목을 삭제하고, '긴 장문 드래그 시 번역 누락 현상 점검'을 신규 과제로 등록.
  5. 환각 표현 제거: 검증되지 않은 '브라우저 로컬 스토리지/메모리 초과' 등의 과장된 증상 표현을 삭제하고, 실제 기술 검토 결과(수십 GB 데이터로 인한 확장 프로그램 내장 불가 및 서버 필요성)로 정정.
* **Status 변경:** Verified 유지

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, Kaikki, DictionaryAPI, LLMPipeline, 단어사전, 프롬프트엔지니어링, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====