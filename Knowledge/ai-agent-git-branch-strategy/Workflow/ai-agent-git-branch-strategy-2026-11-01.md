---
topic: ai-agent-git-branch-strategy
title_kr: "1인 개발 및 AI 에이전트 환경을 위한 3계층 Git 브랜치 관리 전략"
category: Workflow
sub_category: Git & DevOps
version: 2026-11-01
status: Experimental
created_date: 2026-11-01
last_modified: 2026-11-01
language: KR+EN
tags: [Git, Branch Strategy, AI Agent, Squash Merge, Semantic Versioning, WebTranslator]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: null
blog_published: false
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** ai-agent-git-branch-strategy
* **Title_KR:** 1인 개발 및 AI 에이전트 환경을 위한 3계층 Git 브랜치 관리 전략
* **Category:** Workflow
* **Sub-Category:** Git & DevOps
* **Version:** 2026-11-01
* **Status:** Experimental
* **Date:** 2026-11-01
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] 공식문서: Git - Branching Workflows & Tagging (https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows, https://git-scm.com/book/en/v2/Git-Basics-Tagging)
  * [★★★★★] 공식문서: GitHub Docs - About Pull Request Merges (Squash and Merge) & Managing Releases (https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges)
  * [★★★★★] 공식문서: Semantic Versioning 2.0.0 (https://semver.org/lang/ko/)
  * [★★★★☆] 기술 미디어: Microsoft Learn - Adopt a Git branching strategy (https://learn.microsoft.com/en-us/devops/develop/git/git-branching-guidance)
  * [★★★★★] 로컬 프로젝트 실증 데이터: `WebTranslator` Git 커밋 히스토리 (`main` `0237429`~`173d9e4` 및 `v2.0-dev` 48개 커밋), `.agents/AGENTS.md`, `scripts/git-sync.ps1`

---

#### 🔑 Core Concepts (핵심 개념)
  * **3계층 브랜치 아키텍처 (3-Tier Branching Model for Solo + AI Development):** 1인 개발자가 AI 코딩 에이전트(Antigravity, Codex 등)에 구현 및 R&D를 위임하고 버전별로 배포하는 환경에 맞춰, 브랜치를 ① `main` (불변 배포선), ② `v{Major}.{Minor}-dev` (차기 버전 통합선), ③ `feat/*`, `fix/*`, `exp/*` (단위 작업 및 AI 실험 격리선)의 3계층으로 분리하는 구조다. [OPINION]
  * **Annotated Git Tag (`git tag -a`):** 단순 커밋 포인터인 Lightweight 태그와 달리, 태거(Tagger) 정보, 생성 시각, 릴리즈 메시지를 Git 데이터베이스 내 독립 객체로 저장하는 영구 스냅샷 표식이다. 스토어 배포나 패키지 릴리즈 시점의 소스 트리 상태를 보존하고, 향후 이전 버전 긴급 패치(`hotfix/*`)의 분기 기준점으로 사용한다. [FACT]
  * **실험 브랜치(`exp/*`)와 Squash Merge (`git merge --squash`):** R&D나 AI 에이전트 실험 중 발생한 다수의 시행착오 커밋(`wip`, `debug`, `revert`)을 통합 브랜치(`v{Major}.{Minor}-dev`)에 그대로 병합하지 않고, 검증된 최종 결과만 단일 커밋으로 압축하여 병합하는 방식이다. 병합 후 원본 `exp/*` 브랜치는 새 커밋 SHA와의 공통 조상 연결이 끊어지므로 충돌 방지를 위해 즉시 삭제해야 한다. [FACT]
  * **No-Fast-Forward Merge (`git merge --no-ff`):** 버전 통합 브랜치(`v2.0-dev`)를 `main`에 병합할 때 Fast-Forward가 가능하더라도 명시적인 병합 커밋(Merge Commit)을 생성하여, `main` 브랜치 로그만으로도 버전 단위 배포 경계를 식별할 수 있게 하는 방식이다. [FACT]

---

#### 🛠️ Procedures (절차)
  1. **Tier 1 (`main`) 배포 전용 보호 및 태깅 절차:** [★★★★★]
     * `main` 브랜치에서는 직접 코드 수정이나 일반 커밋을 수행하지 않는다.
     * 버전 배포가 확정되면 `v{Major}.{Minor}-dev`에서 버전 메타데이터(`manifest.json`, `package.json`, `CHANGELOG.md`)를 최종 확인한 뒤 `main`으로 체크아웃한다.
     * `git merge --no-ff v{Major}.{Minor}-dev -m "release: v{Major}.{Minor}.{Patch}"` 명령으로 병합 커밋을 생성한다.
     * `git tag -a v{Major}.{Minor}.{Patch} -m "Release v{Major}.{Minor}.{Patch}"`로 Annotated Tag를 생성하고 `git push origin main --tags`로 원격 저장소에 반영한다.
  2. **Tier 2 (`v{Major}.{Minor}-dev`) 버전 통합 및 핫픽스 운용 절차:** [★★★★☆]
     * 차기 배포 버전(예: `v2.0-dev`)은 검증이 끝난 기능과 버그 수정만 통합하는 기준선으로 유지한다.
     * 이미 배포된 이전 버전(예: `v1.0.0`)에 긴급 수정이 필요하면 `git checkout -b hotfix/v1.0.1-{이슈명} v1.0.0`으로 태그에서 분기하여 수정한 뒤, `main`에 병합(`v1.0.1` 태그 생성)하고 현재 개발 중인 `v2.0-dev`에도 `git cherry-pick` 또는 병합으로 반영한다.
  3. **Tier 3 (`feat/*`, `fix/*`, `exp/*`) AI 작업/실험 격리 및 Squash Merge 절차:** [★★★★★]
     * AI 에이전트에게 신규 기능이나 R&D 실험을 지시하기 전 `v{Major}.{Minor}-dev`에서 `feat/{기능명}`, `fix/{이슈명}`, 또는 `exp/{실험명}` 브랜치를 분기한다.
     * 실험 성공 시:
       ```powershell
       git checkout v2.0-dev
       git merge --squash exp/{실험명}
       git commit -m "feat(scope): 검증 완료된 실험 기능 요약"
       git push origin v2.0-dev
       git branch -D exp/{실험명}
       ```
     * 실험 실패/폐기 시: `v2.0-dev`에 `revert` 커밋을 남기지 않고 `git checkout v2.0-dev` 복귀 후 `git branch -D exp/{실험명}`으로 폐기한다. (코드 보관이 필요한 경우 `git tag -a archive/exp-{실험명} exp/{실험명}` 생성 후 브랜치 삭제)
  4. **AI 에이전트 규칙(`AGENTS.md`) 및 자동 동기화 스크립트(`git-sync.ps1`) 가드레일 구성:** [★★★★★]
     * 버전 필드(`manifest.json`의 `version`)만 읽고 브랜치를 강제 전환(`git checkout`)하는 로직을 제거하고, 현재 체크아웃된 브랜치(`git branch --show-current`)를 유지한다.
     * 현재 브랜치가 `main`인 경우 스크립트가 즉시 종료(`exit 1`)되도록 차단 조건(Guard)을 둔다.
     * 문서 수정(`docs:`)과 소스 코드(`src/`, `manifest.json`) 변경이 한 커밋에 섞이지 않도록 커밋 전 `git status --short` 검증을 의무화한다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] 문서 수정 커밋(`1b68205`) 중 `main` 브랜치에 차기 버전(`v2.0.0`) 미완성 코드 유입 및 `v1.0.0` 배포 시점 유실 위험**
    * 원인: `WebTranslator` `v1.0.0` 정식 배포 시점(`0237429`)에 Git Tag를 생성하지 않았고, 워킹 디렉토리에 `v2.0.0` 이미지 번역 코드(`src/content/image/*`, `manifest.json` 버전 `2.0.0`)가 수정된 상태에서 `docs: README 및 프로젝트 명세서 추가...` 커밋(`1b68205`)을 전체 스테이징(`git add`)으로 수행하여 `main`에 미완성 코드가 함께 반영됨.
    * 해결법: 배포 시점 커밋(`0237429`)에 `v1.0.0` Annotated Tag를 부여하여 배포 스냅샷을 명시하고, `main` 브랜치 직접 커밋 차단 가드레일과 문서/코드 분리 스테이징 규칙을 적용한다. [OPINION]
    * 신뢰도: [★★★★★]
  * **[오류 2] `git-sync.ps1` 및 `AGENTS.md`의 버전 기반 강제 브랜치 라우팅으로 인한 작업 브랜치 격리 불가**
    * 원인: `scripts/git-sync.ps1`이 `manifest.json`의 `version`(`2.0.0`)을 파싱해 `$targetBranch = "v2.0-dev"`를 계산하고, 현재 브랜치가 다르면 무조건 `git checkout v2.0-dev`를 실행하도록 설계되어 별도의 기능/실험 브랜치(`feat/*`, `exp/*`)에서 동기화 스크립트를 사용할 수 없었음.
    * 해결법: 버전 문자열 기반 자동 브랜치 전환 로직을 제거하고, 현재 활성화된 브랜치를 그대로 푸시하되 `main` 브랜치만 차단하도록 스크립트와 에이전트 규칙을 수정한다. [OPINION]
    * 신뢰도: [★★★★★]
  * **[오류 3] `v2.0-dev` 단일 브랜치에 48개의 R&D·실험·롤백 커밋이 직렬 누적되어 히스토리 추적 및 부분 롤백 곤란**
    * 원인: OCR 엔진 교체(Tesseract → PaddleOCR → PP-OCRv6 WASM), 스프라이트 시트 최적화, 에이전트(Codex 등) 코드 롤백(`506c416 revert(image): roll back all Codex changes`, `0b74cb6 Revert "feat(imageService): 2-pass A-plan..."`, `0e3fa4e revert(image): remove diagnostic page`) 등을 별도 `exp/*` 브랜치로 격리하지 않고 `v2.0-dev`에서 직접 수행함.
    * 해결법: 모든 R&D 및 불확실한 AI 에이전트 작업은 `exp/*` 브랜치에서 수행한 뒤, 채택된 실험만 `v2.0-dev`에 Squash Merge하고 실패한 실험은 브랜치째 폐기하여 통합 브랜치의 커밋 히스토리를 기능 단위로 유지한다. [OPINION]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] 1인 개발 환경에서는 다수 인원 협업을 전제로 한 무거운 Git Flow 대신, AI 에이전트에게 작업을 시키는 단위(`feat/*`, `fix/*`, `exp/*`)와 버전별 배포 히스토리(`vX.Y-dev` + `main` 태그)를 명확히 분리하는 경량 3계층 구조가 실용적이다. — 출처: 사용자 실제 개발 경험 (`WebTranslator`)
  * [OPINION] AI 코딩 에이전트(Antigravity, Codex 등)는 단일 요청에서도 다수의 파일을 동시에 수정하고 자동 커밋 스크립트를 호출하므로, 에이전트 규칙(`AGENTS.md`)과 동기화 스크립트(`git-sync.ps1`)에 잘못된 브랜치 라우팅이나 무조건적인 `git add -A`가 들어가면 브랜치 오염이 빠르게 누적된다. — 출처: 사용자 실제 개발 경험 (`WebTranslator`)

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 새롭게 수립한 표준 문서(`D:\Noogs\NextCloud\Projects\BRANCH_STRATEGY.md`)의 3계층 구조(`main` + `vX.Y-dev` + `feat/fix/exp`)와 `git-sync.ps1` 가드레일 규격이 의도하신 평소 브랜치 관리 기준에 부합하는지 확인해주세요.
  * [ ] 블로그 포스팅 작성 시 실패한 실험 브랜치 보관 방법으로 '즉시 삭제(`git branch -D`)'를 기본으로 두되, 필요 시 '아카이브 태그(`archive/exp-*`) 생성 후 브랜치 삭제' 팁을 함께 소개할지 확인해주세요.

---

#### 🏷️ Tags
Git, Branch Strategy, AI Agent, Squash Merge, Semantic Versioning, WebTranslator

===== KNOWLEDGE PACKAGE END =====
