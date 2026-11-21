---
topic: unitysense-01-architecture-and-ai-playbook
title_kr: "VS Code용 Unity Rider 대체제 설계: 역할 경계와 AI 무중단 개발 구조"
category: Workflow
sub_category: Architecture
version: 1.0.1
status: Experimental
created_date: 2026-11-22
last_modified: 2026-11-22
language: KR+EN
tags: [Unity, VSCode, UnitySense]
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

* **Topic:** unitysense-01-architecture-and-ai-playbook
* **Title_KR:** VS Code용 Unity Rider 대체제 설계: 역할 경계와 AI 무중단 개발 구조
* **Category:** Workflow
* **Sub-Category:** Architecture
* **Version:** 1.0.1
* **Status:** Experimental
* **Date:** 2026-11-22
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] 프로젝트 마스터 기획서: `d:\Noogs\NextCloud\Projects\UnityBrain\PLAN.md`
  * [★★★★★] AI 자율 실행 지침서: `d:\Noogs\NextCloud\Projects\UnityBrain\AGENTS.md`
  * [★★★★★] 체크포인트 상태 파일: `d:\Noogs\NextCloud\Projects\UnityBrain\PROGRESS.json`
  * [★★★★★] 프로젝트 README: `d:\Noogs\NextCloud\Projects\UnityBrain\README.md`

---

#### 🔑 Core Concepts (핵심 개념)
  * **VS Code용 Rider 대체제 목표 및 역할 경계 (`extensionDependencies`):** VS Code에서 Unity 프로젝트를 개발할 때 기존 Microsoft 공식 확장(`ms-dotnettools.csharp`, `visualstudiotoolsforunity.vstuc`)이 이미 담당하는 기본 C# LSP·디버깅·`UNT0001`~`UNT0038` 진단은 재구현하지 않는다. 대신 Rider에서 쓰던 핵심 기능 중 VS Code 공식 확장에 비어 있는 영역(ShaderLab/HLSL LSP, 도메인 문자열 자동완성, 콜그래프 기반 Roslyn 분석기/Quick-Fix, YAML 에셋 인덱서 CodeLens, `.meta` 동기화)을 전담 보완하는 구조로 설계했다. [FACT]
  * **멀티 프로세스 분산 모노레포 아키텍처:** VS Code Extension Host(단일 스레드 Node.js)의 UI 스레드 블로킹을 막기 위해 기능을 4개 독립 모듈로 분리했다. [FACT]
    * `packages/extension`: VS Code Extension Host (TypeScript/Node.js)
    * `packages/shader-lsp`: ShaderLab/HLSL 전용 Language Server (`web-tree-sitter` + `tree-sitter-hlsl`)
    * `packages/unity-daemon`: `.NET 8` 백그라운드 YAML/GUID 인덱서 데몬 (JSON-RPC 2.0 over stdio)
    * `analyzers/UnityBrain.Analyzers`: Roslyn 정적 분석기 & CodeFix (`UB1xxx`~`UB4xxx`)
  * **계약 우선(Contract-First) 단일 진실 공급원:** `PLAN.md` §3과 `schemas/`(`unity-context`, `unity-api`, `project-settings`, `shader-index`, `daemon-rpc`)에 TypeScript/JSON 스키마를 먼저 고정하여 여러 AI 모델이 서브 플랜(`docs/plans/01~07`)을 나눠 구현해도 결합 시 인터페이스 불일치가 발생하지 않도록 설계했다. [FACT]
  * **체크포인트 기반 무중단 AI 실행 프로토콜 (`AGENTS.md` + `PROGRESS.json`):** 모델의 할당량 제한(Rate Limit)으로 세션이 중단되어도 다음 턴이나 다른 모델이 즉시 이어받을 수 있도록, `PROGRESS.json`에 Step 상태(`pending` / `in_progress` / `completed`), `completedFiles`, `resumeNote`를 파일 단위로 갱신하고 각 Step 테스트 통과 시 로컬 Git 체크포인트 커밋(`chore(checkpoint): complete Step X`)을 자동 기록하는 방식을 채택했다. [FACT]
  * **오픈소스 참조 및 Clean-Room 경계:** Unity API 및 로직 참조는 Apache 2.0 라이선스인 `JetBrains/resharper-unity`를 기준으로 삼으며, 비공개 어셈블리 참조가 필요한 경우에도 사유 코드 복사를 금지하고 매크로명·시그니처 수준의 Clean-Room 스펙 추출만 허용했다. [FACT]

