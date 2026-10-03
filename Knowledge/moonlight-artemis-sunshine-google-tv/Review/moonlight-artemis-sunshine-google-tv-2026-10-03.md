---
topic: moonlight-artemis-sunshine-google-tv
title_kr: Moonlight vs Artemis 및 Sunshine 게임 스트리밍 조합 비교 분석
category: Review
sub_category: Comparison
version: 2026-10-03
status: Verified
created_date: 2026-10-03
last_modified: 2026-10-04
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
* **Status:** Verified
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

##### 3. 다중 모니터(듀얼 모니터) + 가상 디스플레이(3번) 충돌 해결 방안
  * **문제 상황:** 물리 모니터 2개 사용 중 스트리밍 시 가상 디스플레이 3번이 연결되나, Steam Big Picture나 게임이 주 모니터(1번)에 출력되는 현상.
  * **실제 검증된 최적 해결책: Apollo 내장 디스플레이 격리 옵션 활용 [USER VERIFIED]**
    1. Apollo 웹 관리자 (`https://localhost:47990`) 접속
    2. `Configuration` -> `Audio / Video` 탭 진입
    3. `Advanced display device options` 클릭하여 확장
    4. `Device Configuration` 항목을 **`Deactivate other displays and activate only the specified display`** 로 설정
    5. **효과:** 스트리밍 시작 시 물리 모니터(1, 2번)가 일시 비활성화되고 가상 모니터(3번)만 단독 활성화되며, 종료 시 물리 모니터가 자동 복원됨. 주 모니터를 수동 변경할 필요 없이 가장 효용이 높음이 실증됨.

##### 4. 실전 스트리밍 해상도 및 성능 실측 데이터 (Google TV Streamer)
  * **해상도 한계 및 권장치:**
    * 4K/1440p 이상: Google TV Streamer의 하드웨어 디코딩 속도 한계로 인해 조작이 어려운 수준의 심각한 지연 발생.
    * **1080p (Full HD):** 안정적으로 플레이 가능한 마지노선이자 최적 해상도.
  * **게임별 실측 데이터 (HEVC Low-Latency 코덱 기준):**
    1. **엘든 링 (Elden Ring):**
       - 스트림: 1920x1080, 54.32 FPS (렌더링 51.85 FPS)
       - 평균 디코딩 시간: **4.96 ms**
       - 평균 네트워크 지연: **5 ms** (편차 7 ms)
       - 호스트 처리 대기 시간: 최소 2.1 ms / 최대 4.1 ms / 평균 3.1 ms
       - 패킷 손실: 0.00%
    2. **림월드 (RimWorld):**
       - 스트림: 1920x1080, 41.67 FPS (렌더링 41.67 FPS)
       - 평균 디코딩 시간: **4.29 ms**
       - 평균 네트워크 지연: **2 ms** (편차 0 ms)
       - 호스트 처리 대기 시간: 최소 2.0 ms / 최대 2.9 ms / 평균 2.3 ms
       - 패킷 손실: 0.00%
    3. **볼핏 (Ball Pit):**
       - 스트림: 1920x1080, 119.48 FPS (렌더링 119.48 FPS, 고주사율 모드)
       - 평균 디코딩 시간: **5.00 ms**
       - 평균 네트워크 지연: **1 ms** (편차 0 ms)
       - 호스트 처리 대기 시간: 최소 2.1 ms / 최대 3.5 ms / 평균 2.6 ms
       - 패킷 손실: 0.00%
  * **디스플레이 지연 관련:**
    - 빔프로젝터 자체 저지연(게임) 모드가 정상 활성화되어 있어 Google TV Streamer ↔ 빔프로젝터 간 지연 병목은 발생하지 않음.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **가상 디스플레이(3번) 연결 시 Steam/게임이 1번 물리 모니터에 실행되는 문제** *(사용자 검증 추가 — 2026-10-03)*
    * 원인: Windows의 주 모니터(Primary Display) 설정이 1번으로 유지되어 있어, 전체 화면 게임 및 Steam Big Picture가 기본 디스플레이로 열림.
    * 해결법: Apollo 설정의 `Device Configuration`에서 `Deactivate other displays and activate only the specified display`를 적용하여 물리 듀얼 모니터를 비활성화하고 가상 모니터만 단독 구동. [USER VERIFIED]
    * 환경: Windows 11/10, 물리 듀얼 모니터(1440p) + 가상 디스플레이(Apollo SudoVDA)
    * 신뢰도: [★★★★★]
  * **Google TV Streamer에서 1440p/4K 스트리밍 시 조작 불가 수준의 입력 지연 및 스터터링 발생** *(사용자 검증 추가 — 2026-10-04)*
    * 원인: Google TV Streamer의 AP(MediaTek칩) 하드웨어 비디오 디코더 성능 한계로 1440p 이상 고해상도 프레임 디코딩 지연 누적.
    * 해결법: 스트리밍 해상도를 **1080p**로 고정하고 코덱을 `c2.mtk.hevc.decoder.lowlatency`로 구동. 1080p 설정 시 디코딩 시간 4~5ms, 네트워크 지연 1~5ms 수준으로 쾌적한 플레이 가능. [USER VERIFIED]
    * 신뢰도: [★★★★★]
  * **Artemis 클라이언트 설치 파일 접근 제한**
    * 원인: Google TV 플레이스토어에 Artemis가 등록되어 있지 않음.
    * 해결법: Send Files to TV 또는 USB 파일 관리자 앱을 사용하여 GitHub 릴리스 APK를 다운로드한 후 개발자 옵션 허용 상태에서 사이드로딩 설치. [FACT]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] PC 모니터가 1440p 144Hz이고 빔프로젝터가 4K/1440p를 지원하더라도, Google TV Streamer를 클라이언트로 쓸 때는 디코딩 성능 한계로 인해 **1080p로 스트리밍하는 것이 필수적**입니다. 1080p 환경에서는 60FPS(엘든 링)뿐 아니라 120FPS(볼핏 등 경량 게임)까지도 디코딩 지연 4~5ms 내외로 안정적으로 소화합니다.
  * [OPINION] 듀얼 모니터를 쓰는 환경에서는 Apollo의 `Deactivate other displays and activate only the specified display` 옵션이 가장 완벽한 해법입니다. 복잡한 윈도우 주 모니터 재배치나 서드파티 스크립트 없이도 물리 모니터 2개가 깔끔히 꺼지고 가상 화면 3번에 스팀이 단독 실행됩니다.
  * [OPINION] 빔프로젝터의 게임 모드(저지연 모드)를 켜두면 스트리머와 프로젝터 간 HDMI 전송 지연이 억제되므로, 순수 Moonlight 오버레이 지연(합산 10ms 안팎) 수준의 민첩한 응답성을 체감할 수 있습니다.

