---
topic: unitysense-05-csharp-completion-and-roslyn-analyzers
title_kr: "C# Unity 도메인 자동완성과 비침습적 Roslyn 정적 분석기·CodeFix 구축"
category: Concept
sub_category: Roslyn-CSharp
version: 1.0.1
status: Experimental
created_date: 2026-11-22
last_modified: 2026-11-22
language: KR+EN
tags: [Unity, Roslyn, UnitySense]
sources_count: 4
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
blogger_post_id: null
series_id: unitysense
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** unitysense-05-csharp-completion-and-roslyn-analyzers
* **Title_KR:** C# Unity 도메인 자동완성과 비침습적 Roslyn 정적 분석기·CodeFix 구축
* **Category:** Concept
* **Sub-Category:** Roslyn-CSharp
* **Version:** 1.0.1
* **Status:** Experimental
* **Date:** 2026-11-22
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Sub-Plan 03 기획서: `d:\Noogs\NextCloud\Projects\UnityBrain\docs\plans\03-smart-domain-completion.md`
  * [★★★★★] Sub-Plan 04 기획서: `d:\Noogs\NextCloud\Projects\UnityBrain\docs\plans\04-roslyn-analyzers-and-codefixes.md`
  * [★★★★★] 릴리스 변경 이력(v1.1.2~v1.1.6): `git show 725a863:CHANGELOG.md`
  * [★★★★★] 프로젝트 README: `d:\Noogs\NextCloud\Projects\UnityBrain\README.md`

---

#### 🔑 Core Concepts (핵심 개념)
  * **`ProjectSettingsWatcher` & 컨텍스트 문자열 자동완성:** `TagManager.asset`, `InputManager.asset`, `EditorBuildSettings.asset`, `.controller`, `.inputactions`, `.uxml` 등 Unity 비표준 YAML(`!u!` 태그 포함)을 전용 경량 파서로 읽어 `Library/UnityBrainCache/project-settings.json`에 캐싱한다. C# 코드에서 `CompareTag("`, `LayerMask.GetMask("`, `Input.GetAxis("`, `SceneManager.LoadScene("`, `Material.SetFloat("`, `Animator.SetTrigger("`, `Resources.Load("`, `.Q<T>("` 같은 문자열 리터럴 컨텍스트일 때만 후보를 제안해 기본 Roslyn IntelliSense와 충돌하지 않는다. [FACT]
  * **Ctrl+Hover와 F12 정의 이동 분리:** `provideDefinition`이 Ctrl+Hover 시 UDP 패킷 전송이나 토스트 표시 같은 부작용을 일으키지 않도록 순수 `vscode.Location`만 반환하게 분리하고, `F12`(`unitySense.csharpGoToDefinition`) 실행 시 라이브 Unity Editor UDP 이동과 로컬 파일 폴백을 처리한다. [FACT]
  * **Unity 이벤트 함수 생성기 (`UnityEventGenerator`):** `unity-api.json`을 바탕으로 `MonoBehaviour`, `NetworkBehaviour`, `UIBehaviour`, `ScriptableObject`, `Editor`, `StateMachineBehaviour` 상속 계층을 판별해 미구현 이벤트 함수 스니펫(코루틴 `IEnumerator` 및 Unity 6 `async Awaitable` 포함)과 일괄 생성 QuickPick 커맨드를 제공한다. [FACT]
  * **비침습적 MSBuild 주입 (`Directory.Build.props`):** Unity 에디터는 스크립트 변경 시 `.csproj`를 덮어쓰므로 `.csproj`를 직접 수정하지 않는다. 대신 프로젝트 루트 `Directory.Build.props`의 `<!-- UnityBrain:Begin --> ... <!-- UnityBrain:End -->` 마커 블록만 idempotent하게 관리해 `<Analyzer>`(`UnityBrain.Analyzers.dll`)와 `<AdditionalFiles>`(`project-settings.json`, `unity-api.json`)를 주입한다. [FACT]
  * **Roslyn 정적 분석기(`UB1xxx`~`UB4xxx`) 및 `DiagnosticSuppressor`:** Microsoft 공식 `UNT0001`~`UNT0038` 규칙을 재구현하지 않고 다음 고유 규칙을 구현했다. [FACT]
    * `UB1001`/`UB1002`: `Update`/`FixedUpdate`/`LateUpdate` 등 성능 민감 루트에서 클래스 내 콜그래프(BFS)를 추적해 간접 호출된 `GetComponent`, `Camera.main`, `FindObjectOfType` 등을 탐지하고 `Awake()` 자동 캐싱 Quick-Fix/CodeFix 제공
    * `UB1003`: `Shader.PropertyToID` / `Animator.StringToHash` 정적 필드(`static readonly int`) 자동 추출 Quick-Fix/CodeFix
    * `UB2001`~`UB2004`: `[BurstCompile]` 내 관리형 참조 타입, Boxing, 문자열 할당, 예외(`try`/`catch`/`throw`) 정적 검사 (`[BurstDiscard]` 예외 허용)
    * `UB3001`~`UB3003`: `Invoke`/`StartCoroutine` 메서드명, Tag/Layer, Build Scene 문자열 정합성 검증
    * `UB4001` & 경고 억제: `[FormerlySerializedAs]` 리팩토링 및 Unity 직렬화 필드(`CS0649`/`CS0169`/`IDE0051`) 경고 억제

