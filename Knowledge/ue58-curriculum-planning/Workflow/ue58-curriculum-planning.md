---
topic: ue58-curriculum-planning
title_kr: "Unreal Engine 5.8 8시간 입문 부트캠프 커리큘럼 기획과 무조교 체크포인트·샌드박스 설계"
category: Workflow
sub_category: Curriculum-And-Operation-Planning
version: "5.8"
status: Verified (Partial)
created_date: 2026-11-14
last_modified: 2026-11-14
language: KR+EN
tags: [UnrealEngine, UE5.8, CurriculumDesign, Bootcamp, Checkpoint, Sandbox, PythonPPTX]
sources_count: 6
blog_draft_path: "Blog_Posts/2026-11/ue58-curriculum-planning.html"
blog_draft_date: 2026-11-14
blog_id: core-archive
blog_published: false
series_id: ue58-bootcamp
blogger_post_id: "7898696813372398598"
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** ue58-curriculum-planning
* **Title_KR:** Unreal Engine 5.8 8시간 입문 부트캠프 커리큘럼 기획과 무조교 체크포인트·샌드박스 설계
* **Category:** Workflow
* **Sub-Category:** Curriculum-And-Operation-Planning
* **Version:** 5.8
* **Status:** Verified (Partial)
* **Date:** 2026-11-14
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Epic Games 공식 문서 - Fab in Unreal Engine: https://dev.epicgames.com/documentation/en-us/unreal-engine/fab-in-unreal-engine
  * [★★★★★] Epic Games 공식 문서 - Procedural Content Generation Framework in Unreal Engine: https://dev.epicgames.com/documentation/en-us/unreal-engine/procedural-content-generation-framework-in-unreal-engine
  * [★★★★★] Epic Games 공식 문서 - Lumen Performance Guide for Unreal Engine (Engine Scalability Settings): https://dev.epicgames.com/documentation/en-us/unreal-engine/lumen-performance-guide-for-unreal-engine
  * [★★★★★] 부트캠프 Phase 1~4 기획·운영·대본 산출물 (`Bootcamp_Docs/01_Overall_Architecture.md`, `02_Class_Detailed_Design.md`, `03_UE_Project_Architecture.md`, `04_Instructor_Manual.md`, `05_Syllabus.md`, `06_Lecture_PPT_Script.md`, `07_Business_Plan_Texts.md`)
  * [★★★★☆] 기존 강의 교재 분석 기록 (`Unreal Engine의 기초와 레벨 제작 과정.pptx` 및 한글 PDF 139페이지 텍스트·구조 추출본)
  * [★★★★★] 초기 PPT 자동 생성 스크립트 (`Bootcamp_Docs/04_Course_Materials/generate_pptx.py` 및 `Unreal_Bootcamp_Presentation.pptx`)

---

#### 🔑 Core Concepts (핵심 개념)
  * **기능 나열 대신 3대 결과물 중심 8교시 구조 [FACT - 로컬 기획 문서]:**
    * 하루 8시간(09:00~18:00, 50분 수업 + 10분 휴식 × 8교시, 점심 1시간), 강사 1인(보조 강사 없음), 비전공 재직자 10명 고정 조건에서 에디터 기능 암기 대신 단일 프로젝트 내 3개 결과물(`Environment` 자연 환경, `Interactive Workspace` 인터랙티브 공간, `Motion Design` 모션 그래픽)을 완성해 가져가는 구조로 설계했다.
  * **기존 139페이지 교재 분석 및 과잉 스펙 제거 [FACT - 로컬 교재 분석 기록]:**
    * 이전 강사의 139페이지 교재는 로그인·저장 버튼까지 빨간 박스로 표시한 비전공 입문자 대상 자료였으나, 버추얼 프로덕션(`nDisplay`), 카메라 트래킹 장비(`StarTracker`), 그린스크린 합성(`Composure`), 나이아가라 등을 백과사전식으로 나열해 8시간 단일 실습으로는 소화가 불가능했다.
    * 이에 방송 하드웨어·합성 파트를 덜어내고 `M1(언리얼 개요, 1교시) → M2(실외 환경 제작, 2~4교시) → M3(실내 환경과 인터랙션, 5~7교시) → M4(AI와 모션 그래픽, 8교시)`의 4모듈 체제로 재편했다.
  * **무조교(Solo Instructor) 운영 원칙: 체크포인트(`Start Level`) + 자율 샌드박스 [FACT/OPINION - 강사 운영 매뉴얼]:**
    * 10분 단위 강제 통제(제식 훈련 방식)는 조교 없는 1인 강사에게 오히려 관리 부담을 가중시킨다.
    * 따라서 진도가 늦거나 오류가 난 수강생은 쉬는 시간에 보조하거나 다음 교시 시작용 `Start Level`을 열어 합류시키고, 일찍 마친 수강생에게는 에셋·파라미터를 자유롭게 확장하는 샌드박스(Sandbox) 시간을 부여하는 유연한 운영 방식을 채택했다.
  * **AI(MCP) 배치 이동과 행정 서류(HWP) 제약 정렬 [FACT - 로컬 개정 이력]:**
    * 초기에는 6교시(블루프린트 자동문)에 AI(MCP) 활용을 넣었으나, 입문자가 노드 연결 원리(핀 색상 매칭과 조립)를 먼저 익히도록 6교시는 수동 블루프린트 기초에 집중하고 AI 활용은 8교시(`M4. AI와 모션 그래픽`, 시간 부족 시 강사 10초 시연 전환)로 이동했다.
    * 또한 국가인적자원개발 컨소시엄 사업계획서(HWP)의 수정 불가 항목(공식 과정명 `Unreal Engine의 기초와 핵심 콘텐츠 제작 과정`, NCS 분류, A4 칸 수, 훈련 시간·정원 등)과 충돌하지 않도록 계획서·교수학습방법 문구를 동기화했다.

