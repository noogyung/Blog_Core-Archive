---
topic: bazzite-deck-emudeck-retro-os
title_kr: "ASRock DeskMini 기반 Bazzite-Deck + EmuDeck/ES-DE 스팀·레트로 통합 콘솔 OS 구축 기록"
category: Workflow
sub_category: OS-Emulation-Setup
version: 2026-11-12
status: Verified (Partial)
created_date: 2026-11-12
last_modified: 2026-11-14
language: KR+EN
tags: [Bazzite, Bazzite-Deck, EmuDeck, ES-DE, RetroArch, DeskMini, WinSCP, Linux]
sources_count: 6
blog_draft_path: Blog_Posts/2026-11/bazzite-deck-emudeck-retro-os.html
blog_draft_date: 2026-11-14
blog_id: core-archive
blog_published: true
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** bazzite-deck-emudeck-retro-os
* **Title_KR:** ASRock DeskMini 기반 Bazzite-Deck + EmuDeck/ES-DE 스팀·레트로 통합 콘솔 OS 구축 기록
* **Category:** Workflow
* **Sub-Category:** OS-Emulation-Setup
* **Version:** 2026-11-12
* **Status:** Verified (Partial)
* **Date:** 2026-11-12
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Bazzite 공식 문서 (bazzite-deck, Bazzite Portal, ujust, rpm-ostree rebase): https://docs.bazzite.gg/
  * [★★★★★] EmuDeck 공식 문서 및 GitHub 저장소 (Linux/SteamOS 설치 및 BIOS/ROM 구조): https://www.emudeck.com/ , https://github.com/dragoonDorise/EmuDeck
  * [★★★★★] ES-DE (EmulationStation Desktop Edition) 공식 문서 (`.m3u` 다중 디스크, `noload.txt`, `gamelist.xml`, 리눅스 대소문자 구분): https://gitlab.com/es-de/emulationstation-de
  * [★★★★★] Libretro / RetroArch 공식 문서 (SwanStation `.m3u` 상대 경로 및 MAME 2003-Plus 코어): https://docs.libretro.com/library/mame_2003_plus/
  * [★★★★★] NetworkManager 공식 문서 (`Shared to other computers` 기본 서브넷 `10.42.0.1`): https://networkmanager.dev/
  * [★★★★★] WinSCP 공식 문서 (파일 마스크 대괄호 `[]` 이스케이프 및 `lcd`/`cd`/`rm` 스크립팅): https://winscp.net/eng/docs/file_mask

---