---

#### 🛠️ Procedures (절차)
  1. **설정 감시 및 캐시 생성:** `ProjectSettingsWatcher`가 300ms 디바운스로 `ProjectSettings/*.asset`과 `.controller`를 파싱해 `project-settings.json`을 갱신한다. [★★★★★]
  2. **MSBuild 주입기 실행:** `MsbuildAnalyzerInjector`가 `Directory.Build.props`에 분석기 DLL과 `AdditionalFiles`를 주입한다. 경로 내 MSBuild 특수문자(`%`, `'`, `;`, `$`)는 `%25`/`%27`/`%3B`/`%24`로 선행 이스케이프한다. [★★★★★]
  3. **콜그래프 및 CodeFix 적용:** `PerformanceCriticalContextAnalyzer`가 동일 클래스 및 `partial class`의 메서드 호출 그래프를 구축하고, `CacheComponentInAwakeCodeFixProvider`와 `ExtractPropertyToIdCodeFixProvider`가 단건 및 `Fix All` 자동 리팩토링을 수행한다. [★★★★★]

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **한 클래스 내 다중 `Fix All` 시 중복 `Awake()` 및 중복 필드 충돌 (v1.1.6)**
    * 원인: 기본 병렬 `FixAllProvider`로 여러 `UB1001`/`UB1002`/`UB1003` 진단을 동시에 수정하면 각 수정본이 서로의 `Awake()` 생성 여부를 알지 못해 `Awake()`가 중복 생성되거나 필드명이 충돌했다.
    * 해결법: `SequentialFixAllProvider`를 도입해 진단을 순차 적용하도록 변경하고, 다른 `partial class` 파일에 이미 `Awake()`가 있는 경우에도 `Solution` 단위 CodeAction으로 해당 `Awake()`에 초기화 구문을 주입해 런타임 `NullReferenceException`을 방지했다. [FACT]
    * 신뢰도: [★★★★★]
  * **식 본문 `Awake() => ...;`, C# 11 raw string, 주석/문자열 내 `Camera.main` 오탐 (v1.1.6)**
    * 원인: `Awake() => Init();` 같은 식 본문(expression-bodied) 메서드나 C# 11 raw string(`"""..."""`), `@$"..."`, 주석/문자열 내부의 `Camera.main`과 `{}` 중괄호가 Extension Quick-Fix 파서를 오작동시켰다.
    * 해결법: `maskCSharpCommentsAndStrings`가 C# 11 raw string과 중첩 보간식까지 마스킹하도록 보강하고, `Awake() => ...;` 블록 자동 변환, 같은 줄 다중 `Camera.main`, `Awake()` 내부 자기참조 방지, 숫자 시작 해시 ID(`_2DArray` → `_2DArrayId`) 처리를 추가했다. [FACT]
    * 신뢰도: [★★★★★]
  * **오프라인 `.NET 10` SDK 환경 vs `netstandard2.0` Roslyn 호스트 호환성 (v1.1.1, v1.1.4)**
    * 원인: Roslyn Component 표준은 `netstandard2.0`이지만 로컬 오프라인 빌드 환경에는 NuGet 복원이 불가능했고, `.NET 8+` 전용 `.Contains(..., StringComparison)` API 3건이 `netstandard2.0` 컴파일을 깨뜨렸다.
    * 해결법: `UseNuGetNetStandard=true` 릴리스 프로파일(`netstandard2.0` + `Microsoft.CodeAnalysis.CSharp.Workspaces 4.3.1`)과 오프라인 로컬 SDK 빌드 프로파일을 이중화하고, `netstandard2.0` 비호환 API 3건과 `SimpleJson` UTF-8 BOM 처리를 수정했다. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [FACT] `Camera.main`(`UB1001`)에 대해서도 `v1.1.5`부터 `GetComponent<T>()`와 동일하게 `Awake()` 자동 캐싱 Quick-Fix와 Roslyn CodeFix를 양쪽 모두 지원하도록 확장했다.

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 실제 Unity 프로젝트에서 C# Dev Kit과 함께 `Directory.Build.props`를 통해 `UB1xxx`~`UB4xxx` 진단 및 `Ctrl+.` CodeFix를 실행했을 때의 동작 여부 및 체감 속도 확인 필요
  * [ ] `CS0649`/`CS0169`/`IDE0051` 경고 억제(`DiagnosticSuppressor`)가 `[SerializeField]` 필드에서 의도대로 동작했는지 확인 필요

---

#### 🏷️ Tags
Unity, CSharp, Roslyn, CodeFix, BurstCompile, MSBuild, UnitySense

===== KNOWLEDGE PACKAGE END =====
