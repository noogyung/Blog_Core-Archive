---
topic: powertoys-review-and-tips
title_kr: Microsoft PowerToys 개발 역사와 실전 활용 리뷰: 창 관리부터 다중 PC 제어까지
category: Review
sub_category: Productivity
version: 2026-08-29
status: Verified
created_date: 2026-08-29
last_modified: 2026-08-30
language: KR+EN
tags: [PowerToys, Windows, FancyZones, MouseWithoutBorders, Productivity, Development, Review]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** powertoys-review-and-tips
* **Title_KR:** Microsoft PowerToys 개발 역사와 실전 활용 리뷰: 창 관리부터 다중 PC 제어까지
* **Category:** Review
* **Sub-Category:** Productivity
* **Version:** 2026-08-29
* **Status:** Verified
* **Date:** 2026-08-29
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Microsoft Learn 공식 문서: https://learn.microsoft.com/ko-kr/windows/powertoys/?WT.mc_id=twitter-0000-docsmsft
  * [★★★★★] Microsoft PowerToys GitHub Repository: https://github.com/microsoft/PowerToys
  * [★★★★☆] Microsoft DevBlogs & Build 2019 Conference Announcement: https://devblogs.microsoft.com/
  * [★★★★★] 필자 실무 개발 및 듀얼 네트워크/멀티 모니터 환경 실측 검증 (2026-08-30)

---

#### 🔑 Core Concepts (핵심 개념)

##### 1. PowerToys의 역사와 탄생 배경 (Development History & Evolution)
* **Windows 95 태동기 (The Origin):** Windows 95 출시 당시, 윈도우 셸(Shell) 개발팀 엔지니어들이 사내 실험 및 개인 업무 편의를 위해 개발하던 비공식 유틸리티 모음으로 시작됨. `TweakUI`, `Command Prompt Here` 등 OS 기본 기능으로 제공하기에는 무겁거나 실험적인 도구들을 파워 유저에게 별도 제공하며 폭발적인 인기를 얻음. [FACT]
* **Windows XP 전성기 (The Golden Age):** Windows XP 출시와 함께 공식 지원되지 않는 파워 유저 전용 유틸리티 팩으로 확장됨. `ClearType Tuner`, `Image Resizer`, `Virtual Desktop Manager`, `Alt-Tab Replacement` 등이 대표적이었으나, Windows Vista, 7, 8 시대를 거치며 점차 공식 유지보수가 중단되고 동면 상태에 들어감. [FACT]
* **Windows 10/11 오픈소스 부활 (Build 2019 Reboot):** 2019년 5월 Microsoft Build 개발자 컨퍼런스에서 오픈소스 프로젝트로의 부활을 공식 선언함. Microsoft 개발팀 주도하에 GitHub(`microsoft/PowerToys`)에 리포지토리를 개설하고, C++, C#, WinUI 3 기술 스택을 기반으로 전 세계 오픈소스 커뮤니티와 협업하여 현대적 윈도우 환경에 맞게 전면 재작성됨. [FACT]

##### 2. PowerToys의 존재 목적과 설계 철학 (Purpose & Philosophy)
* **OS 기본 셸의 한계 극복:** 윈도우 기본 셸에 모든 기능을 직접 통합할 경우 발생하는 OS 비대화(Bloatware) 및 안정성 저하를 방지하면서도, 개발자 및 파워 유저가 요구하는 심층 커스터마이징 기능을 독립된 모듈 형태로 민첩하게 제공함. [FACT]
* **차세대 OS 기능의 인큐베이터:** 커뮤니티의 피드백을 실시간으로 수용하여 검증된 유틸리티 기능을 향후 정식 Windows 빌드에 흡수·통합하는 테스트베드 역할을 수행함. [FACT]

