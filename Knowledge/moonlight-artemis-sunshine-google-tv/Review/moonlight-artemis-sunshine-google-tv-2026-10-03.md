---
topic: moonlight-artemis-sunshine-google-tv
title_kr: Moonlight vs Artemis 및 Sunshine 게임 스트리밍 조합 비교 분석
category: Review
sub_category: Comparison
version: 2026-10-03
status: Verified (Partial)
created_date: 2026-10-03
last_modified: 2026-10-03
language: KR+EN
tags: [Moonlight, Artemis, Sunshine, Apollo, GoogleTVStreamer, GameStreaming, Projector]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** moonlight-artemis-sunshine-google-tv
* **Title_KR:** Moonlight vs Artemis 및 Sunshine 게임 스트리밍 조합 비교 분석
* **Category:** Review
* **Sub-Category:** Comparison
* **Version:** 2026-10-03
* **Status:** Verified (Partial)
* **Date:** 2026-10-03
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] GitHub Official (Moonlight): https://github.com/moonlight-stream/moonlight-android
  * [★★★★★] GitHub Official (Sunshine): https://github.com/LizardByte/Sunshine
  * [★★★★★] GitHub Official (Apollo Host): https://github.com/ClassicOldSong/Apollo
  * [★★★★★] GitHub Official (Artemis Client): https://github.com/ClassicOldSong/moonlight-android
  * [★★★★☆] Community Benchmarks & Google TV Streamer Analysis: Reddit /r/MoonlightStreaming

---

#### 🔑 Core Concepts (핵심 개념)
  * **Sunshine (호스트):** 오픈소스 게임 스트리밍 서버(NVIDIA GameStream 대체). 다양한 OS를 지원하며 세부 인코더 설정 및 커스텀 커맨드(해상도 변경 스크립트 등) 작성을 지원함. [FACT]
  * **Apollo (호스트):** ClassicOldSong이 유지관리하는 Sunshine의 포크 버전. 가상 디스플레이 드라이버(Windows SudoVDA 기반)가 내장되어 클라이언트 해상도/주사율에 맞춘 자동 전환 및 PnP 가상 모니터 생성을 기본 제공함. [FACT]
  * **Moonlight (클라이언트):** 표준 오픈소스 게임 스트리밍 클라이언트. 구글 플레이스토어에서 정식 설치가 가능하며 장기간 검증된 안정성과 넓은 디바이스 호환성을 가짐. [FACT]
  * **Artemis (클라이언트):** ClassicOldSong이 유지관리하는 Moonlight Android 포크 버전(구 Moonlight Noir). Apollo와의 연동 설정, 세부 디코더 파라미터 제어 및 모바일/TV 최적화 기능이 포함되어 있음. [FACT]
  * **Google TV Streamer 스트리밍 특성:** 4K 60fps 디코딩 시 약 16~21ms 내외의 디코딩 지연이 보고되며, 80~100Mbps 이상의 과도한 비트레이트 설정 시 하드웨어 디코더 과부하로 인한 스터터링(화면 밀림) 위험이 있음. 유선 이더넷 환경 권장. [FACT]

---

#### 🛠️ Procedures (비교 및 추천 분석)

##### 1. 구성 조합 비교
  * **조합 A: 표준 조합 (Moonlight + Sunshine)**
    * **호스트:** Sunshine
    * **클라이언트:** Moonlight (Google TV Streamer 플레이스토어에서 직접 설치 가능)
    * **장점:** 공식 릴리스 및 검증된 안정성, 플레이스토어를 통한 쉬운 설치 및 자동 업데이트, 빔프로젝터가 주 디스플레이이거나 서브 모니터 환경인 경우 안정적 구동.
    * **단점:** 클라이언트와 호스트 모니터 간 해상도/주사율이 다를 경우(예: PC 모니터 QHD 144Hz vs 빔프로젝터 4K 60Hz / 1440p 120Hz), 별도의 가상 디스플레이 드라이버(Virtual Display Driver)나 qres/ChangeScreen 스크립트를 Sunshine 커스텀 훅에 직접 구성해야 함.
  * **조합 B: 통합 최적화 조합 (Artemis + Sunshine/Apollo)**
    * **호스트:** Sunshine (또는 가상 디스플레이 일체형 Apollo)
    * **클라이언트:** Artemis (APK 사이드로딩 필요)
    * **장점:** Artemis는 세부 디코더 옵션 및 Apollo 연동 기능을 제공하며, Apollo와 함께 쓸 경우 가상 디스플레이 생성이 플러그앤플레이 형태로 자동 처리됨.
    * **단점:** 안드로이드 TV 환경에서 APK 직접 추출/사이드로딩(다운로더 앱 또는 ADB 등) 과정 필요. 비공식 포크 특성상 업데이트 관리 수동.
  * **조합 C: 하이브리드 추천 조합 (Moonlight + Apollo)**
    * **호스트:** Apollo (Sunshine 포크, PC 설치)
    * **클라이언트:** Moonlight (Google TV Streamer 공식 플레이스토어 설치)
    * **장점:** 호스트의 가상 디스플레이(SudoVDA) 자동 생성/해제 기능의 편리함을 그대로 누리면서, Google TV Streamer 쪽에는 번거로운 사이드로딩 없이 플레이스토어 공식 Moonlight 앱을 그대로 사용 가능. 현재 사용자 환경(PC 1440p 144Hz vs 빔프로젝터 4K 60Hz/1440p 120Hz)에 최적의 밸런스.
    * **단점:** 기존에 수동 설치한 다른 가상 디스플레이 드라이버(VDD 등)가 있다면 충돌 방지를 위해 사전 삭제 필요.

