---
topic: webtranslator-19-smart-drag-filtering-and-cjk-support
title_kr: 한국어 복사 시 팝업 오작동: 10% 임계값과 CJK 유니코드 보정
category: Troubleshooting
sub_category: Drag-Filter-Unicode
version: 1.0.0
status: Verified
created_date: 2026-08-20
last_modified: 2026-08-21
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, SelectionTranslation, 드래그번역, 단어사전, 유니코드, Unicode, CJK, Regex, 정규식, 트러블슈팅]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
series_id: webtranslator
---

# Knowledge File: webtranslator-19-smart-drag-filtering-and-cjk-support
## Category: Troubleshooting (Drag-Filter-Unicode)
## Date: 2026-08-20

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-19-smart-drag-filtering-and-cjk-support
* **Title_KR:** 한국어 복사 시 팝업 오작동: 10% 임계값과 CJK 유니코드 보정
* **Category:** Troubleshooting
* **Sub-Category:** Drag-Filter-Unicode
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-20
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 19편 개발 및 트러블슈팅 기획 문서 (`docs/blog/19_smart_drag_filtering_and_cjk_support.md`)
* [★★★★★] WebTranslator 텍스트 판정 및 필터링 유틸리티 모듈 (`src/content/utils.js`)
* [★★★★★] WebTranslator 단어 사전 팝업 및 드래그 이벤트 핸들러 (`src/content/dictionary.js`)
* [★★★★☆] Unicode Standard Annex #44 (Unicode Character Database: General_Category Property `\p{L}`)
* [★★★★☆] W3C/ECMA-262 ECMAScript Unicode Property Escapes 정규식 명세

---

#### 🔑 Core Concepts (핵심 개념)

* **[모국어(한국어) 텍스트 복사 시 단어 사전 팝업 간섭 문제]:**
  * 크롬 확장 프로그램 실전 테스트 중, 한국어 웹페이지에서 일반 텍스트를 복사하거나 드래그할 때 불필요하게 단어 사전 팝업이 활성화되어 사용자 마우스 커서를 가로막는 UX 간섭이 발생함. [FACT]
  * 모국어(사용자 설정 목표 언어) 문장을 드래그할 때는 사전 조회를 스킵하고, 외국어 단어 또는 괄호 병기 형태(`Apple (사과)`)일 때만 사전을 호출하도록 정교한 필터링 기준이 필요함. [FACT]
* **[단순 문자 포함 필터의 한계와 10% 임계값(Threshold) 판정 도입]:**
  * 초기 구현에서 단순히 한국어 문자가 1글자라도 포함되면 조회를 차단(`text.includes("한글")` 또는 단순 정규식)하는 방식을 적용했으나, `Apple (사과)`처럼 보조 설명이 붙은 외국어 단어까지 사전 조회가 차단되는 부작용이 발생함. [FACT]
  * 전체 문자열 중 목표 언어(한국어 등) 문자 수가 10% 이상일 때만 이미 번역이 불필요한 모국어 문장으로 판정하고, 10% 미만일 때는 외래어/외국어 단어로 간주하여 사전 조회를 허용하는 비율 기반 임계값(`threshold = 0.10`) 알고리즘을 도입함. [FACT]
* **[CJK 및 다국어 유니코드 속성(`\p{L}`) 정규식 보정]:**
  * 기존 알파벳 중심 정규식(`[a-zA-Z]`)은 일본어(히라가나/가타카나/한자)나 중국어 단어를 유효한 단어로 인식하지 못하고 스킵하는 결함이 존재함. [FACT]
  * ECMAScript 최신 정규식의 유니코드 속성 이스케이프(`\p{L}`, `\p{N}`)와 CJK 유니코드 블록(`[\u4E00-\u9FFF\u3040-\u309F\u30A0-\u30FF\uAC00-\uD7A3]`)을 적용하여, 한 글자 의미 문자인 CJK 단어는 1글자도 허용하고 영문 단어는 2글자 이상으로 분기 처리함. [FACT]