##### 3. 개인적으로 자주 사용하는 유틸리티 (Frequently Used Utilities)
* **FancyZones (PowerToys 사용의 핵심 이유):** 기본 윈도우 스냅(Snap)의 한계를 뛰어넘어 다중 모니터 환경에서 창 레이아웃을 생성하고 정렬하는 창 관리자. 윈도우 스냅 재정의 옵션을 '상대 위치 기준(`Win + 상하좌우 방향키`)'으로 변경하여 상하로 나누어 세로 창에서의 사용성을 높임. [OPINION/FACT]
* **경계 없는 마우스 (Mouse Without Borders):** 단일 키보드/마우스로 로컬 네트워크 내 최대 4대의 Windows PC를 제어. 키보드 자동 연결, 클립보드 공유, 파일 복사/붙여넣기가 작동함. 방화벽 이슈는 옵션 내 '테두리 없는 마우스에 대한 방화벽 규칙 추가' 버튼으로 해결 가능. (단점: 모니터 상하 배치 미지원, 화면 좌우 끝으로만 커서 이동 가능) [OPINION/FACT]
* **미리보기 (Peek):** 탐색기에서 파일 선택 후 `Space` 키를 눌러 팝업으로 내용을 즉시 확인. Visual Studio 미설치 환경이거나 무거운 IDE를 띄우기 싫을 때 `.cpp` 소스 파일 등을 빠르게 확인하는 용도로 사용. 압축 파일(`.zip`) 내부 트리 구조 및 용량 확인, 방향키를 통한 연속 파일 탐색 지원. [OPINION/FACT]
* **색 선택기 (Color Picker):** 단축키(`Win + Shift + C`)로 화면 임의 픽셀의 색상을 추출. 웹 개발에 쓰이는 `HEX`와 유니티(Unity) 등 게임 엔진 및 셰이더 개발에 쓰이는 `RGB`, `HSL`, `HSV` 포맷을 활성화하여 색상 확인 및 복사 시의 번거로움을 해소함. [OPINION/FACT]
* **항상 위에 표시 (Always on Top):** 단축키(`Win + T`로 변경)로 창을 최상단에 고정. 브라우저 창 고정용으로도 여전히 사용하지만 유튜브를 예전만큼 자주 보지 않게 되었고, Firefox의 자체 PiP(팝업) 기능 역시 유튜브 시청 시에만 사용하고 있어 사용 빈도가 줄어듦. [OPINION/FACT]

##### 4. 그 외 전체 유틸리티 상세 소개 (공식 31종 기능)
* **고급 붙여넣기 (Advanced Paste):** 클립보드 텍스트를 마크다운, JSON 등 지정 포맷으로 변환하거나 OpenAI API 기반 AI 텍스트 변환 지원. [FACT]
* **Awake:** 전원 및 절전 모드 설정을 변경하지 않고도 시스템이 절전 상태로 들어가지 않도록 트레이에서 깨어있는 상태 유지. [FACT]
* **명령을 찾을 수 없음 (Command Not Found):** PowerShell 7에서 알 수 없는 명령 입력 시 설치 가능한 WinGet 패키지를 자동 탐색하여 제안. [FACT]
* **명령 팔레트 (Command Palette):** 자주 사용하는 명령, 앱, 도구에 접근할 수 있는 통합 명령 실행 인터페이스. [FACT]
* **자르기 및 잠금 (Crop and Lock):** 실행 중인 창의 특정 영역만 잘라내어 대화형 미니 창 또는 썸네일로 고정. [FACT]
* **환경 변수 (Environment Variables):** 시스템 및 사용자 환경 변수를 GUI 기반 프로필로 관리하고 편집. [FACT]
* **File Explorer 추가 기능 (File Explorer add-ons):** 탐색기 미리 보기 창 및 썸네일에서 SVG, Markdown, PDF, G-code, QOI, STL 등 다양한 개발/그래픽 포맷 렌더링 지원. [FACT]
* **파일 잠금 장치 (File Locksmith):** 파일이 다른 프로세스에 점유되어 수정/삭제되지 않을 때 우클릭 메뉴에서 점유 프로세스를 확인하고 종료. [FACT]
* **잡기 및 이동 (Grab and Move):** 단축키를 누른 채 창 내부 아무 곳이나 드래그하여 창을 이동 및 크기 조절. [FACT]
* **호스트 파일 편집기 (Hosts File Editor):** Windows Hosts 파일의 도메인 및 IP 매핑을 안전하게 편집하는 전용 GUI 관리자. [FACT]
* **이미지 리사이저 (Image Resizer):** 탐색기 우클릭 컨텍스트 메뉴에서 이미지 해상도와 크기를 일괄 변환. [FACT]
* **키보드 관리자 (Keyboard Manager):** 단일 키 또는 단축키 조합을 시스템 전역 또는 특정 앱별로 리매핑. [FACT]
* **라이트 스위치 (Light Switch):** 시간대에 따라 Windows 시스템 테마를 라이트 모드와 다크 모드로 자동 전환. [FACT]
* **마우스 유틸리티 (Mouse Utilities):** 마우스 찾기(`Ctrl` 2회), 형광펜 클릭 표시, 마우스 점프(다중 모니터 커서 이동), 십자선 표시 지원. [FACT]
* **새로 만들기+ (New+):** 탐색기 우클릭 메뉴에서 사전에 정의한 템플릿(문서, 폴더 구조 등)으로 새 항목 생성. [FACT]
* **전원 표시 (Power Display):** 트레이 플라이아웃에서 DDC/CI 지원 외부 모니터의 밝기, 대비, 볼륨, 입력 소스 제어. [FACT]
* **PowerRename:** 정규식, 텍스트 치환, 대소문자 변환, 실행 취소를 지원하는 대량 파일 이름 일괄 변경 도구. [FACT]
* **PowerToys Run:** `Alt + Space`로 실행되는 플러그인 기반의 빠른 검색 및 앱 런처(계산기, 단위 변환 등 지원). [FACT]
* **빠른 강조 (Quick Accent):** 악센트 문자가 포함된 외국어 입력 시 키보드 키를 길게 눌러 악센트 문자 선택. [FACT]
* **레지스트리 미리 보기 (Registry Preview):** `.reg` 레지스트리 파일의 변경 내용을 GUI 트리 및 텍스트로 시각화하고 편집/병합. [FACT]
* **화면 눈금자 (Screen Ruler):** 화면 상의 픽셀 거리, 영역 크기 및 이미지 에지(Edge) 검출 기반 자동 크기 측정. [FACT]
* **바로 가기 가이드 (Shortcut Guide):** 현재 화면에서 사용 가능한 Windows 단축키 목록을 오버레이로 표시. [FACT]
* **텍스트 추출기 (Text Extractor):** 화면의 임의 영역을 캡처하여 즉시 OCR로 텍스트를 추출하고 클립보드에 복사. [FACT]
* **창 호퍼 (Window Hopper):** 동일한 애플리케이션에 속한 여러 창 사이를 단축키로 순환 탐색. [FACT]
* **작업 영역 (Workspaces):** 특정 작업 환경에 필요한 여러 프로그램의 위치와 화면 배치를 한 번에 일괄 실행. [FACT]
* **ZoomIt:** 기술 프레젠테이션을 위한 화면 확대, 펜 주석 및 타이머 기능. [FACT]

