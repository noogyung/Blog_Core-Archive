---
topic: unitysense-08-release-hardening-and-marketplace
title_kr: "UnitySense 릴리스 감사, 리브랜딩, 범용 CI 구축과 VS Code Marketplace 출시"
category: Troubleshooting
sub_category: Release-CI
version: 1.0.1
status: Verified
created_date: 2026-11-22
last_modified: 2026-11-22
language: KR+EN
tags: [VSCode, CI-CD, UnitySense]
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

* **Topic:** unitysense-08-release-hardening-and-marketplace
* **Title_KR:** UnitySense 릴리스 감사, 리브랜딩, 범용 CI 구축과 VS Code Marketplace 출시
* **Category:** Troubleshooting
* **Sub-Category:** Release-CI
* **Version:** 1.0.1
* **Status:** Verified
* **Date:** 2026-11-22
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] 배포 및 마켓플레이스 가이드: `d:\Noogs\NextCloud\Projects\UnityBrain\docs\PUBLISHING.md`
  * [★★★★★] GitHub Actions 워크플로: `d:\Noogs\NextCloud\Projects\UnityBrain\.github\workflows\release-vsix.yml`
  * [★★★★★] 최신 릴리스 변경 이력: `d:\Noogs\NextCloud\Projects\UnityBrain\CHANGELOG.md`
  * [★★★★★] Git 릴리스 커밋 히스토리 (`895625e`~`0841bf4`): `d:\Noogs\NextCloud\Projects\UnityBrain`

---

