---
topic: unitysense-02-virtual-vfs-and-api-extractor
title_kr: "Unity 가상 경로 해석기(VFS)와 resharper-unity API 추출기 구현"
category: Concept
sub_category: Core
version: 1.0.1
status: Verified (Partial)
created_date: 2026-11-22
last_modified: 2026-11-22
language: KR+EN
tags: [Unity, VFS, UnitySense]
sources_count: 3
blog_draft_path: Blog_Posts/2026-11/unitysense-02-virtual-vfs-and-api-extractor.html
blog_draft_date: 2026-11-23
blog_id: core-archive
blog_published: false
blogger_post_id: null
series_id: unitysense
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** unitysense-02-virtual-vfs-and-api-extractor
* **Title_KR:** Unity 가상 경로 해석기(VFS)와 resharper-unity API 추출기 구현
* **Category:** Concept
* **Sub-Category:** Core
* **Version:** 1.0.1
* **Status:** Verified (Partial)
* **Date:** 2026-11-22
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Sub-Plan 01 기획서: `d:\Noogs\NextCloud\Projects\UnityBrain\docs\plans\01-core-and-virtual-vfs.md`
  * [★★★★★] 마스터 아키텍처 문서: `d:\Noogs\NextCloud\Projects\UnityBrain\PLAN.md`
  * [★★★★★] 릴리스 변경 이력: `d:\Noogs\NextCloud\Projects\UnityBrain\CHANGELOG.md`

---

#### 🔑 Core Concepts (핵심 개념)
  * **`UnityProjectContextManager`:** 워크스페이스 루트(및 1단계 하위 폴더)에서 `Assets/`와 `ProjectSettings/ProjectVersion.txt`가 동시에 존재하는 디렉터리를 Unity 프로젝트로 판별한다. `m_EditorVersion`을 정규식으로 추출하고, 사용자 지정 경로(`unitySense.unityEditorPath`) → OS별 Unity Hub 기본 설치 경로 및 `secondaryInstallPath.json` 순으로 `Data/CGIncludes`와 `BuiltInPackages` 경로를 탐색한다. [FACT]
  * **`UnityVirtualPathResolver` (가상 VFS):** Unity 셰이더의 `#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"` 같은 가상 경로는 디스크상의 물리 경로와 직접 일치하지 않는다. 이를 `packages-lock.json`과 `manifest.json`을 바탕으로 5가지 패키지 소스(`embedded`, `local`, `registry`, `git`, `builtin`) 및 `CGIncludes`, `Assets/`, 상대 경로로 결정론적으로 매핑한다. [FACT]
  * **Unity 6 PackageCache 해시 폴더 및 SemVer 폴백:** Unity 6부터 `Library/PackageCache` 하위 폴더명이 `<packageName>@<version>` 대신 `<packageName>@<hash>` 형태로 생성되는 경우가 있다. 정확한 버전 경로가 없으면 `Library/PackageCache` 디렉터리 내 `{packageName}@*` 프리픽스를 스캔하고 SemVer 정렬 폴백을 적용해 호환성을 확보했다. [FACT]
  * **`tools/api-extractor` (`api.xml` → `unity-api.json`):** Apache 2.0 라이선스인 `JetBrains/resharper-unity`의 `api.xml`을 `fast-xml-parser`로 파싱해 `UnityApiDatabase` JSON(`packages/extension/resources/unity-api.json`)으로 변환한다. 이 단일 JSON을 Extension의 이벤트 함수 생성기와 Roslyn 분석기(`UB1xxx`의 `isPerformanceCritical` 판별)가 공통으로 소비한다. [FACT]

---