#### 🔑 Core Concepts (핵심 개념)
  * **Bazzite 공식 이미지 구분 및 Steam Gaming Mode 선택 기준 [FACT / USER VERIFIED]:**
    * Bazzite 공식 웹사이트 다운로드 항목은 `Desktop`, `Home Theater`, `Handheld`로 나뉘며, SteamOS와 동일한 콘솔 인터페이스(Gaming Mode)를 기본 구동하려면 `Home Theater`(또는 Handheld) 계열을 선택해야 한다.
    * 이미지 파일명이 반드시 **`bazzite-deck`**으로 시작하는지 확인해야 하며, 일반 데스크톱용 이미지(`bazzite:stable` 등)에는 `deck` 명칭이 없고 Gaming Mode로 부팅되지 않는다.
    * `rpm-ostree rebase` 명령으로 이미지 스트림을 전환할 수도 있으나, 초기 구성 정리를 위해 `bazzite-deck-stable-amd64.iso` 클린 설치를 선택하는 편이 명확하다.
  * **Bazzite의 `ujust` EmuDeck 설치 명령어 사실 [FACT / USER VERIFIED]:**
    * Bazzite 터미널에서 EmuDeck 설치를 호출하는 실제 명령어는 **`ujust get-emudeck`**이다.
    * AI 모델이 과거 정보나 추론을 바탕으로 `ujust install-emudeck`으로 안내하는 경우가 많으므로, AI 응답을 맹신하지 않고 터미널의 `ujust` 명령어 목록이나 최신 스크립트 레시피를 직접 대조해야 한다.
  * **EmuDeck 설치 방식에 따른 디렉터리 경로 차이 주의점 [FACT / USER VERIFIED]:**
    * EmuDeck 공식 `install.sh` 스크립트(`curl -L https://raw.githubusercontent.com/dragoonDorise/EmuDeck/main/install.sh | bash`)로 수동 설치할 경우, Bazzite 기본 `ujust get-emudeck` 설치 경로와 차이가 발생할 수 있다(`~/Emulation/roms` vs `~/emudeck/roms` 등).
    * 따라서 롬 및 BIOS를 전송하기 전에 실제 생성된 홈 디렉터리의 에뮬레이션 폴더 구조를 먼저 확인해야 한다.
  * **ES-DE 다중 디스크(`.chd` / `.m3u`) 분리 및 `noload.txt` 필수 적용 [FACT / USER VERIFIED]:**
    * ES-DE는 하위 디렉터리도 게임 항목으로 탐색하므로, `.chd` 파일을 단순히 `.hidden`과 같은 리눅스 숨김 폴더에 넣더라도 에뮬레이터 UI 목록에 그대로 노출된다.
    * 다중 디스크 중복을 완전히 숨기기 위해서는 `.chd` 파일들을 별도 하위 폴더(예: `chd/`)로 분리하고 `.m3u` 파일의 참조 경로를 수정한 후, **해당 분리 폴더 내부에 반드시 빈 텍스트 파일인 `noload.txt`를 생성해 배치**해야 한다.
    * 리눅스 상에서도 보이지 않는 `.hidden` 폴더는 파일 탐색 및 유지보수가 번거로우므로, 식별 가능한 일반 폴더명(`chd/`)에 `noload.txt`를 조합하는 방식이 관리상 유리하다. (현재 시스템은 `.hidden` 폴더 내부에 `noload.txt`를 배치해 정상 은닉 처리함).
    * RetroArch SwanStation 코어는 `.m3u` 내부 경로 작성 시 **슬래시(`/`) 구분자**와 **UTF-8(BOM 없음)** 인코딩을 요구한다.
  * **롬 파일 확장자 인식 규칙과 에뮬레이터 코어 정의 [FACT / USER VERIFIED]:**
    * 롬 파일의 확장자 대소문자(`.GBA` vs `.gba`) 인식은 리눅스 파일시스템 자체의 문제라기보다는, ES-DE 및 각 에뮬레이터 코어가 시스템 폴더(`genesis`, `mame` 등)별로 정의한 지원 확장자 파싱 규칙에 의해 결정된다.
    * 실제 테스트 결과 `genesis` 등에서 대문자 확장자도 정상 인식되었으며, 실행 실패 시에는 개별 게임 메타데이터 설정에서 코어를 변경해 대응할 수 있다.
  * **NetworkManager 유선 직결 공유(`10.42.0.1`)와 전송 전략 [FACT / USER VERIFIED]:**
    * 초기 수백 기가바이트(약 300GB) 단위의 대규모 롬셋을 일괄 전송할 때는 대역폭 확보를 위해 유선 직결(`10.42.0.1`) SFTP 전송이 적합하다.
    * 구축 완료 후 일상적인 소량 롬 추가나 메타데이터 관리는 내부 Wi-Fi 로컬 네트워크 SFTP를 사용하는 것이 배선 연결 측면에서 편리하다.

---

#### 🛠️ Procedures (절차)

##### 1. 하드웨어·BIOS 및 Bazzite-Deck OS 기반 구축 [USER REPORTED] ✅ Verified 2026-11-14
  1. 대상 하드웨어(ASRock DeskMini, Ryzen 5 2400G, DDR4 32GB, 500GB NVMe SSD)의 BIOS에서 Secure Boot를 해제하고, XMP/AHCI 설정 및 내장 GPU 할당 메모리(`UMA Frame Buffer`)를 `8G`로 설정한다.
  2. Bazzite 공식 사이트의 `Home Theater` 분류에서 다운로드 이미지명이 **`bazzite-deck`**으로 시작하는지 확인하고 `bazzite-deck-stable-amd64.iso`로 클린 설치하여 Steam Gaming Mode 환경을 구성한다.
  3. `Bazzite Portal`에서 필요한 항목(`Decky Loader`, `Bazzite Buddy`, `Decky Framegen`, `Decky Lossless Scaling`)만 선택해 설치한다(`Sunshine`, `Waydroid`, `OpenRGB`, `Wootility`, `Resilio Sync` 제외).