##### 2. PC + 빔프로젝터 + Google TV Streamer 환경 추천 가이드
  * **현재 사용자 환경 특성:**
    * PC 모니터: 1440p 144Hz
    * 빔프로젝터: 4K 60Hz, 1440p 120Hz 지원
    * 스트리머 네트워크: 5GHz Wi-Fi
    * 게임 모드: 미설정 (테스트 예정)
  * **최종 추천:** **Moonlight(클라이언트) + Apollo(호스트)**
    * **이유:** PC 모니터와 빔프로젝터 간 해상도/주사율 불일치가 존재하므로 가상 모니터 자동 생성이 필수적임. 호스트에 Apollo를 설치하면 스트리밍 시작 시 빔프로젝터가 요구하는 해상도/주사율(4K 60Hz 또는 1440p 120Hz)에 맞춘 PnP 가상 모니터를 자동 생성해 줌. 반면 클라이언트(Google TV Streamer)에는 Artemis를 사이드로딩할 필요 없이 공식 Moonlight 앱만으로도 완벽하게 연동되므로 설치 및 유지보수가 가장 편리함.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **Google TV Streamer에서 4K 스트리밍 시 스터터링/입력 지연 발생**
    * 원인: 비트레이트 과다 설정(100Mbps 초과) 시 디코더 버퍼 병목 또는 빔프로젝터 화면 처리 지연(영상 보정 기능 활성화).
    * 해결법: Moonlight/Artemis 설정에서 비트레이트를 60~80Mbps 수준으로 조정, 코덱을 HEVC(H.265)로 고정, 빔프로젝터 입력 모드를 '게임 모드(Game Mode / 저지연 모드)'로 설정. [FACT]
    * 신뢰도: [★★★★☆]
  * **Artemis 클라이언트 설치 파일 접근 제한**
    * 원인: Google TV 플레이스토어에 Artemis가 등록되어 있지 않음.
    * 해결법: Send Files to TV 또는 USB 파일 관리자 앱을 사용하여 GitHub 릴리스 APK를 다운로드한 후 개발자 옵션 허용 상태에서 사이드로딩 설치. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] PC 모니터가 1440p 144Hz이고 빔프로젝터가 4K 60Hz / 1440p 120Hz인 환경에서는 Apollo 호스트의 가상 디스플레이 자동 매칭이 매우 효과적입니다. 물리 모니터를 끄거나 해상도 강제 복제 없이 독립된 스트리밍 전용 가상 화면을 띄울 수 있습니다.
  * [OPINION] 5GHz Wi-Fi 환경에서는 4K 60fps 전송 시 순간적인 지연 스파이크(Jitter)가 발생할 수 있으므로, 비트레이트는 50~70Mbps 전후로 시작하여 점진적으로 조정하는 것을 권장합니다.
  * [OPINION] 빔프로젝터는 자체 이미지 후처리 엔진(MEMC 등)으로 인해 기본 입력 지연이 40~70ms에 달할 수 있으므로, 테스트 시 빔프로젝터의 게임 모드(저지연 모드) 활성화가 체감 반응성에 결정적인 역할을 합니다.

---

#### ❓ Missing Info (검증 필요 항목)
  * [x] 사용자 PC의 물리 모니터 해상도 및 빔프로젝터 해상도/화면비 일치 여부 — Verified 2026-10-03 (PC: 1440p 144Hz, 프로젝터: 4K 60Hz / 1440p 120Hz)
  * [x] Google TV Streamer의 연결 방식 — Verified 2026-10-03 (5GHz Wi-Fi)
  * [ ] 빔프로젝터 자체 게임 모드(저지연 모드) 적용 후 실제 체감 레이턴시 측정 결과
  * [ ] 사용자가 최종 선택한 조합 구동 결과 및 테스트 피드백

---

#### 🏷️ Tags
Moonlight, Artemis, Sunshine, Apollo, GoogleTVStreamer, GameStreaming, Projector, StreamingReview

===== KNOWLEDGE PACKAGE END =====

---
## 📝 Feedback History

### 2026-10-03 — Test Result: PARTIAL
* **환경:**
  * Host PC: 1440p 144Hz 물리 모니터
  * Client: Google TV Streamer (5GHz Wi-Fi)
  * Display: 빔프로젝터 (4K 60Hz, 1440p 120Hz 지원)
* **검증/확인된 항목:**
  * 모니터와 프로젝터 해상도/주사율 차이로 가상 디스플레이(Virtual Display) 필요성 확인.
  * 조합 C(Moonlight + Apollo) 옵션 타당성 분석 및 추가: 호스트 가상 디스플레이 자동화 + 클라이언트 공식 플레이스토어 Moonlight 조합이 최적의 밸런스임을 확인.
* **진행 대기 항목:**
  * 빔프로젝터 게임 모드 세팅 및 실제 스트리밍 레이턴시 테스트.
* **Status 변경:** Experimental → Verified (Partial)
