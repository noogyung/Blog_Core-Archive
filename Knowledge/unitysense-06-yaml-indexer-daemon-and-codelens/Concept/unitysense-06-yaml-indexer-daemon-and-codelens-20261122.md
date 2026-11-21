---
topic: unitysense-06-yaml-indexer-daemon-and-codelens
title_kr: ".NET 8 무할당 YAML 에셋 인덱서 데몬과 C# CodeLens 사용처 추적"
category: Concept
sub_category: YAML-Daemon
version: 1.0.1
status: Experimental
created_date: 2026-11-22
last_modified: 2026-11-22
language: KR+EN
tags: [Unity, DotNet, UnitySense]
sources_count: 3
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
blogger_post_id: null
series_id: unitysense
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** unitysense-06-yaml-indexer-daemon-and-codelens
* **Title_KR:** .NET 8 무할당 YAML 에셋 인덱서 데몬과 C# CodeLens 사용처 추적
* **Category:** Concept
* **Sub-Category:** YAML-Daemon
* **Version:** 1.0.1
* **Status:** Experimental
* **Date:** 2026-11-22
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Sub-Plan 05 기획서: `d:\Noogs\NextCloud\Projects\UnityBrain\docs\plans\05-yaml-asset-indexer-and-codelens.md`
  * [★★★★★] 마스터 아키텍처 문서(`DaemonRpcMethods`): `d:\Noogs\NextCloud\Projects\UnityBrain\PLAN.md`
  * [★★★★★] 릴리스 변경 이력(v1.1.0~v1.1.4): `git show 725a863:CHANGELOG.md`

---

#### 🔑 Core Concepts (핵심 개념)
  * **독립 백그라운드 데몬 분리 (`UnityBrain.Daemon`):** 수천 개의 `.unity`, `.prefab`, `.asset`, `.controller` YAML 파일을 Node.js 단일 스레드(VS Code Extension Host)에서 파싱하면 에디터 UI가 멈춘다. 이를 `.NET 8` 독립 프로세스로 분리하고 `Content-Length` 프레이밍 기반 JSON-RPC 2.0 over stdio로 통신한다. [FACT]
  * **무할당 스트리밍 YAML 상태 머신 (`UnityYamlStreamParser`):** 단일 씬 파일 크기가 수십 MB에 이를 수 있으므로 전체 DOM 트리를 메모리에 올리는 범용 파서(`YamlDotNet` 등) 대신 `StreamReader` + `ReadOnlySpan<char>` 기반 1-Pass 라인 상태 머신으로 파싱한다. [FACT]
    * `!u!1` (`GameObject`): `m_Name` 수집
    * `!u!4` / `!u!224` (`Transform` / `RectTransform`): 부모-자식 연결 후 O(N) 역추적으로 `Canvas/HUD/PlayerHealthBar` 계층 경로 조립
    * `!u!114` (`MonoBehaviour`): `m_Script` GUID 매칭, 직렬화 스칼라 필드 값, `m_PersistentCalls`의 `UnityEvent` 콜백 메서드(`m_MethodName`) 추출
    * `!u!1001` (`PrefabInstance`): Nested Prefab / Prefab Variant 및 중첩 `propertyPath`의 `m_Modifications` 오버라이드 값 추출
  * **`UBIX` 증분 바이너리 캐시 (`BinaryIndexCache`):** `{projectRoot}/Library/UnityBrainCache/asset-index.bin`에 헤더(`"UBIX"`), 파일 매니페스트(`LastWriteTicks`, `Length`), 인덱싱된 레코드를 직렬화한다. 재시작 시 변경·추가된 파일만 재파싱하고 삭제된 항목만 제거해 웜 스타트 속도를 확보한다. [FACT]
  * **C# 에디터 CodeLens (`UnityAssetUsageCodeLensProvider`):** 데몬에 `usage/findByScriptGuid`, `usage/findByMethod`, `inspector/getSerializedValues` RPC를 질의해 클래스 상단에 에셋 사용처 수, 직렬화 필드 상단에 인스펙터 오버라이드 값, 콜백 메서드 상단에 `UnityEvent` 사용처를 표시한다. 클릭 시 QuickPick에서 해당 `.unity`/`.prefab` 파일의 `fileID` 라인으로 이동하거나 실행 중인 Unity Editor로 Ping을 보낸다. [FACT]

