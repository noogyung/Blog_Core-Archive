---
topic: unitysense-04-omni-shader-tools-and-variants
title_kr: "Omni Shader 도구 통합: 배리언트 추적, 키워드 Dimming, 혼합 포매터와 Explorer"
category: Workflow
sub_category: Shader-Tools
version: 1.0.1
status: Experimental
created_date: 2026-11-22
last_modified: 2026-11-22
language: KR+EN
tags: [Shader, VSCode, UnitySense]
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

* **Topic:** unitysense-04-omni-shader-tools-and-variants
* **Title_KR:** Omni Shader 도구 통합: 배리언트 추적, 키워드 Dimming, 혼합 포매터와 Explorer
* **Category:** Workflow
* **Sub-Category:** Shader-Tools
* **Version:** 1.0.1
* **Status:** Experimental
* **Date:** 2026-11-22
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Sub-Plan 07 기획서: `d:\Noogs\NextCloud\Projects\UnityBrain\docs\plans\07-omni-shader-integration.md`
  * [★★★★★] Sub-Plan 06 기획서(셰이더 키워드 상태바): `d:\Noogs\NextCloud\Projects\UnityBrain\docs\plans\06-meta-sync-and-integration.md`
  * [★★★★★] 릴리스 변경 이력 및 README: `d:\Noogs\NextCloud\Projects\UnityBrain\CHANGELOG.md`, `README.md`

---

#### 🔑 Core Concepts (핵심 개념)
  * **확장 셰이더 포맷 및 Unity 6 / SM 6.x 문법 지원:** `.shader`, `.hlsl`, `.cginc`, `.compute` 외에도 `.urtshader`, `.hlsli`, `.cg`, `.hlslinc`, `.raytrace`, `.fx`, `.fxh` 확장자와 Unity 6 `#pragma dynamic_branch`, `#include_with_pragmas`를 포괄하도록 TextMate 문법과 프로바이더를 확장했다. [FACT]
  * **배리언트(Variant) 수 계산 CodeLens & 키워드 컨텍스트 상태바:** `#pragma multi_compile(_local)?`, `#pragma shader_feature(_local)?`, `#pragma dynamic_branch` 라인을 파싱해 각 Pass/HLSLPROGRAM 상단에 예상 배리언트 조합 수(`Π group.length`)를 CodeLens로 표시한다. 하단 상태바(`Shader Keywords`)에서 활성 키워드를 토글하면 `#if` / `#ifdef` / `#elif` 비활성 분기를 실시간으로 Dimming 처리한다. [FACT]
  * **ShaderLab + HLSL 혼합 코드 포매터 (`shaderFormatter.ts`):** `.shader` 파일 내 ShaderLab 영역(`Properties`, `SubShader`, `Pass`, `Tags`, `Stencil`)과 HLSL/CG 블록을 구분해 문서/범위/On-Type 포매팅을 수행하며, 문자열·주석·전처리기 지시문을 보존한다. [FACT]
  * **UnitySense Shaders 탐색기 & Shader Live Profiler:** VS Code TreeView(`unitySense.shaderExplorer`)에서 프로젝트 셰이더의 `Properties`, `SubShader/Pass`, `#pragma` 키워드, 예상 배리언트 수(128개 초과 시 경고)를 탐색하고 클릭 시 해당 라인으로 이동한다. `unitySense.testCurrentShader`는 TMU/ALU 추정 부하 등급을 분석해 Diagnostic으로 출력한다. [FACT]
  * **ShaderLab DSL 컨텍스트 완성 & 확장 정적 진단:** `Properties` 어트리뷰트(`[HDR]`, `[MainTexture]` 등), `Tags`, `Blend`/`Stencil` 렌더 상태 자동완성과 URP/HDRP/Compute 스니펫을 추가했다. 또한 `HLSLPROGRAM`/`ENDHLSL` 짝 불일치, `Properties`-`CBUFFER_START(UnityPerMaterial)` SRP Batcher 정합성, `#pragma vertex`/`fragment`/`kernel` 엔트리 미정의 진단을 통합했다. [FACT]

