---
topic: ue58-pcg-scatter
title_kr: "Unreal Engine 5.8 Electric Dreams 기반 지면 식생 PCG와 세트 데이터 재사용 확장"
category: Workflow
sub_category: Procedural-Content-Generation
version: "5.8"
status: Verified (Partial)
created_date: 2026-11-12
last_modified: 2026-11-14
language: KR+EN
tags: [UnrealEngine, UE5.8, PCG, ElectricDreams, ProceduralGeneration, Foliage, Megascans]
sources_count: 6
blog_draft_path: "Blog_Posts/2026-11/ue58-pcg-scatter.html"
blog_draft_date: 2026-11-14
blog_id: core-archive
blog_published: false
series_id: ue58-bootcamp
blogger_post_id: "2491154217835264349"
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** ue58-pcg-scatter
* **Title_KR:** Unreal Engine 5.8 Electric Dreams 기반 지면 식생 PCG와 세트 데이터 재사용 확장
* **Category:** Workflow
* **Sub-Category:** Procedural-Content-Generation
* **Version:** 5.8
* **Status:** Verified (Partial)
* **Date:** 2026-11-12
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Epic Games 공식 문서 - Procedural Content Generation Overview: https://dev.epicgames.com/documentation/en-us/unreal-engine/procedural-content-generation-overview-for-unreal-engine
  * [★★★★★] Epic Games 공식 문서 - Using PCG Generation Modes in Unreal Engine: https://dev.epicgames.com/documentation/en-us/unreal-engine/using-pcg-generation-modes-in-unreal-engine
  * [★★★★★] Epic Games 공식 문서 - Electric Dreams Environment in Unreal Engine: https://dev.epicgames.com/documentation/unreal-engine/electric-dreams-environment-in-unreal-engine
  * [★★★★★] Epic Games 공식 문서 - Create PCG Assets from Level 및 PCG 그래프 노드 (`Surface Sampler`, `Transform Points`, `Static Mesh Spawner`)
  * [★★★★★] 로컬 PCG 검증 JSON (`evidence/input-fix_pcg_result.json`, `evidence/pcg-revision_verification.json`)
  * [★★★★★] 로컬 작업 취합 및 에셋 목록 (`00_전달자료.md`, `01_검증과자료목록.md`, `evidence/input-fix_archive-validation.json`, `images/index.md`)

---