---

#### 🛠️ Procedures (절차)
  1. **`MetaGuidMap` 병렬 구축:** `Assets/`와 로컬/임베디드 `Packages/`의 `*.cs.meta` 및 `*.asset.meta` 첫 5줄에서 32자리 hex `guid:`를 추출해 양방향 사전을 구성한다. [★★★★★]
  2. **1-Pass 스트리밍 인덱싱 및 벤치마크 검증:** 합성 `.prefab`/`.unity` 1,000개 풀 인덱싱 `< 500ms`, 1개 파일 변경 후 증분 웜 스타트 `< 150ms` 기준을 `UnityBrain.Daemon.Tests`에서 자동 검증한다. [★★★★★]
  3. **JSON-RPC 클라이언트 안정화 (`UnityDaemonClient`):** Extension 활성화 시 번들된 `UnityBrain.Daemon.dll`을 `dotnet`으로 실행하고, 비정상 종료 시 5초 쿨다운/서킷 브레이커, 15초 일반 요청 타임아웃(`index/rebuild`는 60초), bounded `stderr` 드레인, 멱등 `dispose()`를 적용한다. [★★★★★]

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **파이프라인 연속 JSON-RPC 프레임 오버리드 (v1.1.2)**
    * 원인: `RpcServer.ReadFrameAsync`가 버퍼 단위로 헤더를 읽을 때, 클라이언트가 연속으로 파이프라이닝한 다음 RPC 프레임의 바이트까지 미리 읽어버려 프레이밍이 어긋났다.
    * 해결법: 헤더 영역을 1바이트 단위로 읽어 `\r\n\r\n` 경계를 정확히 분리한 뒤 `Content-Length`만큼 본문을 읽도록 수정했다. [FACT]
    * 신뢰도: [★★★★★]
  * **잘못된 JSON 입력 시 데몬 프로토콜 무응답 (v1.1.4)**
    * 원인: 손상된 JSON-RPC 요청이 들어올 때 표준 에러 응답을 돌려주지 않아 클라이언트가 타임아웃까지 대기했다.
    * 해결법: 파싱 실패 시 JSON-RPC 표준 `-32700 Parse error` 응답을 즉시 반환하도록 `RpcServer.cs`를 보완했다. [FACT]
    * 신뢰도: [★★★★★]
  * **대규모 프로젝트 전체 재인덱싱 시 요청 타임아웃 (v1.1.4)**
    * 원인: `index/rebuild`에 일반 조회와 동일한 15초 타임아웃이 걸려 대용량 프로젝트에서 조기 실패할 위험이 있었다.
    * 해결법: `index/rebuild` 전용 타임아웃을 60초로 분리하고, C# CodeLens 파서에도 주석/문자열 마스킹을 적용해 오탐을 줄였다. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [FACT] `.NET 8`으로 빌드할 때 `UseNet8Release=true`와 `rollForward: Major` 설정을 적용해, `.NET 8`, `.NET 9`, `.NET 10` 런타임이 설치된 PC 어디서나 동일한 `UnityBrain.Daemon.dll`이 실행되도록 구성했다.

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 실제 운영 중인 Unity 프로젝트(씬/프리팹 수량 및 용량)에서 `UnityBrain.Daemon` 초기 인덱싱 및 웜 스타트 실측 시간 확인 필요
  * [ ] Nested Prefab / Prefab Variant 오버라이드 값과 `UnityEvent` 콜백 CodeLens가 실제 프로젝트에서 정확히 표시되었는지 확인 필요

---

#### 🏷️ Tags
Unity, YAML, DotNet8, JSON-RPC, CodeLens, Streaming-Parser, UnitySense

===== KNOWLEDGE PACKAGE END =====
