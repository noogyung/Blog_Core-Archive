---
topic: ue58-character-input-door
title_kr: "Unreal Engine 5.8 Character WASD·MouseY 입력 오류, 캡슐 축소 및 문 통과 검증"
category: Troubleshooting
sub_category: Character-Input-Collision
version: "5.8"
status: Experimental
created_date: 2026-11-12
last_modified: 2026-11-14
language: KR+EN
tags: [UnrealEngine, UE5.8, CharacterMovement, InputMapping, CapsuleCollision, PIE, Troubleshooting]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: ue58-bootcamp
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** ue58-character-input-door
* **Title_KR:** Unreal Engine 5.8 Character WASD·MouseY 입력 오류, 캡슐 축소 및 문 통과 검증
* **Category:** Troubleshooting
* **Sub-Category:** Character-Input-Collision
* **Version:** 5.8
* **Status:** Experimental
* **Date:** 2026-11-12
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Epic Games 공식 문서 - Character Movement Component: https://dev.epicgames.com/documentation/en-us/unreal-engine/character-movement-component
  * [★★★★★] Epic Games 공식 문서 - Enhanced Input in Unreal Engine: https://dev.epicgames.com/documentation/en-us/unreal-engine/enhanced-input-in-unreal-engine
  * [★★★★★] 로컬 PIE 자동/임시 입력 및 상태 복원 검증 JSON (`evidence/input-fix_final_idle.json`, `evidence/input-fix_final_yaw.json`, `evidence/input-fix_input_restored.json`, `evidence/input-fix_walk_closed.json`, `evidence/input-fix_walk_passage.json`)
  * [★★★★★] 로컬 작업 취합 및 검증 기록 (`00_전달자료.md`, `01_검증과자료목록.md`, `evidence/교재_검증_기록.md`, `conversation-index.json`)
  * [★★★★☆] 참고 이미지 (`images/index.md`: `input_play.png`)

---

#### 🔑 Core Concepts (핵심 개념)
  * **CharacterMovementComponent와 CapsuleComponent의 역할 [FACT - 공식 문서]:**
    * `CapsuleComponent`는 캐릭터의 루트 충돌 볼륨으로 바닥 관통이나 벽·문 충돌을 제어한다.
    * `CharacterMovementComponent`는 Walking·Falling·Swimming·Flying 등의 이동 모드와 중력, 가속도, 보행 속도, 계단 오르기 높이(`Step Height`)를 계산해 캡슐에 적용한다.
    * 1인칭 시점 상하(Pitch) 및 좌우(Yaw) 회전에서는 `AddControllerPitchInput`·`AddControllerYawInput`과 카메라/스프링암의 `Use Pawn Control Rotation` 설정이 연동되어야 시점이 움직인다.
  * **초기 확인의 한계: 문·램프 토글만 확인하고 보행 전체를 놓친 사례 [FACT - 로컬 작업 이력]:**
    * 직전 개정(179장 시점)에서는 `BP_WorkCharacter`, Walking, Gravity Scale 1 및 `W`키 위치 변화와 `F`키 문·램프 토글을 확인했다고 보고했으나, 실제 후속 상세 입력 점검에서 **WASD 미작동, 마우스 상하 반전, 캡슐 크기로 인한 문 진입 실패** 문제가 발견되었다.
  * **자동 입력 검사 vs 임시 연속 전진 공급 검사 vs 사용자 실조작 확인의 엄격한 분리 [FACT - 로컬 검증 기록]:**
    * 자동 입력 도구는 짧은 키 입력(tap)만 지원해 WASD 위치 변화와 마우스 회전(`Yaw 0° → 6.610°`)까지만 측정 가능했다.
    * 닫힌 문 정지(`X ≈ -241.80`) 및 열린 문 통과(`X = -320 → -23.85`)는 **임시 연속 전진 값 공급**이라는 별도 테스트로 확인한 뒤, 반드시 임시 값을 제거해 정지 상태(`Velocity = 0`, `restored_forward_idle_value = "0"`)로 복원·재시작 검증을 거쳤다.
    * 실제 키를 계속 누르는 보행 검증은 사용자가 직접 `W`를 약 2초간 눌러 수행했으며, **"문 안으로 정상적으로 들어감"**이라는 답변은 오직 문 통과 성공에 대한 확인이다.