---

#### ❓ Missing Info (검증 필요 항목)
  * [x] 사용자 PC의 물리 모니터 해상도 및 빔프로젝터 해상도/화면비 일치 여부 — Verified 2026-10-03 (PC: 1440p 144Hz, 프로젝터: 4K 60Hz / 1440p 120Hz)
  * [x] Google TV Streamer의 연결 방식 — Verified 2026-10-03 (5GHz Wi-Fi)
  * [x] 최종 사용 조합 확정 — Verified 2026-10-03 (Moonlight + Apollo)
  * [x] 3번 가상 디스플레이 단독 출력 설정 — Verified 2026-10-04 (Apollo Deactivate other displays 적용 완료)
  * [x] 해상도 한계 및 실제 게임별 레이턴시 실측 — Verified 2026-10-04 (1080p 최적, 엘든 링/림월드/볼핏 디코딩 4~5ms 검증)
  * [x] 빔프로젝터 저지연 모드 동작 여부 — Verified 2026-10-04 (저지연 모드 활성화로 정상 구동 확인)

---

#### 🏷️ Tags
Moonlight, Artemis, Sunshine, Apollo, GoogleTVStreamer, GameStreaming, Projector, StreamingReview, EldenRing, RimWorld, BallPit

===== KNOWLEDGE PACKAGE END =====

---
## 📝 Feedback History

### 2026-10-04 — Test Result: PASS
* **환경:**
  * Host PC: 물리 듀얼 모니터 (1440p 144Hz)
  * Client: Google TV Streamer (5GHz Wi-Fi)
  * Display: 빔프로젝터 (저지연/게임 모드 활성화)
  * 선택 조합: Moonlight(Client) + Apollo(Host)
* **검증/확인된 항목:**
  * Apollo 설정의 `Deactivate other displays and activate only the specified display` 적용으로 물리 모니터 비활성화 및 가상 모니터 3번 단독 출력 완벽 동작 확인.
  * Google TV Streamer의 디코딩 한계로 1440p/4K는 실사용 불가 수준의 지연 발생, **1080p가 최적 해상도**임을 확인.
  * 1080p 환경 실측 결과 (HEVC Low-Latency 디코더 `c2.mtk.hevc.decoder.lowlatency`):
    * 엘든 링: 1080p ~54 FPS, 디코딩 4.96 ms, 네트워크 지연 5 ms
    * 림월드: 1080p ~41.7 FPS, 디코딩 4.29 ms, 네트워크 지연 2 ms
    * 볼핏: 1080p ~119.5 FPS, 디코딩 5.00 ms, 네트워크 지연 1 ms
  * 빔프로젝터 저지연 모드 적용으로 클라이언트-디스플레이 간 병목 해소 확인.
* **Status 변경:** Verified (Partial) → Verified

### 2026-10-03 — Test Result: PARTIAL
* **환경:**
  * Host PC: 물리 듀얼 모니터 (1440p 144Hz)
  * Client: Google TV Streamer (5GHz Wi-Fi)
  * Display: 빔프로젝터 (4K 60Hz, 1440p 120Hz 지원)
  * 선택 조합: Moonlight(Client) + Apollo(Host)
* **검증/확인된 항목:**
  * 최종 조합으로 Moonlight + Apollo 채택 확인.
  * 듀얼 모니터 환경에서 가상 디스플레이(3번) 추가 시 Steam이 1번 모니터로 출력되는 이슈 제기.
  * 해결책 수립: Apollo의 `Deactivate other displays` 기능 또는 Windows 상에서 3번 디스플레이를 주 모니터(Primary Display)로 지정/분리 설정.
* **진행 대기 항목:**
  * 3번 가상 디스플레이 단독 출력 설정 적용 및 빔프로젝터 실제 레이턴시 테스트.
* **Status 변경:** Verified (Partial) 유지