##### 2. EmuDeck 및 ES-DE 설치 [FACT + USER REPORTED] ✅ Verified 2026-11-14
  1. Bazzite 터미널에서 EmuDeck 설치 시에는 `ujust get-emudeck` 명령어를 실행한다. ⚠️ 수정됨: AI가 `ujust install-emudeck`으로 잘못 안내하는 사례가 있으므로 주의할 것. 만약 `ujust` 대신 EmuDeck 공식 `install.sh`(`curl -L https://raw.githubusercontent.com/dragoonDorise/EmuDeck/main/install.sh | bash`)로 수동 설치했다면 생성된 폴더 트리(`~/Emulation/roms` 또는 `~/emudeck/roms`)를 직접 확인한다. — Verified 2026-11-14
  2. EmuDeck 마법사에서 `Easy Mode`, `Internal Storage`, `ES-DE` 통합(`Epic Noir` 테마)을 선택한다.
  3. ES-DE에서 불필요한 `Emulators` 메뉴 노출을 숨기려면 해당 폴더 경로에 빈 `noload.txt` 파일을 배치한다.

##### 3. PC↔DeskMini 1차 유선 직결 전송 및 한글화 전략 [FACT + USER REPORTED] ✅ Verified 2026-11-14
  1. 초기 대용량(300GB) 영문 통합 롬셋 및 BIOS 묶음은 속도 확보를 위해 DeskMini의 유선 연결 IPv4를 `Shared to other computers`(`10.42.0.1`)로 설정하여 SFTP(포트 22, WinSCP)로 직결 전송한다. ⚠️ 향후 소량 전송은 Wi-Fi 내부망 활용 예정. — Verified 2026-11-14
  2. 실시간 OCR 번역은 구동 시 최소 1초 이상의 대기 지연이 발생하므로 보류하고, 사전 한글 패치 롬셋 교체 및 ES-DE `gamelist.xml`의 게임 설명(Description) 한글화 방식을 채택한다. (실시간 번역은 추후 메모리 후킹 등의 대안 구상 단계). — Verified 2026-11-14
  3. PC에 영문 원본 전량을 백업으로 유지한 상태에서 저용량 4개 기종(`nes`, `snes`, `megadrive`, `gba` 등 334개)을 1차 업로드한다.

##### 4. 스크립트 한계 대응 및 DeskMini 현지 Antigravity 정리 [USER VERIFIED] ✅ Verified 2026-11-14
  1. PC에서 AI가 작성한 와일드카드 기반 삭제 스크립트를 실행했으나, 롬 제작자별 파일명 표기 규칙 차이(영문명, 일문 음차, 한글 표기, 부제목, 지역 태그 불일치 등)로 인해 22건 실패 및 3건 오삭제(`Contra - Hard Corps`, `Landstalker`, `Pokemon - Sapphire Version`)가 발생했다.
  2. 오삭제된 3종은 PC 원본 백업에서 정상 복원 완료했다.
  3. 원격 스크립트 전송의 한계를 극복하기 위해 DeskMini 현지에 Antigravity 에이전트를 직접 설치하여 로컬에서 직접 제어하며 다음을 수행했다:
     - 중복 롬 완전 정리.
     - PS1 `.chd` 파일들을 분리 폴더(`.hidden` 또는 `chd/`)로 이동하고, `.m3u` 내부 참조 링크를 상대 경로로 일괄 수정.
     - 분리 폴더 내부에 빈 `noload.txt` 파일을 배치하여 ES-DE 에뮬레이터 목록에서 중복 노출 차단.
     - `gamelist.xml`의 게임 설명 부분 한글화 적용.

