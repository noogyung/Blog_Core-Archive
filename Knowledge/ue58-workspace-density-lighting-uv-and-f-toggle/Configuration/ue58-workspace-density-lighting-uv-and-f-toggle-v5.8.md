---
topic: ue58-workspace-density-lighting-uv-and-f-toggle
title_kr: "Unreal Engine 5.8 작은 작업실의 공간 밀도, 물리 광원·UV 정렬 및 F키 토글 설계"
category: Configuration
sub_category: Level-Design-Material-Blueprint
version: "5.8"
status: Experimental
created_date: 2026-11-12
last_modified: 2026-11-12
language: KR+EN
tags: [UnrealEngine, UE5.8, LevelDesign, Lighting, WorldAlignedTexture, Blueprint, Interaction]
sources_count: 4
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** ue58-workspace-density-lighting-uv-and-f-toggle
* **Title_KR:** Unreal Engine 5.8 작은 작업실의 공간 밀도, 물리 광원·UV 정렬 및 F키 토글 설계
* **Category:** Configuration
* **Sub-Category:** Level-Design-Material-Blueprint
* **Version:** 5.8
* **Status:** Experimental
* **Date:** 2026-11-12
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Epic Games 공식 문서 - Texturing Material Functions (`WorldAlignedTexture` / Triplanar Projection): https://dev.epicgames.com/documentation/unreal-engine
  * [★★★★★] 로컬 작업 취합 및 검증 기록 (`00_전달자료.md`, `01_검증과자료목록.md`, `evidence/교재_검증_기록.md`, `conversation-index.json`)
  * [★★★★★] 최종 프로젝트 패키지 및 에셋 목록 (`evidence/input-fix_archive-validation.json`, `evidence/input-fix_final-dependencies.json`)
  * [★★★★☆] 참고 이미지 인덱스 (`images/index.md`: `work_step_0.png`, `work_step_8.png`, `work_clean.png`, `wallpaper_graph.png`, `door_graph.png`, `lamp_graph.png`)

---

#### 🔑 Core Concepts (핵심 개념)
  * **소형·고밀도 실내 공간 설계 원칙 [OPINION - 사용자 요구 및 설계 경험]:**
    * 초기에 검토된 `Cozy Cartoon Pack` 기반의 넓고 헐거운 스튜디오 대신 `Detective Office` 에셋을 사용해 **초기 약 6.3×4.9m → 최종 약 4×3.6m**로 방 크기를 축소했다.
    * 넓고 비어 보이는 방보다 작은 공간 안에 책상·의자·수납장·증거판·책·전화기·서류 더미·컵·액자 등 소품이 밀도 있게 채워져 있으면서도 실제로 걸어서 문을 통과할 수 있는 비례가 중요하다.
    * 최종 `L_Work_00_Empty`의 사용자 배치 액터 수는 `0개`, `L_Work_08_Complete`의 번호 부여 배치 액터 수는 `295개`로 기록되었다.
  * **물리적 발광체 대응 조명 및 차폐 구조 [OPINION/LOCAL OBSERVATION]:**
    * 허공에 근거 없이 떠 있는 광원을 두지 않고, 책상 스탠드(`BP_WorkLamp`, `MI_LampBulb`) 등 실제 발광 사물과 조명을 1:1로 대응시킨다.
    * 창문과 블라인드(`SM_Blinds`)의 정렬을 맞춘 뒤, 창 밖 광원이 벽·창틀·블라인드에 의해 정상적으로 차폐되고 그림자를 드리우도록 배치 관계를 수정했다.
  * **월드 좌표 기반 벽지 UV 타일링 (`M_Wallpaper_World_XZ`, `M_Wallpaper_World_YZ`) [FACT - 공식 문서 및 로컬 에셋]:**
    * Epic 공식 문서의 `WorldAlignedTexture`(Triplanar 투영) 개념과 같이, 오브젝트 로컬 UV나 비균일 스케일에 의존하지 않고 월드 좌표(`WorldPosition`의 XZ, YZ 평면) 기준으로 텍스처를 투영하면 벽 크기 변경이나 창문 컷아웃 부근에서 발생하던 UV 늘어남·어긋남을 방지할 수 있다.
    * 단, 공식 문서가 경고하듯 월드 공간 투영 재질은 동적 이동 오브젝트에 적용 시 텍스처 스위밍(Texture Swimming)이 발생하므로 벽체 같은 정적 환경에 적용하는 것이 적합하다.
  * **초기 접근 자동문 vs 최종 명시적 `F`키 인터랙션 토글 [FACT - 로컬 실행 기록]:**
    * **초기 버전(67장 단계):** 캐릭터/시점이 문에 접근하면 90°로 열리고 조명이 켜지며 짧은 효과음 재생 연결까지 구성하고, 물러나면 닫히는 방식이었다. (당시 효과음 실제 청취는 미검증)
    * **최종 버전(작업실·MCP 개정 이후):** 사용자 요구에 따라 접근 자동 동작 대신 플레이어가 근처에서 **`F`키를 명시적으로 눌러 상태를 유지·전환하는 방식**으로 개편했다.
      * **`BP_WorkDoor`:** `F`키 입력 시 `100°`까지 회전해 열린 상태를 유지하고, 다시 `F`키를 누르면 `0°`로 닫힘 (`door_graph.png`, `input-fix_walk_passage.json`에서 `IsOpen: true, DoorYaw: 100` 확인).
      * **`BP_WorkLamp`:** `F`키 입력 후 조명 `Visibility`가 `False`로 꺼지고, 다음 `F`키 입력 후 `True`로 켜짐 확인 (`lamp_graph.png`, `교재_검증_기록.md`).

