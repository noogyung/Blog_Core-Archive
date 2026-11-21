---
topic: unitysense-07-meta-sync-asmdef-and-ecosystem
title_kr: "Unity 생태계 통합: .meta 4사분면 동기화, .asmdef 순환 진단, UI Toolkit과 에디터 연동"
category: Configuration
sub_category: Ecosystem
version: 1.0.1
status: Experimental
created_date: 2026-11-22
last_modified: 2026-11-22
language: KR+EN
tags: [Unity, VSCode, UnitySense]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
blogger_post_id: null
series_id: unitysense
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** unitysense-07-meta-sync-asmdef-and-ecosystem
* **Title_KR:** Unity 생태계 통합: .meta 4사분면 동기화, .asmdef 순환 진단, UI Toolkit과 에디터 연동
* **Category:** Configuration
* **Sub-Category:** Ecosystem
* **Version:** 1.0.1
* **Status:** Experimental
* **Date:** 2026-11-22
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Sub-Plan 06 기획서: `d:\Noogs\NextCloud\Projects\UnityBrain\docs\plans\06-meta-sync-and-integration.md`
  * [★★★★★] `.asmdef` 프로바이더 소스: `d:\Noogs\NextCloud\Projects\UnityBrain\packages\extension\src\asmdef\unityAsmdefProvider.ts`
  * [★★★★★] UI Toolkit 프로바이더 소스: `d:\Noogs\NextCloud\Projects\UnityBrain\packages\extension\src\uitoolkit\unityUiToolkitProvider.ts`
  * [★★★★★] Unity Editor 연동 소스: `d:\Noogs\NextCloud\Projects\UnityBrain\packages\extension\src\editor\unityEditorIntegration.ts`
  * [★★★★★] 릴리스 변경 이력(v1.1.0~v1.1.4): `git show 725a863:CHANGELOG.md`

---

#### 🔑 Core Concepts (핵심 개념)
  * **`.meta` / GUID 4사분면 생명주기 동기화 (`MetaFileSyncManager`):** VS Code 탐색기에서 Unity 파일이나 폴더를 생성·이름변경·이동·삭제할 때 `.meta` 파일이 누락되면 Scene/Prefab의 GUID 참조가 끊어진다. `onWillRenameFiles`, `onWillDeleteFiles`, `onDidCreateFiles` 훅을 통해 기존 `guid:`를 유지한 채 `.meta`를 함께 이동·삭제하고, 신규 파일 생성 시 150ms 대기 후 Unity Editor와 경합하지 않는 경우에만 32자리 hex GUID를 포함한 표준 `.meta`(`MonoImporter`, `ShaderImporter`, `DefaultImporter`)를 생성한다. 또한 `unitySense.cleanOrphanMetaFiles`로 고아 `.meta`를 정리한다. [FACT]
  * **`.asmdef` / `.asmref` 참조 InlayHint & O(V+E) 순환 참조 진단 (`unityAsmdefProvider.ts`):** 프로젝트 내 모든 `.asmdef`/`.asmref`와 대응 `.meta` GUID를 스캔해 `GUID:xxxx` 참조 옆에 실제 어셈블리명을 InlayHint로 표시하고 `F12` 정의 이동을 지원한다. 편집 중인 더티 문서 메모리 오버레이를 반영해 O(V+E) DFS로 순환 참조 및 자기 참조를 실시간 진단한다. [FACT]
  * **UI Toolkit (`.uxml`, `.uss`) 지원 (`unityUiToolkitProvider.ts`):** `-unity-*` 전용 속성(`-unity-font-definition`, `-unity-slice-*`, `-unity-text-align` 등)과 Unity 6 Flexbox/레이아웃/트랜지션 USS 속성 및 의사 상태(`:hover`, `:focus`, `:checked` 등) 자동완성, `.uss` `@import` 처리, C# `.Q<T>("...")`와의 정의 이동 연동을 제공한다. [FACT]
  * **Unity Editor 연동 (`unityEditorIntegration.ts`):** `Library/EditorInstance.json`에서 실행 중인 Unity Editor의 `process_id` 생존 여부를 확인하고, UDP 패킷(`127.0.0.1`)으로 Ping/ShowUsage 프로토콜을 송신한다. 또한 `.vscode/launch.json`을 JSONC 안전 병합 방식으로 생성/갱신하고 `Editor.log` 빠른 열기 커맨드를 제공한다. [FACT]

