---
topic: ue58-map-consolidation-reference-fixup-and-zip-packaging
title_kr: "Unreal Engine 5.8 맵 경로 통합·참조 복구, 단일 프로젝트 ZIP 배포 및 Smart App Control 차단 기록"
category: Troubleshooting
sub_category: Asset-References-Packaging-Windows-Security
version: "5.8"
status: Experimental
created_date: 2026-11-12
last_modified: 2026-11-12
language: KR+EN
tags: [UnrealEngine, UE5.8, AssetRedirectors, Sequencer, ProjectPackaging, SmartAppControl, Windows11]
sources_count: 7
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** ue58-map-consolidation-reference-fixup-and-zip-packaging
* **Title_KR:** Unreal Engine 5.8 맵 경로 통합·참조 복구, 단일 프로젝트 ZIP 배포 및 Smart App Control 차단 기록
* **Category:** Troubleshooting
* **Sub-Category:** Asset-References-Packaging-Windows-Security
* **Version:** 5.8
* **Status:** Experimental
* **Date:** 2026-11-12
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Epic Games 공식 문서 - Asset Redirectors & ResavePackages: https://dev.epicgames.com/documentation/en-us/unreal-engine/asset-redirectors-in-unreal-engine
  * [★★★★★] Epic Games 공식 문서 - Sequencer Object Binding & Camera Cuts: https://dev.epicgames.com/documentation/en-us/unreal-engine/sequencer-overview-for-unreal-engine
  * [★★★★★] Epic Games 공식 문서 - Unreal Engine Directory Structure (Saved/Intermediate/DerivedDataCache/Binaries)
  * [★★★★★] Microsoft 공식 지원 문서 - Smart App Control FAQ: https://support.microsoft.com/en-us/windows/security/threat-malware-protection/smart-app-control-frequently-asked-questions
  * [★★★★★] Microsoft Learn 공식 문서 - App Control Debugging & Event 3077: https://learn.microsoft.com/en-us/windows/security/application-security/application-control/app-control-for-business/operations/appcontrol-debugging-and-troubleshooting
  * [★★★★★] 로컬 의존성·구경로·위치 통합·배포 검증 JSON (`evidence/input-fix_final-dependencies.json`, `evidence/input-fix_binary-old-paths.json`, `evidence/input-fix_project-location.json`, `evidence/input-fix_archive-validation.json`, `evidence/input-fix_pcg_result.json`)
  * [★★★★★] 로컬 작업 취합 기록 (`00_전달자료.md`, `01_검증과자료목록.md`, `conversation-index.json`)

---

