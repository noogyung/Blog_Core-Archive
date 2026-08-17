---
topic: webtranslator-02-race-condition-and-storage
title_kr: 단축키 연타 레이스 컨디션 해결과 스토리지 분리
category: Troubleshooting
sub_category: StateManagement-Storage
version: 1.0.0
status: Verified
created_date: 2026-08-15
last_modified: 2026-08-15
language: KR+EN
tags: [WebTranslator, ChromeExtension, ManifestV3, RaceCondition, AbortController, ChromeStorage, StateMachine]
sources_count: 3
blog_draft_path: Blog_Posts/core-archive/2026-08/webtranslator-02-shortcut-race-condition-and-storage-split.html
blog_draft_date: 2026-08-15
blog_id: core-archive
blog_published: true
series_name: WebTranslator 개발기
series_part: 2
series_prev_slug: webtranslator-01-inception-and-csp-bypass
---

# Knowledge File: webtranslator-02-race-condition-and-storage
## Category: Troubleshooting (StateManagement-Storage)
## Date: 2026-08-15

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-02-race-condition-and-storage
* **Title_KR:** 단축키 연타 레이스 컨디션 해결과 스토리지 분리
* **Category:** Troubleshooting
* **Sub-Category:** StateManagement-Storage
* **Version:** 1.0.0
* **Status:** Verified
* **Date:** 2026-08-15
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] MDN Web Docs: AbortController & AbortSignal API
* [★★★★★] Chrome Extensions Documentation: chrome.storage.sync vs chrome.storage.local Storage Quotas
* [★★★★★] WebTranslator 실전 개발 및 디렉팅 로그 (`77cc2ad9`, `9705f7a4`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[비동기 레이스 컨디션 (Race Condition)]:** 번역 API 요청 후 응답이 도착하기 전에 사용자가 단축키를 다시 눌러 원본으로 복원했을 때, 뒤늦게 수신된 번역 결과가 DOM을 덮어씌워 렌더링이 깨지는 현상. [FACT]
* **[AbortController 기반 물리 취소]:** 단순한 boolean 플래그 변경이 아닌, `AbortController.abort()`를 호출하여 진행 중인 브라우저 네트워크 `fetch` 요청 자체를 즉각 중단시키는 메커니즘. [FACT]
* **[DOM 기반 단일 상태 관리 (SSOT)]:** `document.body.dataset.wtStatus`에 `idle`, `translating`, `translated`의 명시적 상태를 부여하여 다중 단축키 입력 이벤트를 엄격하게 제어하는 상태 머신. [FACT]
* **[스토리지 용도별 분리 (Sync vs Local)]:**
  * `chrome.storage.sync`: 사용자 설정(API 키, 기본 번역 언어, 테마 등), 기기간 자동 동기화 지원, 100KB 전체 용량 제한. [FACT]
  * `chrome.storage.local`: 대용량 번역 캐시, 사용자 정의 사전 데이터 등, 로컬 전용, 10MB(또는 무제한) 용량 지원. [FACT]

---

#### 🛠️ Procedures (절차)

##### [1단계: AbortController 비동기 취소 파이프라인 구성] [★★★★★]
1. 모듈 전역에 `currentAbortController` 인스턴스 포인터 선언.
2. 번역 시작 시 `currentAbortController = new AbortController()` 생성 후 API 요청 시 `signal` 전달.
3. 번역 진행 중 취소 요청이 인입되면 `currentAbortController.abort()` 즉시 호출.
4. `try...catch` 블록에서 `err.name === 'AbortError'` 발생 시 조용히 return 처리하여 예외 억제.

##### [2단계: DOM 데이터셋 기반 3단계 상태 머신 구현] [★★★★★]
1. `document.body.dataset.wtStatus` 확인:
   * `translating` 상태에서 재입력: `abort()` 호출 $\rightarrow$ `revertTranslation()` $\rightarrow$ `idle` 전이.
   * `translated` 상태에서 재입력: `revertTranslation()` $\rightarrow$ `idle` 전이.
   * `idle` 상태에서 입력: `translating` 설정 $\rightarrow$ 번역 완료 후 `translated` 전이.

##### [3단계: 스토리지 레이어 분리 및 인터페이스화] [★★★★★]
1. 설정값 관리: `chrome.storage.sync.get(['apiKey', 'targetLang'])`로 처리하여 기기 동기화 보장.
2. 번역 캐시 및 사전: `chrome.storage.local.get(['translationCache', 'customDict'])`로 격리하여 쿼터 초과 원천 방지.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[단축키 연타 시 DOM 붕괴 현상]**
  * **증상:** `Alt+A`를 빠르게 2번 이상 누르면 번역문과 원문이 중첩 삽입되거나 텍스트 노드가 소실됨.
  * **원인:** 단순 플래그(`isTranslating`)만으로는 이미 발송된 비동기 Fetch 응답의 수신 및 콜백 실행을 막지 못함.
  * **해결법:** `AbortController`를 도입하여 재입력 즉시 진행 중인 요청을 물리 취소하고 상태 머신으로 통제. [FACT]
* **[chrome.storage.sync 쿼터 초과 에러 (MAX_ITEMS / QUOTA_BYTES)]**
  * **증상:** 웹 서핑을 지속하며 번역 캐시가 누적될 때 `QuotaExceededError` 발생.
  * **원인:** 번역 캐시 데이터를 100KB 한도의 `sync` 스토리지에 무차별 저장함.
  * **해결법:** 설정값은 `sync`, 대용량 캐시는 `local`로 스토리지를 엄격히 분리. [FACT]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] 프론트엔드 비동기 통신에서 단순 플래그 토글 방식은 레이스 컨디션을 유발하므로 네트워크 수준의 `AbortSignal` 연동이 필수적이다.
* [FACT] Chrome 확장 프로그램의 `sync` 스토리지는 기기 간 동기화라는 장점이 있지만 항목당 8KB, 전체 100KB로 매우 작으므로 캐시 저장소로 절대 사용해서는 안 된다.

---

#### 📋 Feedback History
* **2026-08-15:** WebTranslator v1.0.0 개발 로그(`77cc2ad9`, `9705f7a4`) 기반 검증 완료 (Status: Verified).

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, ManifestV3, RaceCondition, AbortController, ChromeStorage, StateMachine, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
