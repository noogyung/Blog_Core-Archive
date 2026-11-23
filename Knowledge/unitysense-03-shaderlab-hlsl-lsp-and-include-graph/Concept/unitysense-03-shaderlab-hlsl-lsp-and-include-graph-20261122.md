---
topic: unitysense-03-shaderlab-hlsl-lsp-and-include-graph
title_kr: "ShaderLab/HLSL 전용 LSP 구축: 오프셋 보존 가상 문서와 다단계 Include 그래프"
category: Concept
sub_category: Shader-LSP
version: 1.0.1
status: Verified
created_date: 2026-11-22
last_modified: 2026-11-22
language: KR+EN
tags: [Shader, HLSL, UnitySense]
sources_count: 3
blog_draft_path: Blog_Posts/2026-11/unitysense-03-shaderlab-hlsl-lsp-and-include-graph.html
blog_draft_date: 2026-11-23
blog_id: core-archive
blog_published: false
blogger_post_id: "6360212940952139526"
series_id: unitysense
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** unitysense-03-shaderlab-hlsl-lsp-and-include-graph
* **Title_KR:** ShaderLab/HLSL 전용 LSP 구축: 오프셋 보존 가상 문서와 다단계 Include 그래프
* **Category:** Concept
* **Sub-Category:** Shader-LSP
* **Version:** 1.0.1
* **Status:** Verified
* **Date:** 2026-11-22
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Sub-Plan 02 기획서: `d:\Noogs\NextCloud\Projects\UnityBrain\docs\plans\02-shaderlab-hlsl-lsp.md`
  * [★★★★★] 릴리스 변경 이력(v1.1.4~v1.1.6 포함): `d:\Noogs\NextCloud\Projects\UnityBrain\CHANGELOG.md` 및 `git show 725a863:CHANGELOG.md`
  * [★★★★★] 프로젝트 README: `d:\Noogs\NextCloud\Projects\UnityBrain\README.md`

---

#### 🔑 Core Concepts (핵심 개념)
  * **1:1 UTF-16 오프셋 보존 가상 HLSL 문서(`extractVirtualHlslDocument`):** `.shader` 파일은 ShaderLab DSL(`Properties`, `SubShader`, `Pass`)과 `HLSLPROGRAM`/`HLSLINCLUDE`/`CGPROGRAM`/`CGINCLUDE` 블록이 섞여 있다. 블록 외부의 ShaderLab 문자는 줄바꿈(`\n`, `\r`)을 제외한 모든 문자를 공백(`' '`)으로 치환(Space Padding)해 가상 HLSL 문서를 생성한다. 별도의 Source Map 역변환 없이 원본 `.shader`와 라인·컬럼 오프셋이 1:1로 일치한다. [FACT]
  * **Unity HLSL 매크로 전처리 프리패스(`UnityHlslMacroPrepass`):** 표준 C++/HLSL 파서가 선언문으로 인식하지 못하는 URP/HDRP 매크로(`TEXTURE2D`, `TEXTURE2D_X_*`, `RW_TEXTURE2D`, `SAMPLER`, `CBUFFER_START`/`END`, `UNITY_INSTANCING_BUFFER_START`, `TEXTURE2D_PARAM`)를 표준 HLSL 선언 형태로 정규화하되, 심볼의 `Range`는 원본 텍스트 오프셋 기준으로 보존한다. [FACT]
  * **다단계 `#include` DAG 및 Unity 6 `#include_with_pragmas`:** `UnityVirtualPathResolver`와 연동해 `#include "..."`, `#include <...>`, Unity 6의 `#include_with_pragmas`를 재귀적으로 추적한다. 방문 집합(`visited`)과 글로벌 캐시로 순환 참조 및 중복 파싱을 방지하며, C/HLSL 전처리기 평가 순서(현재 파일 커서 위쪽 → DFS Include 순서)대로 심볼을 검색한다. [FACT]
  * **소유 구조체(`containerName`) 기반 멤버 구별:** `Attributes.texcoord`와 `Varyings.texcoord`처럼 서로 다른 구조체에 동일 필드명이 존재할 때, 표현식 체인과 소유 구조체(`containerName`) 및 함수 파라미터 스코프(`Vert`의 `input` vs `Frag`의 `input`)를 분리해 Definition(`F12`), Hover, References, Rename, DocumentHighlight가 정확한 대상을 가리키도록 구현했다. [FACT]

---