##### 5. 로컬 백업 방식 및 OneDrive 연동
* **전용 백업 포맷 (.ptb):** PowerToys는 설정 백업 시 `.ptb` (PowerToys Backup) 확장자의 단일 설정 패키지 파일을 생성함. [FACT]
* **기본 저장 위치:** `%USERPROFILE%\Documents\PowerToys\Backup` (`C:\Users\{username}\Documents\PowerToys\Backup`)에 저장됨. [FACT]
* **OneDrive 연동 동기화:** 윈도우 설치 시 기본 활성화되는 OneDrive 동기화(무료 5GB)를 통해 백업 폴더가 자동 보관되며, 윈도우 재설치나 신규 PC 세팅 후 PowerToys 일반 설정에서 백업 위치를 해당 폴더로 지정하고 복원 버튼을 누르면 즉시 설정이 복원됨. [OPINION/FACT]

---

#### 🛠️ Procedures (절차)

##### 1. PowerToys 설치 및 기본 권한 구성 [★★★★★] ✅ Verified 2026-08-30
1. `winget install Microsoft.PowerToys --source winget` 실행 또는 GitHub 공식 릴리스에서 다운로드 설치.
2. 시스템 시작 시 자동 실행 활성화 및 관리자 권한으로 실행 옵션 활성화.

##### 2. FancyZones 다중 모니터 레이아웃 및 단축키 커스텀 [★★★★★] ✅ Verified 2026-08-30
1. `PowerToys 설정` > `FancyZones` 실행.
2. 레이아웃 편집기 (기본 단축키: `Win + Shift + \``)를 열어 모니터별 분할 그리드 생성.
3. '창 동작' 설정에서 **Windows 스냅 단축키 재정의** 활성화.
4. 이동 방식을 **상대 위치 기준(`Win + 상하좌우 방향키`)**으로 변경하여 사용.

##### 3. Color Picker 개발용 포맷 및 단축키 설정 [★★★★★] ✅ Verified 2026-08-30
1. `PowerToys 설정` > `색 선택기` 메뉴 진입.
2. 활성화 단축키 확인 (기본 및 사용 단축키: `Win + Shift + C`).
3. 색상 형식에서 웹용 **HEX**와 게임 엔진/셰이더용 **RGB, HSL, HSV** 포맷을 활성화하고 우선순위 배치.

##### 4. Peek(미리보기) 활성화 및 단축키 설정 [★★★★★] ✅ Verified 2026-08-30
1. `PowerToys 설정` > `Peek` 메뉴 진입.
2. 기본 단축키(`Ctrl + Space`) 대신 조작이 직관적인 **단일 `Space` 키**로 단축키 설정.
3. 탐색기에서 `.cpp` 소스 코드나 `.zip` 압축 파일을 선택하고 `Space` 키를 눌러 즉시 프리뷰 확인.
4. 미리보기 창이 열린 상태에서 키보드 방향키를 눌러 이전/다음 파일로 이동 탐색.