#### 🔑 Core Concepts (핵심 개념)
  * **Electric Dreams 샘플 구조와 강의 프로젝트 내 재구성 [FACT - 공식 문서 및 로컬 기록]:**
    * Epic 공식 `Electric Dreams Environment` 샘플은 `ElectricDreams_Env`(4km×4km 전체 월드), `PCGCloseRange`(근거리 소품·하층 식생·자갈 디테일), `PCGForest`(대규모 숲 생태계 PCG) 레벨로 구성되며 Nanite Foliage와 PCG 프레임워크 시연을 목적으로 한다.
    * 교재 프로젝트(`Bootcamp_BaseProject`)에서는 구매/배포 샘플을 그대로 촬영하지 않고, 선별한 Megascans 바위·절벽·쓰러진 통나무·고사리·잔디·나무 에셋을 `Content/_Bootcamp/90_Library/ElectricDreams/` 아래로 가져와 협곡 전경·중경·원경 구도, 지면 높이, 안개, 카메라 렌즈·위치를 빈 레벨부터 단계별로 새로 배치했다.
  * **PCG 그래프 기본 3단계 파이프라인 (`Surface Sampler` → `Transform Points` → `Static Mesh Spawner`) [FACT - 공식 문서]:**
    * `Surface Sampler`: 입력 표면(Landscape, Static Mesh 등) 위에서 `Density`와 `Point Extents` 기준으로 스폰 포인트(위치·바운드·밀도 속성)를 샘플링한다.
    * `Transform Points`: 포인트의 위치·회전(예: Z축 Yaw `0~360°`)·스케일(Min/Max)을 무작위 변형해 반복감을 줄인다.
    * `Static Mesh Spawner`: 가공된 포인트에 `Mesh Entries`로 등록한 다수 스태틱 메시를 가중치(Weight)에 따라 인스턴스(ISM)로 스폰한다.
  * **5B(`L_Env_05B_PCG`): 지면 대상 필터와 바위 노출·바닥 혼합 제어 [FACT - 로컬 실행 기록]:**
    * Actor Tag 및 지면 대상 필터링을 활용해 잔디와 고사리가 바위 표면 위까지 뒤덮지 않도록 제어하고, 바닥 중심으로 여러 종의 잔디 메시(`SM_KikuyuGrass_01/07/09`)와 고사리(`SM_BostonFern_01`)를 가중치에 따라 혼합 배치한다.
  * **5C·5D(`L_Env_05C_SetSource` → `DA_ForestPatchSet` → `L_Env_05D_SetScatter`): 레벨 세트 추출과 재사용 스캐터링 [FACT - 공식 문서 및 로컬 실행 기록]:**
    * `Create PCG Asset from Level` 기능은 레벨에 수동 배치한 스태틱 메시/ISM 구성을 재사용 가능한 **PCG Data Asset**으로 추출한다.
    * 교재에서는 작은 나무·바위·지면 식생 조합 원본 레벨(`L_Env_05C_SetSource`)에서 `DA_ForestPatchSet`을 추출한 뒤, `L_Env_05D_SetScatter`(`PCG_ForestSetScatter`)에서 이 세트 전체를 더 넓은 구역에 회전·스케일·Seed 값(예: Seed2407)을 바꿔 반복 배치하는 구조를 분리해 설명한다.
  * **5E(`L_Env_05E_Surroundings` / `PCG_ForestSurroundings`): 주변 대형 요소 보강 [FACT - 로컬 실행 기록]:**
    * 풀만으로 주변 빈 공간을 채울 때의 한계를 보완하기 위해 바위(`SM_MossyForestBoulder_03`), 절벽(`SM_HugeNordicCoastalCliff_01`), 나무(`SM_EuropeanHornbeam_Forest_02`) 등 대형 요소를 배치하는 PCG 단계를 별도 구성했다. 단, 이는 시각적 환경 구성을 위한 단계이며 모든 프로젝트에 통용되는 성능 개선이나 최적화 벤치마크가 아니다.

---