* **[드래그 유효 단어 정제 및 무의미한 호출 차단]:**
  * 순수 숫자/통화 기호/날짜 표기, API Key/해시 토큰(`sk-...`, `AIzaSy...`), 300자 이상의 긴 문단, 공백만 있는 문자열 등을 사전 조회 단계 이전에 선제 차단하여 API 쿼터 낭비와 팝업 깜빡임을 방지함. [FACT]

---

#### 🛠️ Procedures (절차)

1. **`isAlreadyTargetLang` 함수에 유니코드 정규화(NFC) 및 목표 언어 문자 비율 10% 판정 로직 구현:** [★★★★★] ✅ Verified 2026-08-21
   - NFD 분해형 유니코드를 `normalize("NFC")`로 정규화하고, 전체 유니코드 문자(`\p{L}`) 대비 목표 언어 문자 비율을 계산하여 10%(`0.10`) 이상일 경우 사전 팝업을 스킵.
   ```javascript
   // src/content/utils.js
   export function isAlreadyTargetLang(text, targetLang, isSelection = false) {
     if (!text || !targetLang) return false;
     var clean = text.trim();
     if (!clean) return false;

     clean = clean.normalize("NFC");
     var allLetters = clean.match(/\p{L}/gu);
     if (!allLetters || allLetters.length === 0) return false;

     var targetCount = 0;
     if (targetLang === "ko") {
       var matches = clean.match(/[\uAC00-\uD7AF\u1100-\u11FF\u3130-\u318F\uA960-\uA97F\uD7B0-\uD7FF]/g);
       targetCount = matches ? matches.length : 0;
     }
     // ... 타깃 언어별 매칭 ...

     var threshold = 0.10; // 목표 언어 문자 비율 10% 이상 판정
     return (targetCount / allLetters.length) >= threshold;
   }
   ```

2. **`isValidDictWord` 함수에 CJK 다국어 1글자 예외 처리 및 유니코드 속성(`\p{L}`) 검증 로직 적용:** [★★★★★] ✅ Verified 2026-08-21
   - 단순 ASCII 정규식을 유니코드 속성(`\p{L}`)으로 대체하고, CJK 한자/가나 1글자 단어를 유효 단어로 인정.
   ```javascript
   // src/content/dictionary.js
   export function isValidDictWord(text) {
     if (!text || typeof text !== "string") return false;
     var clean = text.trim();
     if (clean.length < 1 || clean.length > 35) return false;

     // 1. 숫자/통화/날짜 패턴 스킵
     if (/^[$\u20A0-\u20BA]?\d+(?:[.,]\d+)*[%s]?$/i.test(clean)) return false;
     if (/^\d{1,4}[-./]\d{1,2}[-./]\d{1,4}$/.test(clean)) return false;

     // 2. API Key / Token 패턴 스킵
     if (/^(sk-|AIza|ghp_|eyJ|bearer\s)/i.test(clean)) return false;

     // 3. 유효 언어 문자(\p{L}) 최소 개수 검증 (CJK 1글자 허용)
     var lettersOnly = clean.replace(/[^\p{L}]/gu, "");
     if (lettersOnly.length === 0) return false;

     var isCJK = /[\u4E00-\u9FFF\u3040-\u309F\u30A0-\u30FF\uAC00-\uD7A3]/u.test(clean);
     if (!isCJK && lettersOnly.length < 2) return false;

     return true;
   }
   ```

3. **마우스 드래그(`mouseup`) 이벤트 핸들러에 지능형 유효성 검사 및 150ms 디바운스 연동:** [★★★★★] ✅ Verified 2026-08-21
   - 마우스 드래그 완료 후 `cleanWord` 추출 → `isValidDictWord` 통과 여부 검사 → `isAlreadyTargetLang` 판정 후 유효한 외래어 단어일 때만 팝업 생성.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[한국어 웹페이지 텍스트 드래그 시 단어 사전 팝업 무분별 노출로 인한 UX 저해]**
  * 증상: 한국어 뉴스/블로그 기사를 복사하기 위해 드래그했을 때 불필요하게 단어 사전 팝업이 커서 위치에 노출됨.
  * 원인: 드래그된 문자열의 언어가 사용자의 목표 언어(`targetLang`)와 동일한지 판별하는 필터링 부재.
  * 해결법: `isAlreadyTargetLang` 함수를 도입하여 드래그 텍스트 내 목표 언어 문자 비율이 10% 이상인 경우 사전 팝업 트리거를 즉시 차단함. [FACT]
  * 신뢰도: [★★★★★]