##### 5. 런타임 코어 변경 및 아트워크 스크래핑 [USER VERIFIED] ✅ Verified 2026-11-14
  1. MAME 2003-Plus 코어에서 게임 실행이 실패할 경우, ES-DE 목록 화면에서 개별 게임 메뉴(`Edit Game Metadata` -> `Alternative Emulator`)를 통해 다른 코어로 교체하여 정상 구동한다.
  2. ScreenScraper 일일 API 호출 한도 초과 시 한도가 리셋될 때까지 대기한 후 재수집을 진행하여, 영문명으로 등록된 대부분의 아트워크를 정상 수집했다. (일본명/한국명으로 표기된 롬은 파일명을 인위적으로 수정하지 않고 스크래핑 미적용 상태로 유지).

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] Bazzite `ujust` EmuDeck 설치 명령어 혼선 (`install-emudeck` vs `get-emudeck`)** *(사용자 검증 추가 — 2026-11-14)*
    * 원인: AI 모델이 `ujust install-emudeck` 명령어를 고집했으나, Bazzite 시스템에 실제 정의된 명령어는 `ujust get-emudeck`이었다.
    * 해결법: 터미널에서 `ujust get-emudeck`을 실행하거나 EmuDeck 공식 `install.sh` 스크립트를 직접 실행한다. 공식 스크립트 수동 설치 시에는 생성된 롬/BIOS 경로(`~/Emulation/roms` 등)를 확인해야 한다. [USER VERIFIED]
    * 환경: Bazzite-Deck, Bash Terminal
    * 신뢰도: [★★★★★]
  * **[오류 2] Bazzite 이미지 다운로드 시 Desktop 모드 오설치** *(사용자 검증 추가 — 2026-11-14)*
    * 원인: 다운로드 페이지의 3개 옵션(Desktop, Home Theater, Handheld) 중 Desktop을 선택하면 Gaming Mode(SteamOS 전용 콘솔 UI)가 포함되지 않은 일반 리눅스로 부팅된다.
    * 해결법: Home Theater 또는 Handheld를 선택하고, 파일명에 반드시 `bazzite-deck`이 포함된 ISO 이미지를 받아 클린 설치한다. [USER VERIFIED]
    * 환경: ASRock DeskMini A300/X300 (Ryzen 5 2400G)
    * 신뢰도: [★★★★★]
  * **[오류 3] 와일드카드 롬 삭제 스크립트의 오삭제 및 미삭제** *(사용자 검증 추가 — 2026-11-14)*
    * 원인: 롬 파일명에 영문, 일문 음차, 한글, 부제목, 대괄호 태그 등이 비표준적으로 기입되어 단순 와일드카드 규칙 매칭에서 예외가 발생했다.
    * 해결법: 오삭제된 3종은 PC 원본 백업을 통해 복원 완료했으며, 복잡한 롬 정리는 원격 배치 스크립트 대신 DeskMini 현지에 Antigravity 에이전트를 직접 설치해 로컬 파일시스템에서 대조·정리했다. [USER VERIFIED]
    * 환경: Windows PC ↔ Bazzite SFTP
    * 신뢰도: [★★★★★]
  * **[오류 4] `.chd`를 숨김 폴더(`.hidden`)로 이동했으나 ES-DE에 중복 노출되는 문제** *(사용자 검증 추가 — 2026-11-14)*
    * 원인: ES-DE는 리눅스 숨김 폴더(`.hidden`)라도 롬 폴더 트리에 속해 있으면 폴더 자체를 게임 항목으로 탐색하여 목록에 표시한다.
    * 해결법: 분리된 폴더 내부에 반드시 빈 텍스트 파일인 `noload.txt`를 생성해 넣음으로써 ES-DE 스캐너가 해당 폴더의 파싱을 건너뛰도록 지정했다. [USER VERIFIED]
    * 환경: ES-DE, RetroArch SwanStation
    * 신뢰도: [★★★★★]
  * **[오류 5] DeskMini 무선 연결 불안정 및 컨트롤러 간헐적 끊김** *(사용자 검증 추가 — 2026-11-14)*
    * 원인: DeskMini 내부 M.2 Wi-Fi/BT 모듈을 교체하지 않아 2.4GHz Wi-Fi로 연결되어 있고, 내장 Bluetooth 대역 간섭 등으로 IINE 컨트롤러 사용 시 간헐적 연결 끊김이 발생했다.
    * 해결법: 추후 Wi-Fi 6E/BT 5.x M.2 모듈 교체 및 단자함 공유기 배선 작업을 진행할 예정이며, 현재는 간헐적 끊김 상태를 유지 중이다. [USER REPORTED]
    * 환경: DeskMini 내장 무선 모듈, 2.4GHz Wi-Fi, IINE Bluetooth Controller
    * 신뢰도: [★★★★☆]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] AI에게 비표준 파일명(롬 파일셋)의 일괄 삭제/교체 스크립트를 전적으로 맡기면 오삭제/미삭제가 필연적으로 발생하므로, 백업을 반드시 유지하고 기기 현지(Bazzite)에서 직접 검증하는 편이 안전하다. — 출처: 사용자 구축 세션 검증
  * [OPINION] ES-DE 다중 디스크 정리 시 리눅스 숨김 폴더(`.hidden`)는 파일 브라우징만 번거로워지므로, 일반 폴더(`chd/`)를 만들고 내부에 `noload.txt`를 넣어 에뮬레이터에서만 제외시키는 편이 장기적인 유지보수에 유리하다. — 출처: 사용자 구축 세션 검증
  * [FACT] 대용량 초기 롬 이전(300GB)에는 유선 직결(`10.42.0.1`) SFTP가 효율적이며, 이후 소량 업데이트는 Wi-Fi 로컬 네트워크 SFTP로도 충분하다. — 출처: 사용자 구축 세션 검증
  * [FACT] MAME 2003-Plus 코어에서 구동되지 않는 아케이드 게임은 ES-DE 게임별 상세 메타데이터 메뉴에서 다른 에뮬레이터 코어를 개별 지정하여 해결할 수 있다. — 출처: 사용자 구축 세션 검증