---

#### 🛠️ Procedures (절차)
  1. **`.meta` 4사분면 이동 처리:** Unity 추적 영역(`Assets/`, 로컬 `Packages/`) 내부↔내부, 내부→외부, 외부→내부, 외부↔외부의 4가지 이동 조합(4사분면)에 따라 `.meta` 이동·삭제·생성을 분기 처리한다. [★★★★★]
  2. **`.asmdef` 그래프 탐색 및 심볼릭 링크 보호:** 디렉터리 탐색 시 `visitedRealDirs`로 심볼릭 링크 무한 루프를 차단하고, 어셈블리 의존성 그래프에서 O(V+E) 사이클 탐지를 수행한다. [★★★★★]
  3. **`.vscode/launch.json` JSONC 안전 병합:** 기존 주석과 UTF-8 BOM, 사용자 정의 설정 키를 보존하는 문자열 안전 JSONC 스트리퍼를 적용하고, 파싱에 실패하면 기존 파일을 덮어쓰지 않고 즉시 중단한다. `processId`도 고정하지 않아 재실행 후 디버거 연결 오류를 방지한다. [★★★★★]
  4. **통합 진입점 오케스트레이션 (`extension.ts`):** Unity 워크스페이스(`ProjectSettings/ProjectVersion.txt` 또는 `Assets`)에서만 활성화되며, 비-Unity 워크스페이스에서는 대기 종료해 오버헤드를 남기지 않는다. `deactivate()` 시 `shader-lsp`와 `unity-daemon` 자식 프로세스를 정리한다. [★★★★★]

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **`.meta` 비동기 처리 미포착 예외 및 4사분면 경계 누락 (v1.1.2, v1.1.4)**
    * 원인: Unity `Assets/` 내부 파일을 프로젝트 외부 폴더로 옮기거나 반대로 외부 파일을 `Assets/`로 옮길 때 `.meta`가 고아로 남거나 누락되었고, 비동기 파일 작업의 `.catch()` 누락으로 경고가 발생했다.
    * 해결법: 4사분면 이동·삭제 동기화 로직과 회귀 테스트를 추가하고, 모든 비동기 `.meta` 처리에 `.catch()` 안전 처리를 보강했다. [FACT]
    * 신뢰도: [★★★★★]
  * **`launch.json` UTF-8 BOM 및 기존 키 손실 위험 (v1.1.2, v1.1.4)**
    * 원인: 기존 `.vscode/launch.json`에 UTF-8 BOM이나 JSONC 주석이 포함되어 있으면 일반 `JSON.parse`가 실패하거나 기존 사용자 디버그 구성이 덮어씌워질 위험이 있었다.
    * 해결법: UTF-8 BOM 제거 및 문자열 내부 `//`/`/*`를 건드리지 않는 JSONC 스트리퍼를 적용하고, 파싱 실패 시 파일 쓰기를 중단하도록 방어 로직을 추가했다. [FACT]
    * 신뢰도: [★★★★★]
  * **`.asmdef` 자기 참조 진단 범위 오류 (v1.1.4)**
    * 원인: `.asmdef` 파일이 자기 자신의 이름 또는 GUID를 참조할 때 진단 밑줄 범위가 정확히 해당 참조 문자열을 가리키지 않았다.
    * 해결법: 자기 참조 감지 시 정확한 참조 토큰 범위를 계산해 Diagnostic을 표시하도록 수정했다. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [FACT] VS Code에서 Unity Editor가 실행 중일 때 파일을 생성하면 Unity 자체도 `.meta`를 생성하므로, `MetaFileSyncManager`는 `150ms` 대기 후 `fs.existsSync(metaPath)`를 재확인하는 방식으로 경합을 회피한다.

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 실제 Unity 프로젝트에서 VS Code 탐색기로 스크립트/폴더를 이름 변경·이동·삭제할 때 Unity Editor가 GUID를 유지한 채 정상 리로드했는지 테스트 결과 확인 필요
  * [ ] 실행 중인 Unity Editor와 UDP 이동(`F12` / CodeLens Ping) 및 `Editor.log` 열기, `launch.json` 디버거 연결을 실환경에서 검증한 결과 확인 필요

---

#### 🏷️ Tags
Unity, Meta-Sync, Asmdef, UIToolkit, UnityEditor, VSCode, UnitySense

===== KNOWLEDGE PACKAGE END =====