* **[괄호 병기 단어(`Apple (사과)`)의 사전 팝업 차단 및 외래어 오판]**
  * 증상: 영어 단어 옆에 한국어 뜻이 함께 적힌 텍스트를 드래그하면 한글이 포함되어 있다는 이유로 사전 팝업이 열리지 않음.
  * 원인: 단 1글자라도 한글이 포함되어 있으면 무조건 한국어 문장으로 판정하던 이진(Binary) 검사 로직.
  * 해결법: 전체 문자 수 대비 한글 글자 수 비율을 계산하여 10% 미만인 경우 외래어 단어로 판정하고 사전을 정상 허용함. [FACT]
  * 신뢰도: [★★★★★]

* **[일본어/중국어 단어 드래그 시 사전 미동작 및 무효 단어 판정 오류]**
  * 증상: 일본어(`日本語`, `リンゴ`)나 중국어 단어를 드래그하면 단어가 아니라고 판단되어 사전 조회가 무시됨.
  * 원인: 영문 알파벳(`[a-zA-Z]`) 중심의 정규식과 최소 2글자 길이 제한으로 인해 CJK 유니코드 및 1글자 한자 표기를 인식하지 못함.
  * 해결법: `\p{L}` 유니코드 속성 정규식을 적용하고, CJK 문자셋 범위(`\u4E00-\u9FFF`, `\u3040-\u309F` 등)에 대해서는 1글자도 유효 단어로 인정하도록 분기 처리함. [FACT]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 웹 브라우저 확장 프로그램의 드래그 기반 팝업 UI는 사용자의 일반적인 텍스트 복사/선택 행동을 방해하지 않도록 언어 감지 및 임계값(Threshold) 기반의 엄격한 유효성 필터가 선행되어야 한다.
* [FACT] 다국어 지원 도구에서는 `[a-zA-Z]` 같은 ASCII 기반 정규식을 지양하고, ECMAScript 정규식의 유니코드 속성 이스케이프(`\p{L}`) 및 명시적 CJK 유니코드 범위를 적용해야 다국어 단어 누락을 방지할 수 있다.

---

#### ❓ Missing Info (검증 필요 항목)

* [x] 드래그 텍스트 내 한글 비율 10% 임계값이 `Apple (사과)` 외에 `Python(파이썬)` 등 다양한 괄호 병기 단어에서 사전을 정상 허용함을 확인 — Verified 2026-08-21
* [x] 일본어 1글자 한자(예: `水`, `火`) 및 가타카나 단어 드래그 시 사전 팝업이 누락 없이 정상 호출됨을 확인 — Verified 2026-08-21
* [x] 순수 한국어 문장 드래그 복사 시 팝업 간섭 없이 브라우저 기본 복사 동작이 매끄럽게 수행됨을 확인 — Verified 2026-08-21

---

## 📝 Feedback History

### 2026-08-21 (1차) — Test Result: PASS
* **환경:** Windows 11, Chrome Extension MV3, WebTranslator v1.0.0
* **검증된 단계:** 1단계 ~ 3단계 전체 검증 완료
* **피드백 내용:**
  1. 한국어 웹페이지 텍스트 드래그 복사 시 팝업 간섭 없이 브라우저 기본 복사 동작 정상 수행 확인.
  2. `Apple (사과)`, `Python(파이썬)` 등 괄호 병기 텍스트에서 10% 임계값 판정을 통한 단어 사전 정상 호출 확인.
  3. 일본어 1글자 한자(`水`, `火`) 및 가타카나/중국어 단어 드래그 시 CJK 유니코드 속성 정규식을 통한 사전 팝업 정상 노출 확인.
* **Status 변경:** Experimental → Verified

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, SelectionTranslation, 드래그번역, 단어사전, 유니코드, Unicode, CJK, Regex, 정규식, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
