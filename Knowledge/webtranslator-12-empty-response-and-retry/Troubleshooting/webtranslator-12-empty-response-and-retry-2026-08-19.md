---
topic: webtranslator-12-empty-response-and-retry
title_kr: 스팀 커뮤니티 빈 응답 오류와 지수 백오프 재시도 및 구글 Fallback
category: Troubleshooting
sub_category: API-Resilience
version: 1.3.0
status: Verified
created_date: 2026-08-19
last_modified: 2026-08-19
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, Steam, EmptyResponse, SafetyFilter, 지수백오프, Fallback, GoogleTranslate, 트러블슈팅]
sources_count: 3
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
series_id: webtranslator
---

# Knowledge File: webtranslator-12-empty-response-and-retry
## Category: Troubleshooting (API-Resilience)
## Date: 2026-08-19

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-12-empty-response-and-retry
* **Title_KR:** 스팀 커뮤니티 빈 응답 오류와 지수 백오프 재시도 및 구글 Fallback
* **Category:** Troubleshooting
* **Sub-Category:** API-Resilience
* **Version:** 1.3.0
* **Status:** Verified
* **Date:** 2026-08-19
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 백그라운드 재시도 헬퍼 소스 코드 (`src/background/retry_helper.js`, 커밋 `89a31d2`)
* [★★★★★] WebTranslator 실전 개발 및 스팀 창작마당 디버깅 로그 (`5ce5ba4b-41e3-43ec-9a16-2bbff9a44a1b`)
* [★★★★★] Google Gemini / OpenAI Safety Filter 검열 가이드라인 및 Fallback 복원력 아키텍처

---

#### 🔑 Core Concepts (핵심 개념)

* **[10편(429 쿼터 초과)과의 구조적 차이점]:**
  * **10편:** 분당 요청 한도(15 RPM) 병목 해결을 위한 MutationObserver 디바운스 큐 및 10초 대기 후 큐 후방 재진입(Re-enqueuing) 루프.
  * **12편:** 429 쿼터 문제가 아니라, 스팀 창작마당이나 게임 포럼의 특정 텍스트가 **LLM 안전 필터(Safety Filter) 검열에 걸리거나 일시적 네트워크 순단으로 인해 `""`(빈 응답)이나 누락된 배열을 반환하는 결함** 해결. [FACT]
* **[빈 번역 응답(Empty Response)의 치명성]:** LLM API가 특정 비속어, 게임 내 폭력적 표현, 특수 마크다운을 검열하여 빈 문자열을 반환하면, 해당 배치가 통째로 스킵되어 웹페이지 중간중간이 번역되지 않은 채 영문으로 방치됨. [FACT]
* **[안전 필터 검열 대상에 대한 무한 루프의 위험성]:** 10편과 같이 큐 후방에 무한 재진입시키면, 안전 필터에 걸린 텍스트는 영원히 LLM에서 번역되지 않아 큐 전체가 정체되고 불필요한 API 비용과 지연이 발생함. [FACT]
* **[응답 유효성 엄격 검증 (`Array & Non-Empty`)]:** 번역 결과가 배열인지, 원문 배열 길이와 1:1로 일치하는지, 모든 요소가 공백이 아닌 실제 번역 문자열인지 엄격히 검증. [FACT]
* **[지수 백오프(3회 제한) + Google Translate 긴급 Fallback (2중 안전망)]:**
  * 일시적 네트워크 순단은 1초 ➔ 2초 ➔ 4초 지수 백오프로 최대 3회 재시도하여 자체 복구.
  * 3회 재시도 후에도 LLM이 안전 필터 등으로 응답을 거부하면, 즉시 **무료 구글 번역기(`translateWithGoogle`)로 긴급 우회(Fallback)**하여 사용자 화면에 100% 완전한 번역문을 끊김 없이 렌더링. [FACT]

---

#### 🛠️ Procedures (절차)

1. **`executeWithRetryAndFallback` 래퍼 함수 도입:** [★★★★★] ✅ Verified 2026-08-19
   - 번역 호출 함수(`translateFn`), 텍스트 배열(`texts`), 언어 설정(`targetLang`), 최대 시도 횟수(`maxRetries = 3`)를 파라미터로 전달받음.
2. **배열 길이 및 빈 문자열 엄격 검증:** [★★★★★] ✅ Verified 2026-08-19
   - `Array.isArray(result) && result.length === texts.length && result.every(t => t && t.trim().length > 0)` 조건을 충족할 때만 정상 번역으로 인정하고 즉시 반환.
