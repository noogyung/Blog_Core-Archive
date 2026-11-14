---
topic: ue58-curriculum-maps-ppt
title_kr: "실제 Unreal Editor 5.8 화면과 단계별 체크포인트 맵 기반 실습 교재 재구성"
category: Workflow
sub_category: Curriculum-And-Documentation-Design
version: "5.8"
status: Verified (Partial)
created_date: 2026-11-12
last_modified: 2026-11-15
language: KR+EN
tags: [Unreal, Curriculum, LevelDesign, UnrealEngine, UE5.8, CurriculumDesign, PPTX, StepByStepMaps, TechnicalDocumentation]
sources_count: 7
blog_draft_path: "Blog_Posts/2026-11/ue58-curriculum-maps-ppt.html"
blog_draft_date: 2026-11-14
blog_id: core-archive
blog_published: false
series_id: ue58-bootcamp
blogger_post_id: "8067650772327832436"
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** ue58-curriculum-maps-ppt
* **Title_KR:** 실제 Unreal Editor 5.8 화면과 단계별 체크포인트 맵 기반 실습 교재 재구성
* **Category:** Workflow
* **Sub-Category:** Curriculum-And-Documentation-Design
* **Version:** 5.8
* **Status:** Verified (Partial)
* **Date:** 2026-11-12
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Epic Games 공식 문서 - Unreal Engine Directory Structure: https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-directory-structure
  * [★★★★★] Epic Games 공식 문서 - Electric Dreams Environment in Unreal Engine: https://dev.epicgames.com/documentation/unreal-engine/electric-dreams-environment-in-unreal-engine
  * [★★★★★] Epic Games 공식 문서 - Unreal MCP in Unreal Editor (UE 5.8): https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor?lang=ko
  * [★★★★★] 로컬 작업 취합 및 검증 기록 (`00_전달자료.md`, `01_검증과자료목록.md`, `conversation-index.json`, `evidence/교재_검증_기록.md`)
  * [★★★★★] 최종 PPTX 구조·레이아웃·재입력 검증 JSON (`evidence/input-fix_ppt-final-validation.json`, `evidence/ui-course_import-verification.json`)
  * [★★★★★] 최종 프로젝트 의존성·배포 검증 JSON (`evidence/input-fix_final-dependencies.json`, `evidence/input-fix_archive-validation.json`)
  * [★★★★☆] 참고 이미지 인덱스 (`images/index.md`, `01-editor-empty.png` 등 실제 에디터 캡처 18종)

---

#### 🔑 Core Concepts (핵심 개념)
  * **정방향(0 → 완성) 체크포인트 실습 구조와 "완성 구도 먼저 검토" 제작 순서 [OPINION - 사용자 요구 및 제작 방식]:**
    * 완성된 구매 샘플을 촬영하는 방식만으로는 입문자가 빈 레벨에서 결과를 재현할 수 없다.
    * 제작 시에는 먼저 완성 구도를 만들어 품질을 검토한 뒤 교육용 단계별 요소를 분리하지만, 수강생의 진행 방향은 반드시 `00_Empty`(빈 레벨)에서 출발해 `Complete`(완성)로 나아가는 정방향으로 구성한다.
  * **초기·중간·최종 버전 수치 엄격 분리 원칙 [FACT - 로컬 실행 및 검증 기록]:**
    * 초기 기획(`ue58-curriculum-planning`) 단계의 `python-pptx` 텍스트 초안과 8교시 개념 설계에서 출발해, 교재와 프로젝트는 사용자 피드백에 따라 여러 차례 내용을 교체·통합했으며 최종 장수인 181장보다 많은 182장 중간판도 존재한다. 슬라이드 장수 증가 자체가 품질 향상을 뜻하지 않으므로 단계별 수치를 섞지 않는다.