---

#### ❓ Missing Info (검증 필요 항목)
  * [x] **"최종 롬 파일들의 한글화" 구체 범위 확인:** `gamelist.xml`의 게임 설명(Description) 부분만 한글화 적용 완료. 영문 미등록 등으로 스크래핑/다운로드 실패한 롬은 파일명을 강제로 바꾸지 않고 원형 그대로 유지. — Verified 2026-11-14
  * [x] **PS1 `.m3u`/`.chd` 실구동 및 디스크 교체 검증:** `.m3u` 정상 부팅 및 인게임 디스크 교체(Disc Control) 정상 동작 확인. `.chd` 분리 폴더 내 `noload.txt` 추가로 중복 노출 차단 확인. — Verified 2026-11-14
  * [x] **오삭제 3종 복구 및 다운로드 실패 13종 반영 여부 확인:** 오삭제 3종은 원본 백업을 통해 복원 완료. 다운로드 실패분은 파일명 수정 없이 미반영 상태로 유지. — Verified 2026-11-14
  * [x] **`genesis`/`megadrive` 대문자 확장자(`.GBA`) 및 MAME 코어 확인:** 대문자 확장자 정상 인식 확인(에뮬레이터 확장자 규칙 기반). MAME 2003-Plus 코어 오류 시 개별 코어 선택으로 해결. — Verified 2026-11-14
  * [x] **아트워크 스크래핑 완료 여부:** ScreenScraper 일일 한도 리셋 후 재시도하여 일본명/한국명 롬 일부를 제외한 대부분 정상 수집 완료. — Verified 2026-11-14
  * [ ] **DeskMini Wi-Fi/BT 모듈 교체 및 공유기 유선 배선 작업:** 현재 2.4GHz Wi-Fi 및 내장 BT 사용 중이며 IINE 컨트롤러 간헐적 끊김 현상 발생. 모듈 교체 및 단자함 배선 후 안정성 검증 필요.
  * [ ] **인게임 실시간 번역 구현 방식 검증:** 실시간 OCR의 1초 이상 지연 문제로 인해 메모리 후킹 또는 롬 내부 텍스트 패치 방식의 타당성 및 구현 테스트 필요.