#### 🛠️ Procedures (절차)
  1. **컨텍스트 초기화 및 변경 감시:** `UnityProjectContextManager`가 `ProjectSettings/ProjectVersion.txt`, `Packages/manifest.json`, `Packages/packages-lock.json`을 감시하고 변경 시 `onDidChangeContext` 이벤트를 발행해 `shader-lsp`와 `unity-daemon`에 최신 상태를 전파한다. [★★★★★]
  2. **패키지 경로 해석 우선순위 적용:**
     * `Packages/<packageName>/<subpath>`: `packages-lock.json`의 `source`에 따라 `embedded`(`Packages/` 및 하위 `package.json`의 `name` 스캔) → `local`(`file:` 상대/절대 경로) → `registry`/`git`(`Library/PackageCache/{pkg}@{ver}` 및 `{pkg}@*` 폴백) → `builtin`(`BuiltInPackages/{pkg}`) 순으로 해석한다.
     * `Assets/<subpath>`: `{projectRoot}/Assets/{subpath}`로 직접 해석한다.
     * 일반/상대 경로: 현재 파일 디렉터리 → 프로젝트 루트 → `{cgIncludesPath}` 순으로 탐색한다. [★★★★★]
  3. **가상 디렉터리 목록 API 제공:** `#include "` 자동완성을 위해 `listVirtualDirectory(virtualPrefix)` 메서드를 구현해 `Packages/` 입력 시 설치된 패키지 목록과 하위 `.hlsl`/`.cginc` 파일 목록을 반환한다. [★★★★★]
  4. **Unity API DB 추출 자동화:** `tools/api-extractor` CLI(`node tools/cli.mjs extract:api`)로 `api.xml`에서 타입, 이벤트, 파라미터, 반환형, `supportsCoroutine`, `isPerformanceCritical` 플래그를 추출해 `unity-api.json`을 생성한다. [★★★★★]

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **Unity 6 `Library/PackageCache` 해시 폴더명 미인식**
    * 원인: Unity 2022의 `<packageName>@14.0.11` 규칙만 가정하면 Unity 6의 `<packageName>@<hash>` 폴더 구조에서 URP/Core 셰이더 헤더 경로 해석이 실패한다.
    * 해결법: 정확한 버전 폴더가 디스크에 없을 때 `Library/PackageCache`에서 `{packageName}@*` 프리픽스 폴백 스캔 및 SemVer 정렬을 수행하도록 `UnityVirtualPathResolver`를 보완했다. [FACT]
    * 신뢰도: [★★★★★]
  * **Embedded 패키지의 폴더명과 `package.json` 이름 불일치**
    * 원인: `Packages/` 아래에 로컬 임베디드 패키지를 둘 때 디렉터리명이 실제 `com.company.pkg` 이름과 다를 수 있다.
    * 해결법: `Packages/{packageName}`가 존재하지 않으면 `Packages/` 하위 디렉터리들의 `package.json` `name` 필드를 스캔해 매칭되는 물리 루트를 찾아 연결했다. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [FACT] `UnityVirtualPathResolver`는 Extension Host와 독립 프로세스인 `shader-lsp` 양쪽에서 동일한 해석 결과를 보장해야 하므로 공유 계약과 동일 로직을 유지한다.
  * [OPINION] 현재까지 VFS 경로 해석에서 특수 경로 예외 사례는 발생하지 않았다. Unity 6 `Awaitable` 이벤트는 기본 동작을 확인했으나, 직접 사용해본 적 없는 이벤트여서 정확한 실전 동작 여부는 실제 사용 사례를 통해 추가 확인이 필요한 상태다. — 출처: 사용자 피드백 (2026-11-22)

---

#### ❓ Missing Info (검증 필요 항목)
  * [x] 실제 Unity 프로젝트에서 `Packages/manifest.json` 및 `Library/PackageCache` 경로 해석 시 특수 경로 사례 확인 — Verified 2026-11-22 (현재까지 특수 경로 사례 없음)
  * [ ] `Awaitable` 이벤트의 기본 동작은 확인했으나 실제 사용 사례 기준 정확도 검증 필요

---

#### 🏷️ Tags
Unity, VFS, UnityVirtualPathResolver, API-Extractor, Unity6, UnitySense

===== KNOWLEDGE PACKAGE END =====

---
## 📝 Feedback History

### 2026-11-22 — Test Result: PARTIAL
* **환경:** 로컬 Unity 프로젝트 + VS Code
* **검증된 단계:** 기본 VFS 경로 해석 및 `Awaitable` 이벤트 기본 동작 확인
* **실패한 단계:** 없음 (`Awaitable` 이벤트 실사용 사례는 추후 검증 필요)
* **새 오류/팁:** 현재까지 특수 경로 사례 없음
* **Status 변경:** Experimental → Verified (Partial)