#### 🛠️ Procedures (절차)
  1. **자연환경 협곡 기반 단계 구축 (`01_Landscape` → `05_Foliage`):** [★★★★★]
     * `L_Env_00_Empty`부터 지형(`01_Landscape`), 큰 바위(`02_Rocks`), 근경 바닥 디테일(`03_GroundDetail`), 주요 나무(`04_Trees`), 수동 Foliage(`05_Foliage` / `FT_Fern`) 순으로 기초 씬을 쌓는다.
  2. **5B 바닥 식생 PCG 구성 (`PCG_ForestFloorSet` / `PCG_FernPatch`):** [★★★★★]
     * 지면 액터에만 반응하도록 Actor Tag·대상 필터를 지정한 뒤 `Surface Sampler` → `Transform Points` → `Static Mesh Spawner`를 연결하고, 바위 표면은 노출된 채 바닥 위주로 잔디와 고사리가 섞이도록 밀도와 가중치를 조정한다.
  3. **5C 세트 원본 제작 및 PCG Data Asset 추출 (`DA_ForestPatchSet`):** [★★★★★]
     * `L_Env_05C_SetSource` 레벨에 작은 나무·바위·지면 식생을 하나의 조합으로 배치하고, 에디터의 `Create PCG Asset from Level` 흐름으로 `DA_ForestPatchSet.uasset`을 생성한다.
  4. **5D·5E 넓은 영역 세트 스캐터링 및 대형 요소 확장:** [★★★★★]
     * `L_Env_05D_SetScatter`에서 `DA_ForestPatchSet`을 입력으로 받아 회전·스케일·Seed 변경을 적용한 뒤 `Generate`를 실행한다.
     * `input-fix_pcg_result.json` 기준 최종 `Maps` 경로로 통합된 뒤 `PCGVolume_0`에서 7종 ISM 컴포넌트(`SM_BostonFern_01`, `SM_EuropeanHornbeam_Forest_02`, `SM_KikuyuGrass_01`, `SM_KikuyuGrass_07`, `SM_KikuyuGrass_09`, `SM_MossyForestBoulder_03`, `SM_RockyForestPath_01`) 참조가 확인되었다.
     * 이어 `L_Env_05E_Surroundings`와 `06_Lighting` → `07_Camera` → `08_Complete`로 조명·안개·시네마 카메라를 마무리한다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] 초기 PCG 5B 구간에서 풀만 성기게 보이거나 바위까지 잔디로 덮이는 현상**
    * 원인: 초기 구성에서 PCG 대상 표면 필터링과 지면/바위 구분, 식생 가중치 조합이 충분하지 않아 바위가 식생에 가리거나 바닥 식생이 자연스럽게 어우러지지 않았다.
    * 해결법: Actor Tag와 지면 대상 필터로 바위 표면을 제외·노출시키고, 바닥 중심으로 여러 잔디 메시와 고사리를 혼합하도록 그래프(`pcg-floor-graph.png`)와 캡처를 수정했다. [OPINION/LOCAL OBSERVATION]
    * 신뢰도: [★★★★★]
  * **[오류 2] 중간 버전 `Lessons` 경로의 PCG 재생성 검증 기록과 최종 경로의 혼동**
    * 원인: `pcg-revision_verification.json`은 `Lessons/` 하위 경로 시절(`05B/06/07/08` 5,932개, `05C` 168개, `05D` 3,268개, `deterministic: true`)의 재생성 digest 일치 기록이며, 이후 맵 폴더가 `Maps/` 직하위로 통합되면서 `DA_ForestPatchSet`이 이전 `Lessons/05B`를 가리키는 참조 오류가 발생했다.
    * 해결법: 새 `L_Env_05C_SetSource`에서 `DA_ForestPatchSet`을 다시 추출한 뒤 최종 `L_Env_05D_SetScatter`를 열어 `Generate`를 재실행하고 7종 ISM 참조(`input-fix_pcg_result.json`)를 별도 검증했다. [FACT - 로컬 검증 JSON]
    * 신뢰도: [★★★★★]
  * **[오류 3] `05E` 주변 대형 요소 보강을 범용 최적화 성과로 오인할 위험**
    * 원인: `5E` 단계가 대형 씬 밀도를 높이는 과정이다 보니, 이를 프레임 향상이나 대규모 오픈월드 성능 벤치마크 수치로 과대 해석할 우려가 있다.
    * 해결법: `5E`는 빈 주변 공간을 바위·절벽·나무 PCG로 채우는 레벨 구성 및 교재 단계 설명으로만 기록하고, 프레임레이트·드로우콜·메모리 성능 측정은 수행하지 않았음을 명시한다. [FACT - 검증 경계]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] 수강생 입장에서 "단일 풀/고사리 포인트 샘플링(5B)"과 "수동으로 잘 짜둔 작은 식생 세트 전체를 Data Asset으로 묶어 재사용 배치(5C→5D)" 두 가지 PCG 활용 패턴을 분리해 익히면 구조 파악에 도움이 된다. — 출처: 교재 설계 및 취합 기록 (`00_전달자료.md`)
  * [FACT] 중간 버전(`pcg-revision_verification.json`)에서 동일 Seed 기준 `Generate` 실행 전후 포인트 개수와 SHA-256 digest가 일치(`deterministic: true`)함을 확인했으나, 경로 이동 후에는 반드시 새 경로에서 Data Asset 재생성 및 `Generate` 재확인이 필요하다. — 출처: `pcg-revision_verification.json`, `input-fix_pcg_result.json`

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 최종 `Maps/` 직하위 통합 이후 `L_Env_05B_PCG`~`L_Env_08_Complete` 전체 맵에 대한 결정적 재생성(`digest` 비교) 재측정 여부
  * [ ] 대상 PC 사양별 GPU/CPU 프레임타임 및 Nanite Foliage 활성 여부에 따른 렌더링 부하 실측 (본 기록은 배치·참조 중심이며 성능 프로파일링 미수행)
  * [ ] `Electric Dreams` Fab 에셋의 외부 블로그/강의 재배포 범위와 스크린샷 공개 라이선스 검토

---

#### 🏷️ Tags
UnrealEngine, UE5.8, PCG, ElectricDreams, ProceduralGeneration, Foliage, Megascans

===== KNOWLEDGE PACKAGE END =====
