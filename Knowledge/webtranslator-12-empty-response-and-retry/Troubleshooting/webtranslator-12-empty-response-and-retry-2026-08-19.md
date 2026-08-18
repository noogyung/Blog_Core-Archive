---
topic: webtranslator-12-empty-response-and-retry
title_kr: 스팀 커뮤니티 빈 응답 오류와 지수 백오프 재시도 및 구글 Fallback
category: Troubleshooting
sub_category: API-Resilience
version: 1.3.0
status: Experimental
created_date: 2026-08-19
last_modified: 2026-08-19
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, Steam, 지수백오프, ExponentialBackoff, Retry, Fallback, GoogleTranslate, 트러블슈팅]
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
* **Status:** Experimental
* **Date:** 2026-08-19
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 백그라운드 재시도 헬퍼 소스 코드 (`src/background/retry_helper.js`, 커밋 `89a31d2`)
* [★★★★★] WebTranslator 실전 개발 및 스팀 창작마당 디버깅 로그 (`5ce5ba4b-41e3-43ec-9a16-2bbff9a44a1b`)
* [★★★★★] Chrome Extension MV3 네트워크 복원력 및 지수 백오프 설계 표준

---

#### 🔑 Core Concepts (핵심 개념)

* **[빈 번역 응답(Empty Response) 발생 원인]:** 스팀 창작마당(Steam Workshop)이나 대형 게임 패치 노트처럼 수백 개의 댓글과 문단이 얽힌 웹페이지를 번역할 때, 일시적 네트워크 순단, LLM 프롬프트 세이프티 필터(Safety Filter) 검열, 토큰 절단 또는 JSON 파싱 누락으로 인해 빈 문자열(`""`)이나 누락된 항목이 반환되어 해당 배치가 통째로 증발하는 현상. [FACT]
* **[무한 루프 재호출의 위험성]:** 대기 시간(`delay`)이나 최대 재시도 횟수(`maxRetries`) 없이 단순 `while(true)`로 재호출할 경우 API 429 쿼터 초과 및 브라우저 탭 블로킹을 유발함. [FACT]
* **[지수 백오프(Exponential Backoff) 기반 안전 재시도]:** 시도 횟수(`attempt`)에 따라 `1000ms * (2 ** attempt)` (1초 ➔ 2초 ➔ 4초) 점진적 지연을 부여하여 서버 부하를 완화하고, 최대 3회(`maxRetries = 3`)로 제한하여 루프를 방지. [FACT]
* **[Google Translate 무중단 Fallback 우회 전환]:** 주 엔진(Gemini / GPT 등)이 3회 재시도 후에도 응답에 실패할 경우, 번역이 중단되지 않도록 무료 구글 번역기(`translateWithGoogle`)로 자동 전환하여 본문 누락을 100% 방지하는 2중 안전망 구조. [FACT]

---

#### 🛠️ Procedures (절차)

1. **`executeWithRetryAndFallback` 래퍼 함수 구조 구축:** [★★★★★]
   - 매개변수로 번역 대상 함수(`translateFn`), 원문 배열(`texts`), 목표 언어(`targetLang`), 최대 시도 횟수(`maxRetries = 3`) 전달.
2. **응답 유효성 엄격 검증:** [★★★★★]
   - `Array.isArray(result) && result.length === texts.length && result.every(t => t && t.trim().length > 0)` 조건을 통해 빈 문자열 및 항목 누락 철저 검출.
3. **지수 백오프 대기 구현:** [★★★★★]
   - 검증 실패 시 `1000 * Math.pow(2, attempt)` 밀리초 대기 후 다음 시도 진행.
4. **최종 장애 시 Google Fallback 엔진 긴급 호출:** [★★★★★]
   - 3회 연속 실패 시 콘솔 경고 로깅 후 `translateWithGoogle(texts, targetLang)`을 호출하여 번역 완성.

```javascript
// src/background/retry_helper.js
export async function executeWithRetryAndFallback(translateFn, texts, targetLang, maxRetries = 3) {
  let lastError = null;

  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const result = await translateFn(texts, targetLang);
      if (Array.isArray(result) && result.length === texts.length && result.every(t => t && t.trim().length > 0)) {
        return result; // 정상 번역 수신 완료
      }
      throw new Error("빈 번역 응답 수신");
    } catch (err) {
      lastError = err;
      if (attempt < maxRetries - 1) {
        const delay = 1000 * Math.pow(2, attempt); // 1초, 2초, 4초 대기
        await new Promise(r => setTimeout(r, delay));
      }
    }
  }

  // 3회 모두 실패 시 무료 구글 번역으로 안전 우회
  console.warn(`[WebTranslator] 주 엔진 실패(${lastError.message}) -> 구글 엔진으로 긴급 전환`);
  return await translateWithGoogle(texts, targetLang);
}
```

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[스팀 커뮤니티 대용량 번역 중 '번역 응답이 비어 있습니다' 배치 0 오류]**
  * 증상: 스팀 창작마당 번역 시 `[WebTranslator] 배치 0 오류 — 스킵하고 계속 진행` 메시지와 함께 일부 문단이 영어 원문 그대로 방치됨.
  * 원인: API 서버의 일시적 순단 또는 LLM 안전 필터 검열로 인해 `""` 반환 및 JSON 파싱 실패.
  * 해결법: `executeWithRetryAndFallback`을 적용하여 지수 백오프 3회 재시도 및 실패 시 구글 번역으로 즉시 Fallback 처리. [FACT]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] AI 에이전트에게 재시도 로직 작성을 맡기면 종종 `while(true)` 기반의 무제한 재호출 코드를 작성해 API 서버 429 밴을 유발하므로, `maxRetries`와 지수 지연을 프롬프트 레벨에서 명확히 제약해야 함.
* [OPINION] LLM 번역 중 일부 문장이 누락되더라도 사용자 관점에서는 에러 팝업이 뜨거나 영문으로 방치되는 것보다, 구글 번역으로라도 해당 문단이 매끄럽게 번역되어 채워지는 것이 훨씬 뛰어난 UX를 제공함.

---

#### ❓ Missing Info (검증 필요 항목)

* [ ] 스팀 창작마당 외에 특수 포맷(HTML 엔티티, 이모지, 복합 마크다운)이 다량 포함된 웹사이트에서 빈 응답이 추가로 발생하는 예외 케이스 점검
* [ ] 구글 Fallback 엔진 전환 시 CJK(중/일/한) 및 유럽어 번역 품질과 응답 딜레이 차이 체감 여부 확인

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, Steam, 지수백오프, ExponentialBackoff, Retry, Fallback, GoogleTranslate, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====