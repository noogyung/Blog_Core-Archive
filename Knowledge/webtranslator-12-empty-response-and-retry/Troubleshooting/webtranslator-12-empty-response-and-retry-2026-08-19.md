---
topic: webtranslator-12-empty-response-and-retry
title_kr: 스팀 커뮤니티 빈 응답 오류와 큐 기반 재시도 및 실패 배치 장애 격리
category: Troubleshooting
sub_category: API-Resilience
version: 1.3.0
status: Verified
created_date: 2026-08-19
last_modified: 2026-08-19
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, Steam, EmptyResponse, RetryQueue, 장애격리, 스킵처리, 트러블슈팅]
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
* **Title_KR:** 스팀 커뮤니티 빈 응답 오류와 큐 기반 재시도 및 실패 배치 장애 격리
* **Category:** Troubleshooting
* **Sub-Category:** API-Resilience
* **Version:** 1.3.0
* **Status:** Verified
* **Date:** 2026-08-19
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 배치 워커 및 큐 재시도 소스 코드 (`src/content/translation.js:L565-L695`)
* [★★★★★] WebTranslator Gemini 엔진 내부 지수 재시도 소스 코드 (`src/api/engines/gemini.js:L66-L87`)
* [★★★★★] WebTranslator 실전 개발 및 스팀 창작마당 디버깅 로그 (`5ce5ba4b-41e3-43ec-9a16-2bbff9a44a1b`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[빈 번역 응답(Empty Response) 및 통신 오류의 실체]:** 스팀 창작마당이나 게임 포럼처럼 수백 개의 댓글과 문단이 얽힌 웹페이지를 번역할 때, 네트워크 일시적 순단(`message port closed`, `Background script returned undefined`), LLM 안전 필터(Safety Filter) 검열, 또는 토큰 절단으로 인해 빈 문자열(`""`)이나 누락된 응답이 반환되는 결함. [FACT]
* **[무한 루프 재호출의 치명적 부작용]:** 에러 발생 시 대기 시간 없이 즉시 무한 재호출을 돌리면 API 서버 429 밴을 유발하거나 브라우저 탭이 영구 블로킹되어 확장 프로그램이 마비됨. [FACT]
* **[큐 기반 차등 지연 재시도 (Differential Delay Retry Queue)]:**
  * 일시적 통신 오류 및 빈 응답(`비어 있습니다`): **3초 (`3000ms`) 쿨타임 대기** 후 재시도.
  * API 레이트 리밋 / 과부하(`429/503/RESOURCE_EXHAUSTED`): **10초 (`10000ms`) 쿨타임 대기** 후 재시도.
  * `availableAt` 타임스탬프를 부여하여 쿨타임이 지난 배치만 워커가 순차적으로 소비하도록 제어. [FACT]
* **[최대 3회 재시도 제한 (`item.retryCount < 3`)]:** 동일 배치에 대해 최대 3회까지만 재시도를 허용하여 무한 대기 락(Lock)을 원천 차단. [FACT]
* **[실패 배치 스킵(Skip) 기반 장애 격리 (Fault Isolation)]:** 3회 재시도를 초과하거나 치명적 오류 발생 시, 전체 번역 프로세스를 중단시키지 않고 콘솔에 경고 로그(`배치 X 오류 — 스킵하고 계속 진행`)를 남긴 뒤 **해당 배치만 건너뛰고(Skip) 프로그레스 바 완료 카운트를 올려 나머지 전체 페이지 번역을 완주**하는 비파괴 장애 격리 아키텍처. [FACT]

---

#### 🛠️ Procedures (절차)

1. **배치 큐 데이터 구조 설계 (`batchesQueue`):** [★★★★★] ✅ Verified 2026-08-19
   - 각 배치 아이템에 `id`, `batch`, `availableAt`, `retryCount` 속성을 부여하여 큐에 등록.
2. **응답 유효성 및 오류 유형 감지:** [★★★★★] ✅ Verified 2026-08-19
   - `if (!result?.translations) throw new Error("번역 응답이 비어 있습니다.");` 로 빈 응답 감출.
   - `isRateLimit`(429/503)과 `isCommunicationError`(통신오류/빈응답)를 분기 식별.
3. **차등 쿨타임 큐 재진입 (최대 3회):** [★★★★★] ✅ Verified 2026-08-19
   - `item.retryCount < 3` 조건 만족 시 `item.retryCount++` 증가 후 `availableAt = Date.now() + (isRateLimit ? 10000 : 3000)` 설정하여 큐 후방에 재투입.
4. **한도 초과 시 안전 스킵 및 진행률 보정:** [★★★★★] ✅ Verified 2026-08-19
   - 재시도 3회 초과 시 에러를 던져 중단하지 않고, `completed += batch.length`로 완료 카운트를 반영하여 UI 멈춤 없이 나머지 배치 번역 지속.

```javascript
// src/content/translation.js (실제 구현된 큐 워커 및 에러 처리 로직)
try {
  var result = await sendToBackground({ action: "translate", texts, ... });
  if (result?.error) throw new Error(result.error);
  if (!result?.translations) throw new Error("번역 응답이 비어 있습니다.");

  // 정상 번역 DOM 렌더링 및 캐시 저장
  ...
  completed += batch.length;
  if (onProgress) onProgress(completed);
  pendingBatches--;

} catch (err) {
  var errMsg = err.message || "";
  var isRateLimit = errMsg.includes("429") || errMsg.includes("503") || errMsg.includes("RESOURCE_EXHAUSTED");
  var isCommunicationError = errMsg.includes("message port closed") || errMsg.includes("Background script returned undefined") || errMsg.includes("비어 있습니다");

  if ((isRateLimit || isCommunicationError) && item.retryCount < 3) {
    item.retryCount++;
    item.availableAt = Date.now() + (isRateLimit ? 10000 : 3000); // 429는 10초, 빈 응답/통신은 3초 대기
    batchesQueue.push(item);
    console.warn(`[WebTranslator] 배치 ${item.id} 일시적 오류("${errMsg}"). 재시도 큐에 추가됨 (재시도 ${item.retryCount}/3 회차)`);
  } else {
    // 3회 재시도 초과 또는 복구 불가능한 에러 발생 시 안전하게 스킵하고 진행
    console.warn(`[WebTranslator] 배치 ${item.id} 오류 — 스킵하고 계속 진행 (재시도 초과 또는 치명적 오류)`, err);
    completed += batch.length;
    if (onProgress) onProgress(completed);
    pendingBatches--;
  }
}
```

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[스팀 창작마당 대용량 번역 시 '번역 응답이 비어 있습니다' 배치 오류 및 중단 현상]**
  * **증상:** 수백 개 문단 번역 중 `[WebTranslator] 배치 0 오류 — 스킵하고 계속 진행` 경고와 함께 특정 문단이 비거나 프로그레스 바가 멈추는 현상 발생.
  * **원인:** 일시적 네트워크 순단이나 LLM의 빈 응답 발생 시, 기존에는 재시도 없이 즉시 실패 처리되거나 무한 재시도로 브라우저가 정체됨.
  * **해결법:** 3초/10초 차등 쿨타임 큐를 통해 최대 3회 자동 재시도하고, 3회 초과 시 해당 배치만 격리 스킵(Skip)하여 웹페이지 전체 번역 완주율 100% 보장. [FACT]
  * **신뢰도:** [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 모든 에러를 하나의 대기 시간으로 처리하면 안 되며, 429 쿼터 초과는 10초 쿨타임, 빈 응답/순단은 3초 쿨타임으로 분리해야 사용자 체감 지연을 최소화할 수 있다.
* [FACT] 외부 API나 LLM을 다루는 클라이언트에서는 완벽한 100% 성공을 기대하기보다, 3회 재시도 실패 시 해당 구간을 안전하게 스킵(Skip) 처리하여 전체 UX 흐름을 끊지 않는 '우아한 장애 격리(Graceful Degradation)'가 훨씬 중요하다.

---

#### ❓ Missing Info (검증 필요 항목)

* [x] 실제 WebTranslator 소스 코드(`src/content/translation.js`, `src/api/engines/gemini.js`)와 100% 일치하도록 가상 Fallback 제거 및 큐 재시도/스킵 로직 검증 완료 — Verified 2026-08-19

---

## 📝 Feedback History

### 2026-08-19 — Test Result: PASS
* **피드백 내용:** 가상의 Google Translate Fallback 내용을 전면 삭제하고, 실제 WebTranslator 소스 코드에 탑재된 '3초/10초 차등 쿨타임 재시도 큐(최대 3회) 및 실패 배치 스킵(장애 격리)' 로직으로 사실 관계 정정.
* **수정 내역:**
  1. Title 및 Core Concepts: 허구의 Fallback 제거 ➔ 큐 기반 재시도 및 실패 배치 스킵(장애 격리) 아키텍처로 전면 수정.
  2. Procedures: `src/content/translation.js`의 실제 워커 코드 및 3초/10초 쿨타임 로직 1:1 반영.
  3. Tags: `Fallback`, `GoogleTranslate` ➔ `RetryQueue`, `장애격리`, `스킵처리`로 갱신.
* **Status 변경:** Verified 유지

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, Steam, EmptyResponse, RetryQueue, 장애격리, 스킵처리, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====