---

#### 🛠️ Procedures (절차)
  1. **Phase 1~2 전체 구조 및 8교시 상세 설계 (`01_Overall_Architecture.md`, `02_Class_Detailed_Design.md`):** [★★★★★]
     * 오전 1~4교시 `Environment`, 오후 5~7교시 `Workspace`, 8교시 `Motion Design`으로 시간을 배분하고, 각 교시마다 학습 목표·데모·실습·결과물·준비물·예상 질문·실패 포인트·Recovery Plan을 정의했다.
  2. **Phase 3~4 베이스 프로젝트 격리 폴더(`_Bootcamp`) 및 강사 매뉴얼 수립 (`03_UE_Project_Architecture.md`, `04_Instructor_Manual.md`):** [★★★★★]
     * 수강생이 `Blank` 프로젝트를 새로 만들지 않고 플러그인(PCG, Motion Design, Fab, Model Context Protocol)이 사전 활성화된 베이스 프로젝트를 압축 해제해 바로 여는 전략을 세웠다.
     * 루트 `Content/_Bootcamp` 아래로 작업 폴더를 격리하고, 10명 동시 Fab 다운로드 지연을 막기 위한 사전 캐시·로컬 `.uasset` 백업 및 저사양 PC 대상 `Engine Scalability Settings: Medium` 하향 가이드를 문서화했다.
  3. **기존 139장 교재 필터링 및 `M1~M4` 대본·행정 문서 동기화 (`05_Syllabus.md`, `06_Lecture_PPT_Script.md`, `07_Business_Plan_Texts.md`):** [★★★★★]
     * 기존 교재에서 UI 기초 캡처(뷰포트·아웃라이너·디테일)와 블루프린트 개념만 선별 차용하고, 가상 프로덕션 장비 세팅은 제외했다.
     * HWP 사업계획서의 고정 행정 항목을 유지한 채 과정명(`Unreal Engine의 기초와 핵심 콘텐츠 제작 과정`), 주요훈련내용, 교수학습방법(체크포인트 + 자율 샌드박스) 문구를 최신 커리큘럼과 맞췄다.
  4. **`python-pptx` 기반 초기 텍스트 PPTX 자동 생성 (`generate_pptx.py` → `Unreal_Bootcamp_Presentation.pptx`) 및 후속 단계 연결:** [★★★★★]
     * `M1~M4` 목차와 대본 불릿이 담긴 기본 흰색 템플릿 PPTX 초안을 파이썬으로 자동 생성했다.
     * 그러나 텍스트 위주 기본 슬라이드만으로는 비전공 입문자가 에디터 버튼 위치를 찾기 어렵고 실제 구동 가능한 세분화 `.umap` 체크포인트도 부족하다는 한계가 확인되어, 후속 작업(`ue58-curriculum-maps-ppt`)의 28개 단계별 맵 구축과 실제 UI 캡처 181장 PPT 재구성으로 이어졌다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] 10명 동시 Fab 에셋 다운로드 및 `.mp4` 영상 렌더링 시 물리적 시간 초과 위험**
    * 원인: 강의장 네트워크 대역폭 한계로 10명이 GB 단위 자연환경 에셋을 동시에 받거나, 시퀀서 `.mp4` 렌더링 중 셰이더 컴파일이 겹치면 50분 교시가 멈출 위험이 컸다.
    * 해결법: 무거운 숲 에셋은 베이스 프로젝트에 사전 포함하고, 현장 Fab 실습은 가벼운 소품 위주 또는 로컬 `.uasset` 드래그앤드롭 백업으로 전환했으며, 렌더링 실패 시 뷰포트 실시간 재생 확인으로 대체하도록 설계했다. [FACT - 로컬 현실성 검토]
    * 신뢰도: [★★★★★]
  * **[오류 2] 6교시 블루프린트 입문 단계에 AI(MCP)와 노드 수동 조립을 동시에 배치해 발생하는 학습 혼선**
    * 원인: 블록 조립 개념이 잡히기 전 6교시에 MCP 프롬프트 질의를 넣으면, 비전공 수강생이 핀 색상 매칭과 자동문 기초 흐름을 익히기 어려워졌다.
    * 해결법: 6교시는 수동 블루프린트 조립에 집중하도록 고정하고, AI(MCP) 활용은 8교시 모션 그래픽 실습 및 강사 10초 데모 시연으로 이동했다. [FACT - 로컬 개정 이력]
    * 신뢰도: [★★★★★]
  * **[오류 3] 관공서 제출용 사업계획서(HWP) 고정 항목과 신규 커리큘럼 문서 간 과정명·대상 불일치**
    * 원인: 이진 HWP 포맷 추출이 매끄럽지 않아 초기 동기화 시 과정명을 이전 버전 PDF 파일명(`Unreal Engine의 기초와 레벨 제작 과정`)으로 잘못 롤백해 충돌이 발생했다.
    * 해결법: 사용자 확인을 거쳐 공식 과정명을 `Unreal Engine의 기초와 핵심 콘텐츠 제작 과정`으로 정정하고, A4 칸 수·NCS 분류·시간·정원 등 변경 불가 행정 항목 외의 훈련내용·교수학습방법만 동기화했다. [FACT - 로컬 개정 이력]
    * 신뢰도: [★★★★★]
  * **[오류 4] `python-pptx`로 생성한 초기 텍스트 위주 기본 템플릿 PPT의 현장 가이드 한계**
    * 원인: 텍스트 불릿만 담은 기본 템플릿 PPT와 8개 개념적 `Map_*_Start` 설계만으로는 실제 UE 5.8 에디터 화면에서 초보자가 클릭 지점을 따라가기 어려웠다.
    * 해결법: 이 초안 구조를 기반으로 실제 UE 5.8 레벨 28개(`L_Env`, `L_Work`, `L_Motion`)와 실제 UI 캡처·빨간 강조 박스 중심의 181장 PPT를 새로 제작하는 다음 단계(`ue58-curriculum-maps-ppt`)로 넘어갔다. [FACT - 로컬 후속 연계]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] 조교 없이 10명을 혼자 가르칠 때는 전원을 10분 단위로 통제하려 하기보다, 빠른 수강생에게는 자유롭게 꾸미는 샌드박스 시간을 주고 뒤처진 수강생은 쉬는 시간 케어와 다음 교시 `Start Level` 오픈으로 합류시키는 편이 강사와 수강생 모두의 부담을 줄인다. — 출처: `04_Instructor_Manual.md`
  * [OPINION] 국비·컨소시엄 과정 개편 시에는 NCS 분류번호, 시간, 정원, 공식 과정명 등 행정 서류상 변경 불가능한 항목을 먼저 고정해 두고 그 틀 안에서 세부 실습 내용과 교수학습방법 문구를 현대화해야 서류 불일치를 피할 수 있다. — 출처: `07_Business_Plan_Texts.md` 개정 기록

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 사업계획서(HWP) 최종 제출본에서 A4 페이지 칸 수 제한 내에 교수학습방법 및 주요훈련내용 수정 텍스트가 잘림 없이 들어갔는지 확인
  * [ ] 초기 설계안의 8개 교시 단위 `Map_*_Start` 구조에서 최종 28개 세부 체크포인트 맵(`L_Env`, `L_Work`, `L_Motion`)으로 확장할 때 교시별 배정 시간(50분×8교시) 내 소화 가능 범위 실전 점검

---

#### 🏷️ Tags
UnrealEngine, UE5.8, CurriculumDesign, Bootcamp, Checkpoint, Sandbox, PythonPPTX

===== KNOWLEDGE PACKAGE END =====