#### 🔑 Core Concepts (핵심 개념)
  * **Unreal Engine 경로 기반 에셋 참조와 Redirector 문제 [FACT - 공식 문서]:**
    * Unreal Engine은 GUID 단독이 아니라 **경로 기반(Path-based) 참조**를 사용하므로, 에셋이나 맵을 이동·이름 변경하면 구경로에 작은 **Redirector** 파일이 남거나 미로드 패키지의 참조가 구경로를 가리킬 수 있다.
    * `Fix Up Redirectors in Folder` 또는 `ResavePackages -fixupredirects`를 통해 참조를 새 경로로 다시 저장해야 쿠킹·패키징 및 배포 시 누락 경고를 막을 수 있다.
  * **단일 프로젝트 구조 통합: `Guided`/`Lessons` 제거 및 28개 맵·289개 패키지 정리 [FACT - 로컬 검증 JSON]:**
    * 기존 `Workspace`와 `Environment`에 혼재하던 `Guided`/`Lessons` 중복 폴더 구조를 각 트랙의 `Maps` 직하위로 통합했다:
      * `Content/_Bootcamp/01_Environment/Maps/` (13개: `L_Env_00_Empty`~`08_Complete` + `05B`~`05E`)
      * `Content/_Bootcamp/02_Workspace/Maps/` (9개: `L_Work_00_Empty`~`08_Complete`)
      * `Content/_Bootcamp/03_MotionDesign/Maps/` (6개: `L_Motion_00_Empty`~`05_Complete`)
    * `input-fix_final-dependencies.json` 검사 결과: **총 28개 맵(및 `BP_WorkGameMode` 루트), 의존 패키지 289개, 누락(`missing`) 0개** 확인.
    * `input-fix_binary-old-paths.json`에서 검사 대상의 `Guided`/`Lessons` 옛 경로 문자열 잔존 건수가 `[]`(0건)임을 확인.
  * **캐시 폴더 제외 원칙과 `Binaries` 제외 조건 [FACT - 공식 문서 및 로컬 검증 JSON]:**
    * `Saved/`, `Intermediate/`, `DerivedDataCache/`는 엔진이 자동 재생성하는 로컬/캐시 디렉터리다.
    * **`Binaries/` 제외 조건부 주의:** 이 프로젝트에는 별도의 사용자 C++ 모듈이나 커스텀 플러그인 바이너리가 없음을 확인했기 때문에 `Binaries`를 제외했다. C++ 프로젝트에서는 `Binaries`를 무조건 제외하면 다음 실행 시 전체 재컴파일이 필요하므로 모든 Unreal 프로젝트에 대한 일반론으로 쓰지 않는다.
    * 최종 ZIP(`336개 파일`, `2,857,347,863`바이트 ≈ `2.86GB`)은 압축 전 파일 합계 `2,903,714,732`바이트 대비 약 **1.6% 크기 차이**만 난다. 용량 절감의 핵심은 압축 알고리즘 자체가 아니라 대용량 캐시·중복 복사본을 제외한 결과다.
  * **ElectricDreamsSample DLL Smart App Control 차단: 로컬 관찰 vs Microsoft 공식 정책 분리 [FACT - 로컬 로그 및 MS 공식 문서]:**
    * **당시 로컬 관찰(초기 Electric Dreams 샘플 열기 시도 당시):** `UnrealEditor-ElectricDreamsSample.dll` 로드 실패 시 Epic Launcher 원본 파일과 설치본의 SHA-256 해시가 일치했고 둘 다 디지털 서명 수 `0`이었으며, Windows `CodeIntegrity > Operational` 로그에 차단 이벤트 `3077`이 기록되었다. (검토용 Python 스크립트는 프로젝트 밖으로 옮겼고 `.uproject`도 Launcher 원본과 일치했으나 DLL 차단 자체는 계속 남음; 해당 DLL 삭제 시 필수 모듈 누락으로 실행 불가)
    * **Microsoft 공식 문서 정책:** Smart App Control(SAC)은 개별 파일·폴더 예외 추가나 Windows Defender 검사 제외 목록 우회를 지원하지 않는다. 개발자 측 근본 해법은 Microsoft Trusted Root Program에 연결되는 코드 서명 인증서로 모든 바이너리를 서명하는 것이며, 사용자 측 **Windows 보안 → 앱 및 브라우저 컨트롤 → 스마트 앱 컨트롤 설정 → 끄기**는 PC 전체에 적용되고 한 번 끄면 Windows 초기화/클린 설치 없이는 다시 켤 수 없다. (당시 작업에서 보안 설정을 변경해 해결했다고 서술하지 않는다.)

---