#### 🛠️ Procedures (절차)
  1. **가상 문서 추출 및 `HLSLINCLUDE` 암묵적 연결:** `.shader`에서 `HLSLINCLUDE`/`CGINCLUDE`와 `HLSLPROGRAM`/`CGPROGRAM`을 추출하고, 각 `Pass`의 `HLSLPROGRAM`이 상위 `SubShader`의 `HLSLINCLUDE`를 암묵적 `#include` 스코프로 상속받도록 연결한다. `Properties { ... }` 선언도 전역 심볼로 등록해 HLSL 본문에서 `_BaseMap`을 `F12`로 추적할 수 있게 한다. [★★★★★]
  2. **심볼 테이블 구축(`HlslSymbolTable`):** 함수 정의/전방 선언, 구조체 및 멤버 필드, `#define` 상수/함수형 매크로, `cbuffer` 변수, Texture/Sampler, ShaderLab 프로퍼티를 인덱싱한다. [★★★★★]
  3. **LSP 프로바이더 연결:**
     * `ShaderDefinitionProvider`: `#include` 경로 이동, `#pragma vertex/fragment/kernel` 엔트리 이동, `varName.fieldName` 멤버 타입 추론 이동, 일반 심볼 `F12` 이동을 처리한다.
     * `ShaderCompletionProvider` / `ShaderHoverProvider`: `#include "Packages/..."` 경로 완성, 구조체 멤버 완성, 심볼 시그니처와 출처 가상 경로 Hover를 제공한다.
     * 커스텀 RPC `unityBrain/getShaderPropertyIndex`를 통해 Extension의 C# `Material.SetFloat("_BaseMap", ...)` 자동완성과 실시간 연동한다. [★★★★★]

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **서로 다른 구조체의 동일 필드명 충돌 (v1.1.5)**
    * 원인: `Step8Attributes.texcoord`와 `Step8Varyings.texcoord`처럼 필드명이 같을 때 단순 이름 매칭만으로는 Hover, Definition, References, Rename이 엉뚱한 구조체 필드를 참조했다.
    * 해결법: 심볼에 `containerName`을 기록하고 표현식 체인 타입 추론 및 함수 파라미터 스코프 분리를 적용해 정확한 구조체 멤버만 매칭하도록 수정했다. [FACT]
    * 신뢰도: [★★★★★]
  * **`.uv`/`.r`/`.x` 구조체 필드 Rename 차단 (v1.1.6)**
    * 원인: `ShaderRenameProvider`가 `.uv`, `.r`, `.x` 같은 구조체 필드명을 벡터 스위즐(swizzle)로 오인해 이름 변경을 차단했다.
    * 해결법: `isVectorSwizzleToken`을 표현식 체인 해석 기반 판별로 변경하고 `uv`를 스위즐 오탐 대상에서 제외했다. [FACT]
    * 신뢰도: [★★★★★]
  * **이모지/비-BMP 문자 뒤 UTF-16 컬럼 밀림 (v1.1.4, v1.1.6)**
    * 원인: 주석이나 문자열에 이모지 등 비-BMP 문자가 포함될 때 `Array.from()`(유니코드 코드 포인트 단위)으로 마스킹하면 VS Code LSP의 UTF-16 코드 유닛 오프셋과 어긋났다.
    * 해결법: `stripCommentsAndStringsByLine`, `ShaderDocumentSymbolProvider`, `maskShaderLabTriviaAndBlocks`의 문자 처리를 UTF-16 코드 유닛 기준(`split('')`)으로 통일했다. [FACT]
    * 신뢰도: [★★★★★]
  * **`.hlsl` 헤더 수정/Rename 시 형제 `.shader` 누락 (v1.1.6)**
    * 원인: `.shader`에서 `#include`된 `.hlsl` 심볼을 References/Rename할 때 동일 헤더를 공유하는 다른 `.shader` 파일이 탐색되지 않았다.
    * 해결법: 동일 헤더를 포함하는 형제 `.shader` 파일까지 함께 탐색·수정하고, 멀티패스 셰이더에서 타 Pass 구조체보다 `#include` 헤더의 구조체를 우선 해석하도록 보완했다. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [FACT] `Library/PackageCache` 전체를 미리 파싱하지 않고, 열려 있는 파일과 `Assets/` 내부 `.shader`/`.hlsl`에서 실제로 `#include`로 참조되는 그래프만 온디맨드로 추적·캐싱해 메모리와 초기 지연을 줄였다.
  * [OPINION] 직업 특성상 셰이더 파일을 자주 오가며 작업하는데, HLSL 추적 기능이 정상 동작하며 유용하게 사용하고 있다. — 출처: 사용자 피드백 (2026-11-22)
  * [OPINION] `Step8Attributes.texcoord`와 `Step8Varyings.texcoord`처럼 Vertex Shader와 Fragment Shader에서 같은 이름의 필드를 쓰더라도 서로 목적이 다를 수 있고, 시맨틱을 다르게 쓰며 목적이 변경되면 개별적으로 수정해야 하는 경우가 있어 구조체별 구별이 필수적이었다. — 출처: 사용자 피드백 (2026-11-22)

---

#### ❓ Missing Info (검증 필요 항목)
  * [x] 실제 셰이더 작업에서 HLSL 추적(`F12`) 동작 및 활용도 확인 — Verified 2026-11-22 (셰이더 간 추적 정상 동작 및 실사용 중)
  * [x] `v1.1.5`의 `Step8Attributes.texcoord` vs `Step8Varyings.texcoord` 충돌 맥락 확인 — Verified 2026-11-22 (Vertex/Fragment Shader 간 동일 필드명이라도 시맨틱·목적에 따라 개별 수정 필요)

---

#### 🏷️ Tags
Unity, ShaderLab, HLSL, LSP, Tree-sitter, Include-Graph, UnitySense

===== KNOWLEDGE PACKAGE END =====

---
## 📝 Feedback History

### 2026-11-22 — Test Result: PASS
* **환경:** 로컬 Unity 셰이더 작업 환경 + VS Code
* **검증된 단계:** 다단계 HLSL 추적 및 구조체별 동일 필드명 분리 맥락 확인 완료
* **실패한 단계:** 없음
* **새 오류/팁:** Vertex/Fragment Shader의 동일 필드명(`texcoord` 등)은 목적과 시맨틱이 달라 개별 수정이 필요함
* **Status 변경:** Experimental → Verified