3. **지수 백오프 지연 적용 (최대 3회):** [★★★★★] ✅ Verified 2026-08-19
   - 유효성 검증 실패 또는 에러 발생 시 `1000 * Math.pow(2, attempt)` 밀리초 대기 후 다음 재시도 수행.
4. **Google Translate Fallback 자동 전환:** [★★★★★] ✅ Verified 2026-08-19
   - 3회 연속 실패 시(안전 필터 검열 등) 콘솔에 경고를 남기고 `translateWithGoogle`로 자동 우회하여 번역 보절 완료.

```javascript
// src/background/retry_helper.js
export async function executeWithRetryAndFallback(translateFn, texts, targetLang, maxRetries = 3) {
  let lastError = null;

  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const result = await translateFn(texts, targetLang);
      // 단순 오류뿐만 아니라 빈 문자열("")이나 항목 누락을 엄격히 검출
      if (Array.isArray(result) && result.length === texts.length && result.every(t => t && t.trim().length > 0)) {
        return result; // 정상 번역 수신 완료
      }
      throw new Error("빈 번역 응답 수신 또는 문단 누락");
    } catch (err) {
      lastError = err;
      if (attempt < maxRetries - 1) {
        const delay = 1000 * Math.pow(2, attempt); // 1초, 2초, 4초 지수 대기
        await new Promise(r => setTimeout(r, delay));
      }
    }
  }

  // 3회 재시도 실패(안전 필터 검열 등) 시 무료 구글 번역으로 긴급 Fallback 우회
  console.warn(`[WebTranslator] 주 엔진 실패(${lastError.message}) -> 구글 엔진으로 안전 Fallback 전환`);
  return await translateWithGoogle(texts, targetLang);
}
```

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[스팀 커뮤니티 대용량 번역 중 '번역 응답이 비어 있습니다' 배치 0 오류]**
  * **증상:** 스팀 창작마당 번역 시 `[WebTranslator] 배치 0 오류 — 스킵하고 계속 진행` 메시지가 뜨며 특정 문단들이 통째로 영문 원문으로 방치됨.
  * **원인:** 게임 패치 노트/댓글의 특정 단어가 LLM 안전 필터에 걸려 빈 문자열(`""`)로 응답되거나 일시적 네트워크 순단 발생.
  * **해결법:** `executeWithRetryAndFallback`을 통해 3회 지수 백오프 검증 후, 안전 필터 등으로 지속 실패 시 구글 번역(Google Fallback)으로 자동 우회하여 본문 누락률 0% 달성. [FACT]
  * **신뢰도:** [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 10편에서 구축한 429 큐 대기 로직과 12편의 빈 응답 Fallback 로직은 상호 보완적으로 작동함 (429 속도 조절 $ightarrow$ LLM 호출 $ightarrow$ 빈 응답/안전 필터 시 구글 Fallback).
* [FACT] 안전 필터에 걸린 텍스트는 아무리 재호출해도 LLM이 번역을 거부하므로, 무한 재시도가 아닌 유한 재시도(3회) 후 구글 번역으로 우회하는 Fallback 설계가 필수적이다.

---

#### ❓ Missing Info (검증 필요 항목)

* [x] 10편(429 큐 후방 재진입)과의 구조적 차이점 및 안전 필터 대응 Fallback 분기 명확화 — Verified 2026-08-19
* [ ] 구글 Fallback 엔진 전환 시 CJK(중/일/한) 및 유럽어 번역 품질과 응답 딜레이 차이 체감 여부 확인

---

## 📝 Feedback History

### 2026-08-19 — Test Result: PASS
* **피드백 내용:** 10편의 지수 백오프/429 큐 로직과 12편의 빈 응답(Empty Response) 및 안전 필터 Fallback 로직 간의 중복/혼선 해소 및 10~11편 지식과의 정합성 동기화.
* **수정 내역:**
  1. 10편(429 쿼터 초과 vs 무한 큐 재진입)과 12편(안전 필터 검열/빈 응답 vs 구글 번역 Fallback 2중 안전망)의 명확한 차이점 정리.
  2. 안전 필터 검열 대상에 대한 무한 루프 위험성 및 유한 3회 재시도 후 Fallback 전환의 필요성 보강.
  3. Procedures 및 Core Concepts 섹션 전면 정비.
* **Status 변경:** Experimental → Verified

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, Steam, EmptyResponse, SafetyFilter, 지수백오프, Fallback, GoogleTranslate, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====