#### 🛠️ Procedures (절차)
  1. **맵 경로 통합 및 잔여 구파일·문자열 검사:** [★★★★★]
     * `Guided`/`Lessons` 하위의 22개(Env/Work) 맵을 각 `Maps/` 직하위로 복제·이동하고, MCP `move` 반환값만 믿지 않고 에디터 삭제 후 디스크에 남은 구파일과 구경로 문자열(`input-fix_binary-old-paths.json`)을 직접 점검한다.
  2. **PCG Data Asset 및 Sequencer 카메라 바인딩 참조 복구:** [★★★★★]
     * **`DA_ForestPatchSet` 복구:** 이전 `Lessons/05B`를 가리키던 참조를 끊고, 새 경로 `L_Env_05C_SetSource`에서 PCG Asset을 다시 추출한 뒤 새 `L_Env_05D_SetScatter`를 열어 `Generate`를 실행해 7종 ISM 참조(`input-fix_pcg_result.json`)를 확인한다.
     * **`LS_Environment_15s` 복구:** 시퀀스의 카메라 바인딩이 구맵 액터를 가리키지 않도록 새 `L_Env_08_Complete`의 `CineCameraActor`로 재할당하고 시퀀스를 명시적으로 저장한다.
     * 에디터를 다시 열어 전체 28개 맵 기준 의존 패키지 289개, `missing: []`를 확인한다.
  3. **단일 원본 프로젝트 유지 및 중복 백업 정리:** [★★★★★]
     * `Bootcamp_Docs/Bootcamp_BaseProject/Bootcamp_BaseProject.uproject` 단 1개만 유지하고, 작업 중 생성된 임시·백업 폴더의 중복 프로젝트 복사본은 파일 일치 확인 후 정리한다(`input-fix_project-location.json`, verifiedFiles 336).
  4. **에디터 배포용 ZIP 패키징 및 CRC·SHA-256 검증:** [★★★★★]
     * `.git`, `.vs`, `Binaries`, `DerivedDataCache`, `Intermediate`, `Saved`, `__pycache__`를 제외한 336개 파일(프로젝트 1개, 맵 28개, 최종 181장 PPTX, 가이드/CSV/JSON 자료)을 단일 최상위 폴더로 압축한다.
     * `crc_check: "pass"`, 복사본 SHA-256 일치(`3c7ff8ed3300b2c63a13588241ac48687c4202025e7ecc95675efcb403a50cc2`)를 확인한다. 실행 EXE 빌드가 아닌 UE 5.8 에디터용 프로젝트 배포물임을 명시한다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] 맵 이동 후 `DA_ForestPatchSet` 및 `LS_Environment_15s` 구경로 참조 잔존**
    * 원인: `Guided`/`Lessons` 폴더에서 `Maps/` 직하위로 맵을 통합하는 과정에서 단순 파일/MCP move 호출만으로는 PCG Data Asset 원본 참조(`Lessons/05B`)와 Sequencer Object Binding(`CineCameraActor`)이 자동으로 새 맵 액터를 가리키지 않았다.
    * 해결법: 새 `L_Env_05C_SetSource`에서 `DA_ForestPatchSet`을 재추출하고 `L_Env_05D_SetScatter`에서 재실행 검증했으며, `LS_Environment_15s`는 새 `L_Env_08_Complete`의 `CineCameraActor`로 바인딩을 교체·저장한 뒤 전체 의존성 검사(`289개 패키지, missing 0`)로 종결했다. [FACT - 로컬 검증 JSON]
    * 신뢰도: [★★★★★]
  * **[오류 2] `UnrealEditor-ElectricDreamsSample.dll` Windows Smart App Control 차단 (Event ID 3077)**
    * 원인: Epic Launcher가 배포한 샘플 프로젝트의 해당 DLL에 디지털 서명(`서명 수 0`)이 없어, Windows 11 Smart App Control 강제(Enforcement) 정책에서 CodeIntegrity 차단(Event 3077)이 발생했다. 검토용 Python 스크립트를 치우고 `.uproject`를 원본과 일치시켜도 원본 DLL 자체의 서명 부재 문제는 남았다.
    * 해결법: 강의 프로젝트(`Bootcamp_BaseProject`)는 샘플 프로젝트를 그대로 쓰지 않고 선별한 에셋과 자체 블루프린트(사용자 C++ DLL 없음)로 구성해 사용했다. Smart App Control 자체에 대해서는 개별 예외가 불가하다는 Microsoft 공식 정책을 명확히 구분해 기록한다. [FACT - 로컬 로그 및 MS 공식 문서]
    * 신뢰도: [★★★★★]
  * **[오류 3] 프로젝트 폴더 중복 누적 및 캐시로 인한 배포 용량 비대화**
    * 원인: 개정 단계별로 백업·작업 복사본 프로젝트가 여러 경로에 흩어지면 최신 수정본 혼선과 대용량 `Saved`/`Intermediate`/`DDC` 누적이 발생한다.
    * 해결법: 단일 원본 프로젝트 위치만 남기고 불필요한 중복 복사본을 정리(`input-fix_project-location.json`)한 뒤, 캐시 폴더를 제외한 336개 파일로 ZIP을 생성해 CRC 및 SHA-256을 검증했다. [FACT - 로컬 검증 JSON]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] 언리얼에서 폴더 구조를 정리할 때는 "에디터 move API가 성공했다"는 응답에서 멈추지 말고, ① 디스크 구폴더 잔존 파일 확인, ② 문자열/바이너리 구경로 스캔, ③ PCG Data Asset·Sequencer 바인딩 수동 재연결 및 저장, ④ 최종 의존성 스캔(`missing: 0`)까지 4단계로 확인하는 편이 안전하다. — 출처: 맵 통합 작업 경험 (`00_전달자료.md` §8)
  * [FACT] 공개 Git 저장소(`BlogDocs`/`Blog_Core-Archive`)에는 대용량 ZIP(2.86GB), PPTX(52.4MB), `.uproject`/`.uasset` 원본 및 구매·Fab 에셋을 업로드하지 않고 기술 지식과 검증 메타데이터만 관리한다. — 출처: `01_검증과자료목록.md`, `02_주제스킬_전달명령.txt`

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] **다른 PC의 깨끗한 환경에서 `Unreal_Bootcamp_최종배포.zip`만 압축 해제해 UE 5.8로 최초 실행(캐시 재생성, 플러그인 자동 로드, 28개 맵 오픈) 실전 검증**
  * [ ] 최초 실행 시 필요한 UE 5.8 엔진 플러그인 목록(PCG, Motion Design, ModelContextProtocol, AllToolsets 등)이 대상 강의실 PC에 모두 설치·활성화 가능한지 확인
  * [ ] `ElectricDreams`·`DetectiveOffice` 에셋 및 교재 스크린샷의 외부 공개·강의 배포 라이선스 범위 최종 확인

---

#### 🏷️ Tags
UnrealEngine, UE5.8, AssetRedirectors, Sequencer, ProjectPackaging, SmartAppControl, Windows11

===== KNOWLEDGE PACKAGE END =====