#### 🔑 Core Concepts (핵심 개념)
  * **릴리스 감사 및 회귀 테스트 강화 (`v1.1.2` ~ `v1.1.6`):** 초기 `v1.0.0` 이후 실전 릴리스 감사 과정에서 번들러 버그(`v1.1.3`), C#/Shader/Daemon 경계 오류(`v1.1.4`), 구조체 동일 필드 충돌 및 `Camera.main` 캐싱(`v1.1.5`), `FixAllProvider` 순차 적용 및 `partial class` 타 파일 `Awake()` 지원(`v1.1.6`)까지 단계적으로 회귀 테스트(`releaseRegression.test.ts`, `releaseArtifact.test.ts`)와 함께 수정했다. [FACT]
  * **로컬 오프라인 `.NET 10` vs 범용 릴리스 프로파일 이중화:** 로컬 개발 PC가 오프라인 `.NET 10` SDK 환경이어서 기본 빌드는 `.NET 10`용으로 산출된다. 외부/동료 PC(`.NET 8`/`.NET 9` 및 구형 Roslyn C# Dev Kit) 호환을 위해 CI에서 `UseNuGetNetStandard=true`(Analyzers → `netstandard2.0` + `Microsoft.CodeAnalysis.CSharp.Workspaces 4.3.1`)와 `UseNet8Release=true`(Daemon → `net8.0` + `rollForward: Major`)를 적용한 범용 VSIX를 빌드한다. [FACT]
  * **`UnityBrain` → `UnitySense` 리브랜딩 (`921b8c4`, `0aa03bf`):** 확장명, 커맨드 접두사(`unitySense.*`), 설정 키, 탐색기 뷰 ID(`unitySense.shaderExplorer`), 매니페스트(`keywords` 마켓플레이스 최대 5개 제한 준수), GitHub 저장소(`https://github.com/noogyung/unitysence`)를 `UnitySense` 기준으로 전면 정비하고 `v1.0.0`을 출시했다. [FACT]
  * **GitHub Actions 태그 기반 자동 게시 파이프라인 (`release-vsix.yml`):** `v*` 태그 푸시 또는 `workflow_dispatch` 실행 시 Ubuntu 러너(Node 22 + `.NET 8` SDK)에서 타입체크 → 범용 Release 빌드 → fixture-free TS 테스트 + `.NET` Analyzer/Daemon 테스트 → VSIX 패키징 → GitHub Releases 첨부 → `VSCE_PAT` 기반 VS Code Marketplace 자동 게시까지 일괄 수행한다. [FACT]

---

#### 🛠️ Procedures (절차)
  1. **VSIX 산출물 정제:** OPC `[Content_Types].xml`, `extension.vsixmanifest` 메타데이터를 정비하고, 불필요한 데몬 런타임 바이너리를 제외(`UnityBrain.Daemon.dll` 및 `.runtimeconfig.json` 중심)해 패키지 용량과 구성을 정리한다. [★★★★★]
  2. **Azure DevOps PAT 및 Marketplace Publisher 준비:** `dev.azure.com`에서 `All accessible organizations` + `Marketplace > Manage` 권한으로 PAT를 발급해 GitHub Secrets(`VSCE_PAT`)에 등록하고, `marketplace.visualstudio.com/manage`에 Publisher(`noogkim`)를 생성한다. [★★★★★]
  3. **범용 CI 워크플로 실행:** `git tag v1.0.1 && git push origin v1.0.1`로 태그를 푸시해 GitHub Releases 업로드와 VS Code Marketplace 게시를 자동 실행한다. [★★★★★]

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **빌드된 확장 번들에서 Node 내장 모듈 import 유실 (`v1.1.3`, 커밋 `895625e`)**
    * 원인: `tools/cli.mjs`의 `rewriteVscodeImportsSync()` 멀티라인 정규식이 `import * as vscode from 'vscode'`를 치환하는 과정에서 인접한 Node 모듈 import 구문까지 함께 손상시켜 VS Code 확장 활성화 시 치명적 오류가 발생했다.
    * 해결법: 정규식 치환 로직을 안전하게 수정하고, `packages/extension/test/e2e/releaseArtifact.test.ts`에 실제 빌드 산출물의 import 보존 여부를 검증하는 자동 테스트를 추가했다. [FACT]
    * 신뢰도: [★★★★★]
  * **GitHub Actions `step if` 내 `secrets` 참조 오류 및 로컬 픽스처 의존 테스트 실패 (커밋 `ee7fda7`)**
    * 원인: GitHub Actions 워크플로에서 `if:` 조건식 내부에 `secrets.VSCE_PAT`를 직접 참조해 문법/실행 오류가 발생했고, 로컬 전용 테스트 픽스처에 의존하는 일부 테스트가 클린 CI 러너에서 실패했다.
    * 해결법: `VSCE_PAT`를 `env:`로 주입한 뒤 셸 스크립트 내부에서 비어 있는지 검사(`if [ -z "$VSCE_PAT" ]`)하도록 변경하고, CI에서는 픽스처 비의존 단위·회귀·아티팩트 테스트 스위트를 명시 실행하도록 분리했다. [FACT]
    * 신뢰도: [★★★★★]
  * **CI 환경에서 `.NET` 테스트 프로젝트 `project.assets.json` 누락 (커밋 `59b2ac6`)**
    * 원인: 오프라인 빌드용 설정이 남아 있어 GitHub Actions의 `dotnet test` 단계에서 `project.assets.json` 복원이 정상 동작하지 않았다.
    * 해결법: `UnityBrain.Analyzers.csproj`, `UnityBrain.Analyzers.Tests.csproj`, `UnityBrain.Daemon.csproj`와 워크플로 옵션을 정비해 CI에서 정상 복원 및 테스트가 통과하도록 수정했다. [FACT]
    * 신뢰도: [★★★★★]
  * **VS Code Marketplace Publisher ID 대소문자 불일치 (`v1.0.1`, 커밋 `0841bf4`)**
    * 원인: `package.json`에 `"publisher": "NoogKim"`으로 기재했으나 실제 Marketplace Publisher ID가 소문자 `noogkim`이어서 자동 게시 시 일치 오류가 발생했다.
    * 해결법: `packages/extension/package.json`의 `publisher`를 `noogkim`으로 정규화하고 `v1.0.1` 태그로 재배포해 자동 게시를 연동했다. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [FACT] VS Code Marketplace는 `package.json`의 `keywords` 개수를 최대 5개로 제한하므로 패키징 전에 5개 이내로 정렬해야 게시 오류가 나지 않는다.
  * [OPINION] `v1.0.1` 태그 푸시 후 GitHub Actions 빌드와 VS Code Marketplace 자동 배포, 그리고 VS Code 확장 검색창에서의 설치 테스트까지 정상적으로 완료되었다. — 출처: 사용자 피드백 (2026-11-22)
  * [OPINION] `UnityBrain`은 `JetBrains`에서 따온 이름이지만 실제 동작 대상은 `Rider`여서 명칭이 애매했고, 무난하게 쓸 만한 이름으로 `UnitySense`를 선택했다. — 출처: 사용자 피드백 (2026-11-22)
  * [OPINION] Rider를 쓰다가 쓰지 못하게 된 환경에서 불편함을 느껴 시작한 프로젝트이므로, 출근 후 실무 프로젝트에서 돌려보며 확인할 문제가 아직 많다고 본다. 향후 계획은 신규 기능 추가보다 실제 사용 중 나오는 불편사항을 개선하는 유지보수 중심이다. — 출처: 사용자 피드백 (2026-11-22)

---

#### ❓ Missing Info (검증 필요 항목)
  * [x] `v1.0.1` 태그 푸시 후 VS Code Marketplace 자동 배포 및 확장 검색창 설치 테스트 결과 확인 — Verified 2026-11-22 (빌드, 자동 배포, 검색창 설치 정상 완료)
  * [x] `UnityBrain` → `UnitySense` 리브랜딩 이유 및 향후 로드맵 확인 — Verified 2026-11-22 (명칭 정리 + 향후 신규 기능보다 실무 사용 중 불편사항 개선·유지보수 중심)

---

#### 🏷️ Tags
VSCode-Marketplace, GitHub-Actions, VSIX, DotNet, Release, UnitySense

===== KNOWLEDGE PACKAGE END =====

---
## 📝 Feedback History

### 2026-11-22 — Test Result: PASS
* **환경:** GitHub Actions + VS Code Marketplace (`noogkim.unitysense` `v1.0.1`)
* **검증된 단계:** 태그 푸시 빌드, Marketplace 자동 배포, VS Code 확장 검색창 설치 테스트 완료
* **실패한 단계:** 없음
* **새 오류/팁:** 향후 로드맵은 새 기능 추가가 아니라 실무 프로젝트 사용 중 발생하는 불편사항 개선(유지보수) 중심
* **Status 변경:** Experimental → Verified