---

#### 🛠️ Procedures (절차)
  1. **Gap Analysis 기반 중복 배제:** 기존 `shader-lsp`와 `shaderEditorProviders.ts`에 이미 구현된 기능(Semantic Tokens, Folding, DocumentSymbol, Color Picker, Rename 등)을 제외하고, 미구현 영역(포매터, ShaderLab DSL 완성, 스니펫, 확장 진단, Shader Explorer)만 선별 통합했다. [★★★★★]
  2. **전처리기 평가기 구현:** `eval`을 사용하지 않는 안전한 전처리기 조건 파서를 구현해 `#if defined(...)`, `#elif`, `#if 0`, `#define FOO 0` 및 활성 키워드 상태에 따라 비활성 코드 영역을 계산한다. [★★★★★]
  3. **혼합 포매터 멱등성 검증:** 중첩된 `Properties` / `SubShader` / `Pass` / `HLSLPROGRAM` / `#if` 구조에서 포매터를 2회 연속 실행해도 diff가 발생하지 않도록 테스트를 구성했다. [★★★★★]
  4. **Shader Explorer 및 Live Profiler 등록:** `packages/extension/package.json`의 `viewsContainers` / `views`에 `unitySense.shaderExplorer`를 등록하고, `unitySense.shader.format.indentSize`, `unitySense.shader.diagnostics.enabled` 설정 옵션을 노출했다. [★★★★★]

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **Include-guard `#ifndef`/`#define` 자기 자신 Dimming 및 `#if 0` / `#define FOO 0` 오작동 (v1.1.4, v1.1.6)**
    * 원인: 헤더 상단의 `#ifndef MY_HEADER_INCLUDED` 직후 `#define MY_HEADER_INCLUDED`가 등장할 때 평가 순서 오류로 블록 전체가 비활성화(Dimming)되거나, `#define FOO 0`을 단순히 "정의됨(true)"으로 처리해 `#if FOO` 분기 판단이 어긋났다.
    * 해결법: include-guard 패턴 예외 처리 및 매크로의 정의 여부(`defined(FOO)`)와 수치 평가(`#if 0`, `#define FOO 0`)를 분리해 비활성 분기 Dimming 오작동을 제거했다. 멀티라인 `#elif` 활성 줄 Dimming 오류도 함께 수정했다. [FACT]
    * 신뢰도: [★★★★★]
  * **블록 주석 종료 줄 포매팅 및 Stencil `Pass` 오탐 (v1.1.2, v1.1.4)**
    * 원인: `Stencil { Pass Replace }` 내부의 `Pass` 키워드가 ShaderLab의 `SubShader { Pass { ... } }` 블록 시작으로 오인되거나, 블록 주석(`/* ... */`) 종료 라인에서 들여쓰기가 깨지는 문제가 있었다.
    * 해결법: 주석/문자열/`Stencil` 내부 `Pass` 마스킹을 적용하고 블록 주석 종료 줄 포매팅 규칙을 보완했다. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [FACT] 외부 셰이더 확장(Omni Shader Tools, Shader languages support 등)을 별도로 여러 개 설치하면 언어 ID(`shaderlab`, `hlsl`)와 문법/포매터가 서로 충돌하기 쉬워, 핵심 생산성 기능을 단일 확장에 통합하는 방향을 택했다.

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 실제 프로젝트에서 Shader Explorer의 128 배리언트 초과 경고나 Shader Live Profiler(`unitySense.testCurrentShader`)를 실행해 본 결과 및 유용성 피드백 필요
  * [ ] ShaderLab+HLSL 혼합 포매터를 실제 복잡한 URP 커스텀 셰이더에 적용했을 때 들여쓰기 결과에 어색한 부분이 없었는지 확인 필요

---

#### 🏷️ Tags
Unity, ShaderLab, HLSL, Shader-Variants, Formatter, Shader-Explorer, UnitySense

===== KNOWLEDGE PACKAGE END =====