| 개정 단계 | 핵심 변경 내용 | PPT 장수 | 맵 수 | 패키지 수 | 상태 구분 |
|---|---|---:|---:|---:|---|
| 최초 교재 정리 | 기존 교재 정리, 실습 프로젝트·체크포인트 별도 준비 상태 | 61장 | - | - | 초기본 |
| 첫 씬 개정 | Electric Dreams 협곡, Detective Office 방(약 6.3×4.9m), SignalGrid 플립 | 67장 | 16개 | - | 초기 개정판 |
| 단계별 재구성 | 빈 레벨부터 단계별 추가, 에셋 선별, 배치 값·가이드 | 71장 | 25개 | 266개 | 중간판 |
| UI 상세 교육 | 실제 UE 화면에 번호·설명, 버튼/노드/입력칸 확대 | 145장 | - | - | 중간판 |
| 식생 PCG 확장 | 바닥 중심 잔디·고사리, 바위 노출, 세트 추출·확장 | 165장 | - | - | 중간판 |
| 모션·MCP 개정 | 1,200개 타일 360° 파동, 공식 MCP 등록·호출 | 182장 | - | - | 중간판 (최종보다 많음) |
| 환경·작업실 재배치 | 대형 요소 PCG, 방 축소, 광원/UV/F 토글 | 179장 | - | - | 직전 중간판 |
| **입력·경로 최종판** | **WASD/MouseY/캡슐/보행 수정, Maps 통합 및 참조 복구** | **181장** | **28개** | **289개** | **최종본** |

  * **실제 에디터 UI 중심 상세 설명 원칙 [OPINION - 사용자 요구]:**
    * 슬라이드 수를 줄이기 위해 클릭 위치나 속성을 생략하지 않고, 실제 Unreal Editor 화면 위에 번호를 매겨 메뉴·버튼·입력칸·노드 핀·배치 에셋·설정 값·실행 결과를 1:1로 대응시킨다.
    * 시퀀스나 동작을 추가할 때마다 별도 동영상을 제작하지 않고 실제 뷰포트 스틸컷을 사용한다.
  * **입문 이론 복원과 4파트 커리큘럼 구성 [FACT - 로컬 취합 기록]:**
    * 기존 126장 참고 교재에서 엔진 정의·활용 사례·템플릿/샘플·Blueprint·Launcher/Fab·물리 기반 렌더링 등 입문 파트를 복원해 **언리얼 입문 → 자연환경(13개 맵) → 작업실과 인터랙션(9개 맵) → 모션 그래픽(6개 맵)** 순서로 28개 실습 맵을 편성했다.
  * **PPT 자동화 검증 범위와 미검증 경계 구분 [FACT - 로컬 검증 JSON]:**
    * `input-fix_ppt-final-validation.json` 기준 최종 PPTX(`52,447,202`바이트, SHA-256 `c6246e414224ab25c21f9c28432a56ff69548360ac2a33a28ffa866f90e56873`)는 181장, 관계 파트 378개, `packageIntegrity` 및 `presentationLayout` finding 0개, `맑은 고딕` 3,378개 텍스트 스크립트 글꼴 정책 통과, 네이티브 표 22개 산술 검사 finding 0개로 확인되었다.
    * `ui-course_import-verification.json`에서는 6장씩 30개 배치 + 1장(총 31배치 = 181장) 전체 재입력 검사가 `ok: true`로 기록되었다.
    * 단, `claim_boundary`와 `native_powerpoint_verified: false`가 명시하듯 **슬라이드 텍스트/노트 의미(`notes_parts_skipped: 181`), PowerPoint 데스크톱 앱에서의 네이티브 슬라이드쇼 및 실제 글꼴 렌더링은 검사 범위 밖**이다.

---