##### 5. Always on Top 단축키 커스텀 [★★★★★] ✅ Verified 2026-08-30
1. `PowerToys 설정` > `Always On Top` 메뉴 진입.
2. 기본 단축키(`Win + Ctrl + T`)에서 누르기 편한 **`Win + T`**로 변경.
3. 독립 프로그램 창 선택 후 `Win + T`를 눌러 최상단 고정 및 테두리 표시 확인.

##### 6. 경계 없는 마우스(Mouse Without Borders) 듀얼 네트워크 연동 절차 [★★★★★] ✅ Verified 2026-08-30
1. 메인 PC와 서브 PC 양쪽에 PowerToys 설치 후 `경계 없는 마우스` 활성화.
2. 일반적인 단일 로컬 네트워크 환경에서는 보안 키(Security Key)와 컴퓨터 이름 입력만으로 즉시 연동됨.
3. **특수 환경 (메인 PC가 Wi-Fi 내부망 + 랜선 외부망으로 분리된 경우):**
   - 양쪽 PC의 Wi-Fi 네트워크 속성을 '개인(Private)'으로 설정.
   - PowerToys 설정 내 **IP 매핑(IP Mapping)**을 활성화하고 `[상대PC이름] [상대PC_Wi-Fi_IP]` 등록.
   - 방화벽 알림 발생 시 옵션 내 `테두리 없는 마우스에 대한 방화벽 규칙 추가` 버튼 클릭 (또는 방화벽 포트 TCP 15100, UDP 15101 인바운드 허용).
   - 네트워크 연결 속성(`ncpa.cpl`) > `IPv4` > `고급`에서 자동 메트릭 해제 후 수동 지정:
     - 유선 랜(외부망): 인터페이스 메트릭 `10`
     - Wi-Fi(내부망): 인터페이스 메트릭 `20` (인터넷 트래픽은 외부망으로 고정하고 내부망 통신 분기)

##### 7. 설정 백업 생성 및 OneDrive 동기화 복원 절차 [★★★★★] ✅ Verified 2026-08-30
1. `PowerToys 설정` > `일반` > `백업 및 복원`에서 백업 생성 클릭 (`.ptb` 파일 생성).
2. 기본 경로인 `%USERPROFILE%\Documents\PowerToys\Backup`이 OneDrive 동기화 영역에 포함되어 있는지 확인.
3. 신규 PC 또는 윈도우 포맷 후: PowerToys 설치 후 일반 설정에서 백업 위치를 OneDrive 동기화 폴더로 지정하고 **복원(Restore)** 버튼을 클릭하여 설정 일괄 복원.

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[Mouse Without Borders 다중 네트워크 어댑터 분리 환경에서의 연결 실패]** *(사용자 검증 추가 — 2026-08-30)*
  * 환경: 메인 PC에 내부망용 Wi-Fi와 외부망용 유선 랜선이 동시 연결된 특수 환경 (일반적인 단일 공유기 환경에서는 보안 키 입력만으로 정상 연결됨)
  * 증상: 보안 키를 정확히 입력했음에도 연결 대기 상태에서 타임아웃 발생.
  * 원인: 메인 PC의 기본 브로드캐스트 패킷이 외부망으로 연결된 유선 랜 어댑터로 송출되어 내부망 Wi-Fi에 있는 서브 PC를 검색하지 못함.
  * 해결법:
    1. PowerToys 설정의 `IP 매핑(IP Mapping)`에 상대 PC 이름과 내부망 Wi-Fi IP를 수동 매핑.
    2. 옵션 내 `테두리 없는 마우스에 대한 방화벽 규칙 추가`를 실행하여 방화벽 포트(TCP 15100, UDP 15101) 개방.
    3. 어댑터 속성(`ncpa.cpl`)의 IPv4 고급 설정에서 인터페이스 메트릭(유선 랜 10, Wi-Fi 20)을 수동 지정하여 인터넷 경로와 내부망 경로 분기. [USER VERIFIED]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] **FancyZones의 가치:** PowerToys를 사용하게 된 핵심 이유이자 다중 모니터 환경의 필수 유틸리티. 윈도우 스냅 재정의 옵션을 '상대 위치(`Win + 상하좌우`)'로 변경하여 상하로 나누어 세로 창에서의 사용성을 높임.