---

#### 🛠️ Procedures (절차)
  1. **단계별 실내 공간 구축 (`L_Work_00_Empty` ~ `L_Work_06_Lighting`):** [★★★★★]
     * `00_Empty`(0개) → `01_Shell`(4×3.6m 외벽·창·출입구 구조) → `02_Furniture`(책상·의자·캐비닛 등 주요 가구) → `03_WallDetails`(블라인드·증거판·액자·창틀 정렬) → `04_Props`(책·서류·전화기·컵 등 밀도 소품) → `05_Materials`(벽지 월드 좌표 UV 적용 및 재질 인스턴스 할당) → `06_Lighting`(램프 발광 연동 및 창 외부 광원 차폐).
  2. **벽지 UV 그래프 적용 및 문 메쉬 비례 보정:** [★★★★★]
     * 창문 주변 벽체 방향에 따라 `M_Wallpaper_World_XZ`와 `M_Wallpaper_World_YZ` 머티리얼을 구분 적용해 벽지 패턴 왜곡을 수정한다.
     * 문 메쉬(`SM_door_Baked_0021`)는 비균일 스케일 확대를 피하고 원래 비례를 유지한 상태에서 프레임 및 캐릭터 캡슐 통과 규격과 맞춘다.
  3. **`F`키 토글 인터랙션 구성 (`07_Interaction` → `08_Complete`):** [★★★★★]
     * `BP_WorkDoor`와 `BP_WorkLamp` Blueprint에 근접 감지 + `F`키 입력 바인딩, Timeline 회전(`0°↔100°`) 및 `Visibility` 토글 로직을 구현한다.
     * MCP trace 검사로 닫힌 문, 실내·입구 바닥, 책상 서랍의 충돌(Collision) 설정을 점검한다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] 넓고 비어 있는 방 구도 및 부자연스러운 초기 에셋 톤**
    * 원인: 초기 Cozy Cartoon Pack 기반이나 넓은 방 크기에서는 소품 간 밀도가 떨어져 썰렁해 보이고, 실사 위주의 Detective Office/자연환경 톤과도 맞지 않았다.
    * 해결법: Detective Office 에셋으로 통일하고 작업실 크기를 6.3×4.9m에서 최종 4×3.6m로 단계적으로 줄여 295개 액터 기반의 밀도 높은 작업실로 재구성했다. [OPINION]
    * 신뢰도: [★★★★★]
  * **[오류 2] 창문 주변 벽지 UV 어긋남 및 문 메쉬 비례 왜곡**
    * 원인: 벽체 스케일 조정과 창문 개구부 주변에서 기본 UV 타일링이 어그러졌고, 문 크기를 맞추기 위한 비균일 스케일링이 문 형태 비례를 해쳤다.
    * 해결법: 벽체 법선 축에 맞춰 월드 좌표 기반 머티리얼(`M_Wallpaper_World_XZ`, `M_Wallpaper_World_YZ`)로 UV를 재구성하고(`wallpaper_graph.png`), 문 메쉬는 비균일 확대를 제거해 원래 비례를 유지하도록 수정했다. [FACT - 로컬 실행 및 캡처 기록]
    * 신뢰도: [★★★★★]
  * **[오류 3] 허공 광원 및 창틀/블라인드 차폐 불일치**
    * 원인: 실제 램프 발광부와 무관한 위치에 보조 광원이 놓이거나 블라인드·창틀 정렬이 어긋나 빛과 그림자의 물리적 근거가 약했다.
    * 해결법: 램프 액터(`BP_WorkLamp`) 위치와 광원 컴포넌트를 일치시키고 창문·블라인드(`SM_Blinds`) 정렬을 바로잡아 외부 광원의 차폐와 그림자를 보정했다. [LOCAL OBSERVATION]
    * 신뢰도: [★★★★☆]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] 사용자는 입문용 씬이라도 빈 상자 방보다 작은 면적 안에 책상 위 소품과 가구가 꽉 들어찬 구도를 중요시했다. 방 크기를 먼저 줄이면 같은 수량의 소품으로도 밀도를 높이기 쉽다. — 출처: 사용자 요구 취합 (`00_전달자료.md`)
  * [OPINION] 접근하면 자동으로 열리고 멀어지면 닫히는 문은 시연에는 편리하지만, 문 근처에서 멈춰 서 있거나 실내를 둘러보는 실습에서는 `F`키 토글 방식이 의도치 않은 개폐를 막아 교육용으로 더 적합했다. — 출처: `00_전달자료.md` §5
  * [FACT] 초기 버전에서 다룬 접근 시 효과음 재생은 연결 여부까지만 확인되었으며 실제 스피커 청취 테스트는 검증되지 않았다. — 출처: `00_전달자료.md` §11, `conversation-index.json`

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 초기 버전의 효과음 재생 연결과 달리 최종 `L_Work_08_Complete`에서 오디오/사운드 실제 청취 여부 확인 필요
  * [ ] 작업실 내부 모든 가구·소품(책상, 의자, 코트걸이, 테이블 등) 주변 전체 동선에서의 콜리전 끼임 여부 전수 보행 테스트 (현재는 입구 문 통과 및 주요 trace만 확인)
  * [ ] 최종 디자인 구도(가구/소품 배치, 색감, 조명 밝기)에 대한 사용자 최종 만족도 확인 (`문 안으로 정상적으로 들어감` 응답은 문 통과 확인일 뿐 전체 디자인 승인이 아님)

---

#### 🏷️ Tags
UnrealEngine, UE5.8, LevelDesign, Lighting, WorldAlignedTexture, Blueprint, Interaction

===== KNOWLEDGE PACKAGE END =====