#### 🛠️ Procedures (절차)
  1. **교재 범위 및 커리큘럼 백본 수립:** [★★★★★]
     * 기존 교재의 입문 개념(엔진 구조, 뷰포트 조작, View Mode, Content Browser, 레벨/액터/에셋, PBR)을 앞단에 배치하고, 3개 실습 트랙(`01_Environment`, `02_Workspace`, `03_MotionDesign`)의 최종 완성 목표를 먼저 확정한 뒤 `00_Empty`부터 단계별 체크포인트 맵 28개를 설계한다.
  2. **강의 프로젝트 내부 독립 재구축:** [★★★★★]
     * Fab 샘플(Electric Dreams, Detective Office)을 원본 프로젝트 채로 촬영하지 않고, 선별한 에셋을 단일 강의 프로젝트(`Bootcamp_BaseProject`)의 `Content/_Bootcamp/90_Library/` 아래로 정리해 빈 씬에서 직접 배치·재현할 수 있게 구성한다.
  3. **UI 번호 매핑 및 부속 보조 자료 동반 생성:** [★★★★★]
     * 실제 에디터 화면 캡처에 UI 번호를 부여해 File/New Level/Save, Landscape, Foliage, PCG, Blueprint Components/Timeline/Collision, Material Instance, 광원/노출, Sequencer, Cloner/Effector, Character 입력, F 토글, MCP 실행 화면을 설명한다.
     * 강사용·수강생용 보조 자료(`강사_진행표.md`, `단계별_제작_가이드.html`, `검증_기록.md`, `Asset_Catalog.csv`, `Placements_AllStages.csv`, `Placements_Foliage.csv`, `Materials_Assignments.csv`, MCP 요청/응답 JSON 예시, `README.md`)를 함께 패키징한다.
  4. **최종 PPTX 구조·레이아웃 검증 및 단일 배포 동기화:** [★★★★★]
     * 씬이나 맵 경로를 최종 수정한 뒤에는 PPTX 슬라이드 텍스트·스크린샷·가이드 HTML·ZIP 배포본을 함께 갱신하고, 파일명 혼동을 피하기 위해 SHA-256 해시로 최종본을 확정한다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] 구매 샘플 원본 촬영 및 단순 배치로 인한 실습 재현성 부족**
    * 원인: 초기 안에서는 완성 샘플 레벨이나 단순 배치 구도 중심으로 캡처하여, 수강생이 빈 레벨에서 어떤 UI를 눌러 어떤 에셋과 수치로 장면을 만드는지 따라가기 어려웠다.
    * 해결법: 완성 구도를 먼저 검증한 뒤 `Bootcamp_BaseProject` 내부에서 에셋을 직접 재배치하고, `00_Empty`부터 `Complete`까지 단계별 맵과 UI 번호 캡처·배치 CSV를 제공하도록 재설계했다. [OPINION - 사용자 피드백 기반 개선]
    * 신뢰도: [★★★★★]
  * **[오류 2] 슬라이드 138 텍스트 프레임 미세 겹침 경고 및 파일명 혼동 위험**
    * 원인: `input-fix_ppt-final-validation.json` 검사 결과 슬라이드 138의 객체 ID 6과 12 사이에 `substantial_text_frame_overlap` 경고 1건(너비 `0.2357in`, 높이 `0.1279in`)이 감지되었으며, 개정 과정에서 로컬 PPTX 파일명이 여러 차례 바뀌었다.
    * 해결법: 파일명만으로 최신본을 판별하지 않고 SHA-256(`c6246e414224ab25c21f9c28432a56ff69548360ac2a33a28ffa866f90e56873`)으로 최종 PPTX를 대조했으며, 슬라이드 138 겹침 경고 1건은 검증 기록에 남기고 실제 PowerPoint 화면 확인 항목으로 분리했다. [FACT - 로컬 검증 JSON]
    * 신뢰도: [★★★★★]
  * **[오류 3] `evidence/교재_검증_기록.md` 등 중간 시점 기록의 잔존 문구 혼선**
    * 원인: 개정 중 작성된 `교재_검증_기록.md` 상단 본문에는 직전 버전인 179장 시점 문구가 남아 있고, 중간 버전에는 182장/165장/71장/25맵/266패키지 수치가 존재한다.
    * 해결법: 최종 판정 시 `input-fix_ppt-final-validation.json`(181장), `input-fix_final-dependencies.json`(28맵, 289패키지), `input-fix_archive-validation.json`(336파일)을 최우선 기준 근거로 구분해 사용한다. [FACT - 로컬 검증 JSON]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] 사용자는 초기 67장/71장 단계의 단순 설명에 만족하지 않고, UI 버튼·입력칸·노드 핀까지 번호로 짚어주는 145장 이후의 상세 구성을 요구했다. 장수 자체가 목적이 아니라, 실제 에디터 조작 재현 가능 여부가 기준이었다. — 출처: 사용자 피드백 취합 (`00_전달자료.md`)
  * [OPINION] 초기 완료 보고(67장, 71장, 179장)를 최종 사용성 검증으로 인용하면 후속으로 발견된 입력 오류·참조 문제와 충돌하므로, 단계별 이력은 개정 맥락으로만 구분해 기록하는 편이 정확하다. — 출처: 작업 취합 기록 (`00_전달자료.md`)
  * [FACT] 최종 ZIP 내부 최종 배포용 PPTX 파일의 `slide_size_in`은 `13.3333 × 7.5`(16:9)이다. — 출처: `input-fix_archive-validation.json`, `input-fix_ppt-final-validation.json`

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] Microsoft PowerPoint 데스크톱 앱에서 181장 전체 슬라이드쇼 실행, 애니메이션 전환, `맑은 고딕` 네이티브 렌더링 실제 육안 검증 (`native_powerpoint_verified: false`)
  * [ ] 181개 슬라이드의 발표자 노트(`notes_parts_skipped: 181`) 문맥·정확성 전수 검토
  * [ ] 슬라이드 138의 객체 ID 6–12 겹침 경고(`0.2357 × 0.1279in`)가 실제 PowerPoint 화면에서 가독성에 영향을 주는지 확인
  * [ ] 후속 블로그 작성 시 이 기록을 단일 글로 다룰지, 주제별로 나누어 연재할지 구성 방식 확정 필요

---

#### 🏷️ Tags
Unreal, Curriculum, LevelDesign, UnrealEngine, UE5.8, CurriculumDesign, PPTX, StepByStepMaps, TechnicalDocumentation

---
## 📝 Feedback History

### 2026-11-15 (라벨 통일) — Test Result: PARTIAL
* **환경:** 시리즈 메타데이터 및 Blogger 초안 라벨 검수 (`Blog_Core-Archive`)
* **검증된 단계:** HTML `Labels (Tags)` 및 Knowledge `tags` 동기화 완료 (`Unreal, Curriculum, LevelDesign`)
* **수정된 내용:** 시리즈 공통 라벨 `Unreal`, `Curriculum` 적용 및 대표 라벨 `LevelDesign` 통일
* **Status 변경:** Verified (Partial) → Verified (Partial)

### 2026-11-15 — Test Result: PARTIAL
* **환경:** Knowledge Base 및 HTML 초안 검수 (`Blog_Core-Archive`)
* **검증된 단계:** 2편 PPT 검증 기록 및 3편 연결 브리지 검수 완료
* **실패/보완 항목:** 특정 과정명이 포함된 내부 PPTX 파일명 표기 제거 및 2편→3편 흐름 연결 보강
* **새 오류/팁:** `Experiences & Tips`에서 특정 과정명이 포함된 내부 PPTX 파일명 표기를 `최종 배포용 PPTX 파일`로 일반화함(바이트 크기 및 SHA-256 기준은 유지)
* **Status 변경:** Verified (Partial) → Verified (Partial)

===== KNOWLEDGE PACKAGE END =====