* [OPINION] **Color Picker 실무 활용:** `Win + Shift + C` 단축키를 활용하며, 웹 개발용 `HEX`와 유니티(Unity) 게임 엔진 및 셰이더용 `RGB`, `HSL`, `HSV`를 동시에 활성화하여 번거롭고 귀찮은 점을 해소함.
* [OPINION] **Peek(미리보기) 실전 활용:** `Space` 단축키로 호출하며, Visual Studio가 설치되지 않은 환경이나 무거운 IDE를 띄우지 않고 `.cpp` 소스 코드를 빠르게 열람할 때 탁월함. 압축 파일 내부의 폴더 트리 및 용량 파악이 가능하며 방향키로 이전/다음 파일을 연속 탐색할 수 있어 탐색기 기본 뷰어보다 훨씬 쾌적함.
* [OPINION] **Always on Top 활용 변화:** `Win + T`로 단축키를 변경하여 사용. 브라우저 창 고정용으로도 여전히 사용하지만 유튜브를 예전만큼 자주 보지 않게 되었고, Firefox의 자체 PiP 팝업 기능 역시 유튜브 시청 시에만 사용하고 있어 사용 빈도가 줄어듦.
* [OPINION] **Mouse Without Borders 장단점:**
  * 장점: 키보드 자동 동기화, 클립보드 공유, 파일 복사/붙여넣기가 작동함. 방화벽 문제는 전용 규칙 추가 버튼으로 해결 가능.
  * 아쉬운 점: 최신 버전에서도 모니터 상하(2D 그리드) 배치가 불가능하며 좌우 끝으로만 커서 이동이 가능한 1차원 레이아웃 제약이 존재함.
* [OPINION] **OneDrive 5GB 무료 공간을 통한 설정 동기화:** 자체 클라우드 동기화가 없더라도 `.ptb` 백업 파일이 저장되는 기본 문서 폴더를 윈도우 기본 OneDrive로 동기화해 두면, 포맷이나 새 PC 설치 시 일반 설정에서 해당 경로를 지정하고 복원 버튼만 누르면 모든 커스텀 단축키와 그리드 설정이 즉시 복구됨.

---

#### ❓ Missing Info (검증 필요 항목)
* [x] 최신 PowerToys 버전에서 Mouse Without Borders의 다중 모니터 상하(2x2 그리드) 레이아웃 배치 지원 여부 — 최신 버전에서도 미지원 확인(좌우 이동만 가능) — Verified 2026-08-30
* [x] Peek 성능 및 압축 파일 지원 — 바이너리 지연 여부와 무관하게 전용 툴 대비 압축 파일 트리/용량 확인 및 방향키 탐색 지원 확인 — Verified 2026-08-30
* [x] OneDrive 동기화 복원 절차 — 윈도우 기본 OneDrive 연동 후 일반 설정에서 백업 위치 지정 및 복원 버튼 클릭으로 정상 작동 확인 — Verified 2026-08-30

---

#### 🏷️ Tags
PowerToys, Windows11, FancyZones, MouseWithoutBorders, Productivity, Development, Review, ColorPicker, Peek, SystemUtility

---

## 📝 Feedback History

### 2026-08-30 — Test Result: PASS
* **환경:** Windows 11, Dual Network (Wi-Fi 내부망 + Wired LAN 외부망), Multi-Monitor
* **검증된 단계:** 1, 2, 3, 4, 5, 6, 7 전 단계 검증 완료
* **수정 및 반영 사항:**
  - '주요 핵심 유틸리티'를 '개인적으로 자주 사용하는 유틸리티'로 명칭 변경 및 실사용 리뷰 보강
  - 31종 공식 유틸리티 생태계 전체를 개별 항목으로 상세 기술
  - '로컬 백업 아키텍처'를 '로컬 백업 방식'으로 수정하고 `.ptb` 전용 백업 포맷 및 OneDrive 연동 복원 절차 명시
  - 기본 단축키와 실제 커스텀 단축키 매핑 비교 내용을 절차에 반영
  - 듀얼 네트워크 연결 실패 오류에 IPv4 인터페이스 메트릭 수동 변경 절차 추가 및 특수 환경 맥락 명시
  - FancyZones, Color Picker, Always on Top의 주관적 표현을 사용자 진술 사실에 맞춰 수정 (과장된 수식어 및 자의적 유추 제거)
  - 에러 항목에서 Peek 실사용 사례 제외 (PowerToys 자체 에러만 유지)
  - Mouse Without Borders 상하 레이아웃 미지원 및 Peek 압축 파일 뷰어/방향키 탐색 기능 검증 완료
* **Status 변경:** Experimental → Verified

===== KNOWLEDGE PACKAGE END =====