---

#### 🛠️ Procedures (절차)
  1. **Blueprint Key 핀 문자열 파싱 오류 수정 (`BP_WorkCharacter`):** [★★★★★]
     * Key 입력 노드의 핀에 잘못 기록되어 있던 구조체 문자열 `(KeyName=...)`을 제거하고, `W`, `S`, `A`, `D`, `MouseX`, `MouseY` 키 값을 직접 전달하도록 수정한다.
  2. **마우스 상하(Pitch) 방향 보정 및 1인칭 카메라 설정:** [★★★★★]
     * 마우스 Y축 입력이 상하 반전되는 문제를 해결하기 위해 `0 - MouseY`로 부호를 변환해 `AddControllerPitchInput`에 연결한다.
     * `EyeCamera` 상대 높이는 `Z = 72cm`로 배치하고 `Use Pawn Control Rotation`을 활성화한다.
  3. **소형 작업실 출입구 통과용 캡슐 및 이동 수치 조정:** [★★★★★]
     * 문 비례를 유지한 채 통과할 수 있도록 `CapsuleComponent` 크기를 반지름 `24cm → 18cm`, 반높이 `88cm → 80cm`로 축소한다.
     * `CharacterMovement`는 `MOVE_Walking`, `GravityScale = 1`, `Walk Speed = 180`, `Step Height = 25`로 설정한다.
  4. **4단계 분리 검증 및 입력 원복:** [★★★★★]
     * **(1) 단발 키·마우스 검사:** WASD 각 방향 위치 변화 및 마우스 상하 이동 시점 방향 확인; 오른쪽 마우스 입력 후 `Yaw 0° → 6.610°` 변화 기록(`input-fix_final_yaw.json`).
     * **(2) 닫힌 문 충돌 검사:** 연속 전진 임시 공급 시 닫힌 문(`IsOpen: false, DoorYaw: 0`) 앞 `X = -241.797`에서 정지 확인(`input-fix_walk_closed.json`).
     * **(3) 열린 문 통과 검사:** 열린 문(`IsOpen: true, DoorYaw: 100`) 바깥 `X = -320, Y = -40, Z = 82.15`에서 출발해 실내 `X = -23.847`까지 Walking 상태로 진입 확인(`input-fix_walk_passage.json`). (문 밀착 상태에서 열면 문짝에 의해 옆으로 밀리는 물리 현상이 있어 문 밖 출발로 분리 검증)
     * **(4) 원복·재시작 및 실조작 확인:** 임시 전진 값 제거(`restored_forward_idle_value: "0"`) 후 저장·재시작해 `Velocity (0, 0, 0)`, `MOVE_Walking`, `GravityScale = 1` 유지 확인(`input-fix_final_idle.json`, `input-fix_input_restored.json`). 이후 사용자가 `W`키를 길게 눌러 문 안 진입을 직접 확인했다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] PIE 실행 시 WASD 키 입력이 전혀 작동하지 않는 문제**
    * 원인: `BP_WorkCharacter` 그래프의 Key 핀에 키 이름 대신 구조체 직렬화 문자열 `(KeyName=...)`이 잘못 기록되어, 엔진이 첫 글자 `(` 등으로 잘못 해석하며 키 입력이 매칭되지 않았다. (특히 MCP나 텍스트/스크립트 기반 핀 값 대입 시 발생하기 쉬운 오류)
    * 해결법: `(KeyName=...)` 문자열을 제거하고 `W`, `S`, `A`, `D`, `MouseX`, `MouseY` 키 리터럴 값을 Key 핀에 직접 전달하도록 수정했다. [FACT - 로컬 디버깅 기록]
    * 신뢰도: [★★★★★]
  * **[오류 2] 마우스 상하(Pitch) 시점 이동 방향 반전**
    * 원인: `MouseY` 입력 값이 `AddControllerPitchInput`의 기대 부호와 반대로 연결되어 마우스를 위로 올릴 때 아래를 보는 현상이 발생했다.
    * 해결법: Blueprint에서 `0 - MouseY` 연산으로 부호를 반전시켜 `AddControllerPitchInput`에 전달하고, `EyeCamera`의 `Pawn Control Rotation` 동작과 함께 실제 뷰포트 상하 이동 방향을 확인했다. [FACT - 로컬 실행 확인]
    * 신뢰도: [★★★★★]
  * **[오류 3] 좁은 문틀에서 캐릭터가 걸려 들어가지 못하고, 문 밀착 개방 시 옆으로 밀리는 현상**
    * 원인: 초기 캡슐 반지름(24cm)과 반높이(88cm)가 소형 작업실 문폭 대비 크고, 문에 바짝 붙은 상태에서 `BP_WorkDoor`가 `100°`로 회전하면 회전하는 문 메쉬 콜리전이 캐릭터 캡슐을 옆으로 밀어냈다.
    * 해결법: 캡슐 반지름을 `18cm`, 반높이를 `80cm`로 줄여 여유 통과 폭을 확보했고, 검증 시에도 문에 밀착하지 않고 문 바깥(`X = -320`)에서 열린 문을 통과하는 시나리오를 명확히 분리해 확인했다. [FACT/LOCAL OBSERVATION]
    * 신뢰도: [★★★★★]
  * **[오류 4] Unreal에서 C++ 소스 열기 트리거로 인한 Visual Studio 창 팝업 중단**
    * 원인: 입력 검증 중 에디터 내에서 C++ 관련 보기/소스가 열리며 Visual Studio 로그인 및 `.h` 파일 앱 선택 창이 화면을 가로막았다.
    * 해결법: 해당 팝업 창들을 닫고 플레이 검증을 재개했다. (이 프로젝트는 순수 Blueprint/에셋 기반이며 사용자 C++ 모듈이 없음) [LOCAL OBSERVATION]
    * 신뢰도: [★★★★☆]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] 자동 테스트 도구가 단발성 키 입력만 지원해 연속 이동을 검증할 수 없을 때, 임시 연속 전진 값을 넣어 통과 테스트를 진행하는 것 자체는 유효하지만 **테스트 직후 임시 값을 반드시 제거하고 재시작 Idle(`Velocity = 0`)을 JSON으로 검증**해야 교육용 프로젝트에 테스트 찌꺼기가 남지 않는다. — 출처: 로컬 검증 절차 (`01_검증과자료목록.md`)
  * [OPINION] 사용자는 실제 키를 누른 뒤 **"문 안으로 정상적으로 들어감"**을 확인해 주었으며, 이는 문 입구 보행 통과라는 특정 버그가 수정되었음을 확인한 것이지 작업실 구석구석과 전체 디자인에 대한 승인으로 확대 해석해서는 안 된다. — 출처: `02_주제스킬_전달명령.txt`, `conversation-index.json`

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 문 입구 통과(`X = -320 → -23.85`) 이후 작업실 내부 책상·의자·소파·서랍 사이 모든 동선에서의 자유 보행 및 끼임 여부 전수 테스트
  * [ ] 문에 아주 가까이 붙은 상태에서 `F`키로 문을 열 때 옆으로 밀리는 현상을 교육용으로 허용할지, 개방 중 충돌 무시 보완이 필요한지 검토
  * [ ] `BP_WorkCharacter`의 직접 키 핀 방식과 UE 5.8 표준 `Enhanced Input`(`IA_Move`, `IA_Look`, `IMC_Default` Negate Modifier) 방식 중 교재 설명 범위 정렬 여부 확인

---

#### 🏷️ Tags
UnrealEngine, UE5.8, CharacterMovement, InputMapping, CapsuleCollision, PIE, Troubleshooting

===== KNOWLEDGE PACKAGE END =====
