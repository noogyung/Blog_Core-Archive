---
topic: ue58-motion-cloner-wave
title_kr: "Unreal Engine 5.8 Motion Design Cloner·Effector 1,200개 타일 360° 파동 구현과 프레임 검증"
category: Concept
sub_category: Motion-Design-Cloner-Effector
version: "5.8"
status: Experimental
created_date: 2026-11-12
last_modified: 2026-11-12
language: KR+EN
tags: [UnrealEngine, UE5.8, MotionDesign, Cloner, Effector, Sequencer, Niagara]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** ue58-motion-cloner-wave
* **Title_KR:** Unreal Engine 5.8 Motion Design Cloner·Effector 1,200개 타일 360° 파동 구현과 프레임 검증
* **Category:** Concept
* **Sub-Category:** Motion-Design-Cloner-Effector
* **Version:** 5.8
* **Status:** Experimental
* **Date:** 2026-11-12
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Epic Games 공식 문서 - Motion Design Cloners and Effectors in Unreal Engine: https://dev.epicgames.com/community/learning/tutorials/vjP9/unreal-engine-cloner-effector-motion-design
  * [★★★★★] Epic Games 공식 문서 - Scripting the Unreal Editor Using Python (`init_unreal.py`): https://dev.epicgames.com/documentation/unreal-engine/scripting-the-unreal-editor-using-python
  * [★★★★★] 로컬 모션 프레임 정량 검증 JSON (`evidence/motion-revision_motion-verification.json`)
  * [★★★★★] 로컬 작업 취합 및 에셋 목록 (`00_전달자료.md`, `01_검증과자료목록.md`, `evidence/input-fix_archive-validation.json`, `conversation-index.json`)
  * [★★★★☆] 참고 이미지 (`images/index.md`: `motion-grid.png`, `motion-rotation.png`, `motion-sequencer.png`, `Guides/images/motion-frame117.png`, `motion-frame239.png`)

---

#### 🔑 Core Concepts (핵심 개념)
  * **Motion Design Cloner & Effector 아키텍처 [FACT - 공식 문서]:**
    * Unreal Engine 5.4 이상에 도입된 Motion Design 툴셋의 핵심 구성으로, 내부적으로 Niagara를 기반으로 동작한다.
    * **Cloner Actor:** 지정한 레이아웃(Grid, Circle, Spline 등)에 따라 스태틱 메시를 대량 복제 배치한다.
    * **Linked Effector:** Cloner에 연결되어 영향 영역(Falloff/Area) 내 인스턴스의 위치 Offset, 회전(Rotation), 스케일 등을 절차적으로 변형한다.
  * **모션 챕터의 3단계 진화 구분 [FACT - 로컬 작업 이력]:**
    * **초기안:** 짙은 남색 스튜디오에서 정지된 3D 타이틀과 구체 1개가 0초·2.5초·5초 키로 위아래로 움직이는 단순 시퀀스 → 입문 모션 그래픽 교재 요구에 미달해 교체됨.
    * **중간판(SignalGrid 플립):** 타일이 제자리에서 180° 뒤집히며 파동을 만들고, 글자 획을 이루는 168개 타일이 뒤집혀 밝은 뒷면으로 `SignalGrid` 문자를 그리는 버전.
    * **최종판(1,200개 타일 공 추적 360° 파동):** `40 × 30 = 1,200개` 타일(`SM_SignalTile`)을 촘촘히 배치하고, 구체(`MI_WaveBall`)가 지나가는 위치를 따라 Effector가 영향을 주며 타일이 상하 파동과 누적 약 360° 회전을 수행한 뒤 원래 위치·자세로 복귀하는 버전.
  * **회전 해석 원칙: 단일 프레임 Euler 각 대신 누적 궤적·쿼터니언·복귀 확인 [FACT - 로컬 검증 JSON]:**
    * Euler 각은 `-180° ~ +180°` 범위로 래핑(Wrapping)되므로 한 프레임의 회전 각도만 보고 `360°` 회전 여부를 단정할 수 없다.
    * `motion-revision_motion-verification.json`에서 프레임 0~239 구간의 인스턴스 수, 위치 Z, 쿼터니언, Euler 회전 궤적을 함께 기록해 전체 회전 통과와 원위치 복귀를 확인했다.
  * **`Content/Python/bootcamp_cloner.py`의 역할 경계 [FACT - 로컬 파일 구조 및 취합 기록]:**
    * `init_unreal.py`와 함께 포함된 `bootcamp_cloner.py`는 Cloner 액터를 로드할 때 활성 상태를 초기화하는 보조 코드이며, 수강생 대신 모션 씬 전체를 자동 생성·완성하는 스크립트가 아니다.

---