---

#### 🛠️ Procedures (절차)
  1. **전제 확장과 기능 경계표 작성:** `csharp` + `vstuc`의 담당 범위와 신규 구현 범위(`UB1xxx`~`UB4xxx`, Shader LSP, 도메인 자동완성, YAML 인덱서, `.meta` 동기화)를 `PLAN.md` §1.3에 명문화한다. [★★★★★]
  2. **모노레포 및 공유 스키마 확정:** `pnpm-workspace.yaml`과 `UnityBrain.sln` 기반 통합 디렉터리 구조와 5종 JSON/TS 계약(`UnityProjectContext`, `UnityApiDatabase`, `ProjectSettingsCache`, `ShaderSymbolIndex`, `DaemonRpcMethods`)을 정의한다. [★★★★★]
  3. **세부 기획서 분할(`docs/plans/01~07`):** 의존성 그래프(`01 Core` → `02 Shader LSP`, `03 Completion`, `04 Roslyn`, `05 Daemon` → `06 Integration`, `07 Omni Shader`)에 따라 각 모듈의 파일 경로, export 심볼, 알고리즘, Definition of Done(자동화 테스트)을 독립 문서화한다. [★★★★★]
  4. **AI 실행 가드레일 구성(`AGENTS.md`):**
     * 중간 확인 질문 금지 및 빌드/테스트 실패 시 자가 복구 루프 강제
     * 터미널 명령어 단일 접두사(`pnpm`, `dotnet`, `git`, `node`) 분리 실행으로 Always Allow 매칭 유지
     * 파일 단위 `PROGRESS.json` 마이크로 체크포인트 갱신 및 Step 완료 시 Git 체크포인트 커밋 수행 [★★★★★]

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **AI 장기 세션 중 Rate Limit 도달 및 컨텍스트 단절**
    * 원인: 대규모 모노레포(TypeScript + C# Roslyn + .NET 데몬)를 단일 세션에서 연속 구현할 때 사용량 제한으로 작업이 도중에 멈추면, 다음 세션에서 이미 작성한 파일을 덮어쓰거나 누락하는 문제가 발생한다.
    * 해결법: `PROGRESS.json`에 각 Step별 `targetFiles`, `completedFiles`, `verificationCommands`, `resumeNote`를 기록하고, 재개 시 `in_progress` Step의 `completedFiles`를 건너뛰어 남은 파일부터 이어서 작성하는 복구 알고리즘을 `AGENTS.md`에 명시했다. [FACT]
    * 신뢰도: [★★★★★]
  * **터미널 복합 명령어로 인한 반복 승인 팝업 중단**
    * 원인: `&&`, `;`, `$(...)`, `$VAR` 등을 포함한 복합 셸 명령은 에이전트 IDE의 명령어 접두사 자동 승인(Always Allow) 매칭을 깨뜨려 무중단 실행을 가로막는다.
    * 해결법: `AGENTS.md`에서 복합 체인과 셸 변수 확장을 금지하고 `pnpm`, `dotnet`, `git`, `node` 단일 접두사 명령을 개별 도구 호출로 분리하도록 강제했다. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] 이 시리즈는 "VS Code에서 Rider 수준 달성"을 내세우기보다, 실제 Unity 개발 환경에서 Rider를 대체하기 위해 비어 있는 공백을 하나씩 구현하고 검증한 개발 기록으로 서술한다. — 출처: 사용자 방향성 지침

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] Rider 대신 VS Code용 대체제를 직접 개발하기로 결심한 구체적인 계기(예: 라이선스 비용, 메모리 점유율, AI 코딩 도구/에디터 통합 워크플로우 등) 확인 필요
  * [ ] `PROGRESS.json`과 `AGENTS.md` 기반으로 실제 개발을 진행할 때 어떤 AI 모델 조합(Claude, Codex, Gemini 등)을 사용했고, Rate Limit 중단 후 이어하기가 실제로 몇 회 정도 동작했는지 경험 확인 필요
  * [ ] 최초 코드명 `UnityBrain`으로 시작해 프로젝트 마지막에 `UnitySense`로 브랜딩을 바꾼 배경 확인 필요 (1편 도입부 또는 8편에서 다룰 포인트)

---

#### 🏷️ Tags
Unity, VSCode, UnitySense, Rider, Architecture, AI-Workflow, Monorepo

===== KNOWLEDGE PACKAGE END =====