---

#### 🏷️ Tags
Bazzite, Bazzite-Deck, EmuDeck, ES-DE, RetroArch, SwanStation, DeskMini, WinSCP, Linux, SteamOS

===== KNOWLEDGE PACKAGE END =====

---
## 📝 Feedback History

### 2026-11-14 — Test Result: PARTIAL
* **환경:** ASRock DeskMini (Ryzen 5 2400G, DDR4 32GB, 500GB NVMe SSD), Bazzite-Deck OS, 2.4GHz Wi-Fi, IINE Bluetooth Controller
* **검증된 단계:**
  - Bazzite-Deck Home Theater 클린 설치 및 Gaming Mode 부팅 확인.
  - `ujust get-emudeck` 정규 명령어 확인 (AI의 `ujust install-emudeck` 오류 지적).
  - 공식 `install.sh` 수동 설치 후 디렉터리 경로 분기 확인.
  - 유선 직결(`10.42.0.1`) SFTP 기반 300GB 1차 전송 및 향후 Wi-Fi 전송 전략 확정.
  - PS1 `.m3u` 정상 부팅 및 인게임 디스크 교체 정상 동작 검증.
  - `.chd` 분리 폴더(`.hidden`) 내 `noload.txt` 배치를 통한 ES-DE 목록 중복 노출 차단 검증.
  - 오삭제 3종 원본 복원 완료 확인.
  - 대문자 확장자(`.GBA`) 정상 인식 및 MAME 2003-Plus 코어 오류 시 개별 코어 교체 해결 검증.
  - ScreenScraper 일일 한도 리셋 후 영문 등록 롬 아트워크 수집 완료 확인.
  - `gamelist.xml` 게임 설명(Description) 한글화 적용 확인.
* **실패/보류된 단계:**
  - 와일드카드 기반 원격 삭제 스크립트 실행 시 비표준 롬 파일명으로 인한 오삭제/미삭제 오류 발생 -> DeskMini 현지 Antigravity 설치로 해결.
  - DeskMini 내장 Wi-Fi/BT 모듈 미교체로 인한 2.4GHz 연결 및 IINE 컨트롤러 간헐적 끊김 지속.
  - 실시간 OCR 번역은 1초 이상 지연으로 보류 (메모리 후킹 등 대안 구상 단계).
* **새 오류/팁:**
  - AI가 `ujust install-emudeck`으로 잘못 안내할 수 있으므로 실제 Bazzite 명령어인 `ujust get-emudeck`을 사용할 것.
  - ES-DE는 리눅스 숨김 폴더(`.hidden`)도 목록에 노출하므로 내부에 반드시 `noload.txt`를 넣어야 하며, 관리상 일반 폴더(`chd/`) 사용을 권장함.
* **Status 변경:** Experimental → Verified (Partial)

### 2026-11-14 — Blog Visual Assets Update
* **추가된 이미지 자료 (5종):**
  - `bazzite-deck-emudeck-retro-os-img2.png`: Bazzite 공식 다운로드 페이지 (Desktop, Home Theater PC, Handheld 선택 UI)
  - `bazzite-deck-emudeck-retro-os-img3.jpg`: 부팅 후 진입하는 Steam Gaming Mode(게임 모드) 콘솔 UI
  - `bazzite-deck-emudeck-retro-os-img4.png`: Bazzite Portal 제어판 화면
  - `bazzite-deck-emudeck-retro-os-img5.jpg`: ES-DE 프론트엔드 실구동 화면 (한글 설명 및 박스아트 매핑)
  - `bazzite-deck-emudeck-retro-os-img6.jpg`: 선반 위 거치된 4K 빔프로젝터 및 ASRock DeskMini 초소형 본체 실물 사진
* **동기화 대상:** `Blog_Posts/2026-11/bazzite-deck-emudeck-retro-os.html` 본문 맥락에 맞춰 `<img>` 태그 5종 매핑 완료.