#### 🛠️ Procedures (절차)
  1. **단계별 모션 실습 맵 구축 (`L_Motion_00_Empty` ~ `L_Motion_05_Complete`):** [★★★★★]
     * `00_Empty` → `01_Studio`(배경 스튜디오·재질 `M_GridCase`, `M_AnodizedGraphite`, `M_ChampagneAlloy`) → `02_OneTile`(단일 타일 `SM_SignalTile`) → `03_Grid`(Cloner Grid `40 × 30 = 1,200개` 배열) → `04_Effector`(Effector 링크·영역·Offset·Rotation 설정) → `05_Complete`(8초 시퀀스 `LS_SignalWave_08s` 완성).
  2. **Sequencer 공 이동 키프레임 및 Effector 연동:** [★★★★★]
     * 구체 액터에 `ball_x: -800.0 → +800.0` 이동 키프레임을 설정하고, Effector가 구체 이동에 연동해 1,200개 타일 그리드를 순차적으로 통과하도록 구성한다.
  3. **프레임별 정량 검증 (`motion-revision_motion-verification.json` 실측):** [★★★★★]
     * **초기 정지 구간(프레임 0, 75, 90, 96):** `count = 1200`, `z = 24.0..24.0`, `rot = (0.0, 0.0, 0.0)` 유지 (`ball_x` `-800.0 → -180.65`).
     * **파동·회전 구간(프레임 102~165):** `z_min = 16.00`, `z_max = 109.00` 범위의 상하 파동 발생; 샘플 타일의 Euler Roll/X축 회전은 프레임 108(`-14.23°`) → 114(`-94.51°`) → 117(`-151.84°`) → 120(`+147.65°`) → 126(`+44.02°`) → 132(`+0.14°`) → 138(`0.0°`)을 거쳐 누적 약 360° 회전을 통과.
     * **종료 복귀 구간(프레임 210, 239):** `ball_x = 800.0`, `count = 1200`, `z = 24.00..24.00`, `rot = (0.0, 0.0, 0.0)`으로 모든 타일이 초기 위치·자세로 복귀함.
  4. **뷰포트 스틸컷 기반 교재 제작:** [★★★★★]
     * 시퀀스를 수정할 때마다 영상을 렌더링하지 않고 주요 프레임(`motion-frame117.png`, `motion-frame239.png`, Cloner/Effector/Sequencer UI 캡처)을 스틸컷으로 촬영해 PPT에 반영한다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] 초기 단순 타이틀·구체 모션의 교육 밀도 부족**
    * 원인: 단일 3D 텍스트와 구체 1개의 상하 키프레임만으로는 Motion Design 모드의 Cloner·Effector 활용법을 설명하기 어려웠다.
    * 해결법: SignalGrid 타일 플립 중간판을 거쳐 최종 `40×30 = 1,200개` 타일 그리드 + 공 연동 Effector 파동 구조로 재설계했다. [OPINION - 사용자 요구 반영]
    * 신뢰도: [★★★★★]
  * **[오류 2] 단일 프레임 회전각을 근거로 360° 회전을 단정하는 검증 오류 위험**
    * 원인: Euler 각도는 `-180°~+180°`로 래핑되기 때문에 프레임 117(`-151.84°`)이나 프레임 120(`+147.65°`) 같은 단일 프레임 값만으로는 1바퀴 회전 방향과 완주 여부를 판별할 수 없다.
    * 해결법: 프레임 0부터 239까지 연속 샘플링한 위치·쿼터니언·Euler 궤적(`motion-revision_motion-verification.json`)을 함께 분석해 360° 통과와 원위치 복귀를 확인했다. [FACT - 로컬 검증 JSON]
    * 신뢰도: [★★★★★]
  * **[오류 3] 프레임 검증 결과를 전체 성능 벤치마크로 확대 해석할 위험**
    * 원인: 1,200개 타일 프레임 데이터가 통과했다고 해서 실시간 FPS 성능이나 GPU 부하 측정이 완료된 것으로 오인할 수 있다.
    * 해결법: 해당 JSON은 인스턴스 개수·좌표·자세 복귀 정확성 검증 데이터로만 한정하고, 프레임타임·GPU 프로파일링 수치로는 사용하지 않는다. [FACT - 검증 경계]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] Motion Design 실습은 3D 모델링 지식이 적은 입문자도 메시 1개(`SM_SignalTile`)와 Cloner Grid + Effector 조합만으로 시각적 변화가 뚜렷한 파동 애니메이션을 만들 수 있어, 복잡한 블루프린트 없이도 시퀀서 연동 원리를 익히기 좋았다. — 출처: 교재 구성 경험 (`00_전달자료.md`)
  * [OPINION] 시퀀스를 바꿀 때마다 렌더링 영상을 뽑는 대신 핵심 프레임 스틸컷과 UI 파라미터 캡처를 활용하면 교재 수정 사이클에서 불필요한 대기 시간을 줄일 수 있다. — 출처: 사용자 제작 원칙 (`00_전달자료.md`)

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 최종 `Maps` 직하위 통합 이후 `L_Motion_05_Complete`에서 `motion-verification.json`에 준하는 프레임 궤적 재검증 여부 (해당 JSON은 `motion-revision` 중간 개정 시점 검증 기록임)
  * [ ] 타겟 수강생 실습 PC 사양에서 1,200개 타일 Niagara Cloner 에디터 뷰포트 실시간 프레임레이트(FPS) 실측
  * [ ] `Content/Python/bootcamp_cloner.py`를 수강생이 수동으로 빈 레벨부터 따라 만들 때 반드시 필요한지, 기존 완성 맵 재오픈 시 상태 초기화 전용인지 교재 안내 문구 확인

---

#### 🏷️ Tags
UnrealEngine, UE5.8, MotionDesign, Cloner, Effector, Sequencer, Niagara

===== KNOWLEDGE PACKAGE END =====
