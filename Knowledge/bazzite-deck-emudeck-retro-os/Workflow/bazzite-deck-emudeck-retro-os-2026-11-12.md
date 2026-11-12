---
topic: bazzite-deck-emudeck-retro-os
title_kr: "ASRock DeskMini 기반 Bazzite-Deck + EmuDeck/ES-DE 스팀·레트로 통합 콘솔 OS 구축 기록"
category: Workflow
sub_category: OS-Emulation-Setup
version: 2026-11-12
status: Experimental
created_date: 2026-11-12
last_modified: 2026-11-12
language: KR+EN
tags: [Bazzite, Bazzite-Deck, EmuDeck, ES-DE, RetroArch, DeskMini, WinSCP, Linux]
sources_count: 6
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** bazzite-deck-emudeck-retro-os
* **Title_KR:** ASRock DeskMini 기반 Bazzite-Deck + EmuDeck/ES-DE 스팀·레트로 통합 콘솔 OS 구축 기록
* **Category:** Workflow
* **Sub-Category:** OS-Emulation-Setup
* **Version:** 2026-11-12
* **Status:** Experimental
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
  * **Bazzite-Deck 이미지 및 불변(Atomic) OS 구조 [FACT]:**
    * `bazzite-deck`은 Fedora Atomic 기반 불변 OS인 Bazzite의 핸드헬드·HTPC 전용 변형 이미지로, 부팅 시 기본으로 Steam Gaming Mode(Gamescope)로 진입하며 Desktop Mode 전환(`Return to Gaming Mode`)을 함께 제공한다.
    * 일반 데스크톱 이미지(`bazzite:stable`)를 오설치한 경우 `rpm-ostree rebase` 명령으로 이미지 스트림을 전환할 수 있으나, 초기 환경 정리 측면에서 `bazzite-deck-stable-amd64.iso` 클린 재설치를 선택할 수도 있다.
  * **Bazzite Portal 및 `ujust` 태스크 러너 [FACT]:**
    * `Bazzite Portal`은 설치 후 주요 유틸리티(예: `Decky Loader`, `Bazzite Buddy`, `Decky Framegen`, `Decky Lossless Scaling` 등)를 GUI로 선택 설치하는 설정 허브다.
    * `ujust`는 Bazzite의 `just` 기반 편의 명령어 러너이며, `ujust install-emudeck` 실패 시(`_install_appimage_file` 오류 등) EmuDeck 공식 `install.sh` 스크립트(`curl -L https://raw.githubusercontent.com/dragoonDorise/EmuDeck/main/install.sh | bash`)로 우회 설치할 수 있다.
  * **EmuDeck 디렉터리 구조 및 BIOS 배치 규칙 [FACT]:**
    * Linux/SteamOS 환경에서 EmuDeck(`Internal Storage` 선택 시)은 `~/Emulation/roms/` 하위에 기종별 소문자 폴더(`nes`, `snes`, `genesis`, `psx`, `gba` 등)를 생성하며, 에뮬레이터 펌웨어/BIOS는 기본적으로 하위 폴더 없이 `~/Emulation/bios/` 루트에 직접 배치하도록 구성한다(일부 예외 기종 제외).
    * EmuDeck 앱 내 `BIOS Checker`로 BIOS 파일명과 위치를 점검할 수 있다.
  * **ES-DE 다중 디스크(`.m3u`) 및 중복 숨김(`noload.txt`) 처리 방식 [FACT]:**
    * ES-DE는 개별 디스크 파일(`.chd`, `.bin`/`.cue`)과 `.m3u` 플레이리스트가 같은 루트 폴더에 함께 있으면 목록에 중복 노출한다.
    * ES-DE가 지원하는 대표적인 정리 방식은 두 가지다: ① `.m3u` 확장자 폴더(`Game.m3u/Game.m3u` + `.chd`)로 묶는 "Directories interpreted as files" 방식, ② `.chd` 파일을 별도 하위 폴더로 이동해 `noload.txt`(빈 텍스트 파일)로 스캔을 차단하거나 `.m3u` 내부 상대 경로(`subfolder/Disc 1.chd`)만 수정하는 방식.
    * RetroArch의 SwanStation 코어는 `.m3u` 내부 경로 작성 시 **슬래시(`/`) 구분자**와 **UTF-8(BOM 없음)** 인코딩, 파일명 대소문자 일치를 요구한다.
  * **Linux 파일시스템 대소문자 구분(Case Sensitivity) [FACT]:**
    * `ext4`/`btrfs` 등 Linux 파일시스템은 대소문자를 엄격히 구분하므로, ES-DE 시스템 폴더명은 소문자(`genesis`, `megadrive` 등)여야 하며 롬 파일 확장자(`.GBA` vs `.gba`)가 정의된 소문자 필터와 다르면 인식이 누락될 수 있다.
  * **NetworkManager 유선 직결 공유(`10.42.0.1`) 및 WinSCP 파일 마스크 [FACT]:**
    * Linux NetworkManager에서 이더넷 연결을 `Shared to other computers`로 설정하면 호스트 인터페이스에 `10.42.0.1` IP가 할당되고 `dnsmasq` 기반 DHCP/NAT가 동작해, 별도 공유기 없이 메인 PC와 유선 직결 SFTP(포트 `22`) 전송이 가능하다.
    * WinSCP 스크립팅(`rm`, `put`, `get`)에서 대괄호(`[` `]`)는 문자 집합 와일드카드로 해석되므로, 파일명에 `(USA) [!].zip` 같은 대괄호가 포함된 경우 `[[]` 형태로 이스케이프하지 않으면 `rm` 대상 탐색이 실패한다.

---

#### 🛠️ Procedures (절차)

##### 1. 하드웨어·BIOS 및 Bazzite-Deck OS 기반 구축 [USER REPORTED]
  1. 대상 하드웨어(ASRock DeskMini, Ryzen 5 2400G, DDR4 32GB, 500GB NVMe SSD)의 BIOS에서 Secure Boot를 해제하고, XMP/AHCI 설정 및 내장 GPU 할당 메모리(`UMA Frame Buffer`)를 `8G`로 설정한다.
  2. `bazzite-deck-stable-amd64.iso`로 클린 설치하여 Gaming Mode와 데스크톱 전환(`Return to home`) 환경을 구성한다.
  3. `Bazzite Portal`에서 필요한 항목(`Decky Loader`, `Bazzite Buddy`, `Decky Framegen`, `Decky Lossless Scaling`)만 선택해 설치한다(`Sunshine`, `Waydroid`, `OpenRGB`, `Wootility`, `Resilio Sync` 제외).

##### 2. EmuDeck 및 ES-DE 설치 [FACT + USER REPORTED]
  1. `ujust` 기반 EmuDeck 자동 설치에서 `_install_appimage_file` 오류가 발생한 경우, 터미널에서 공식 설치 스크립트를 실행해 EmuDeck을 설치한다. [FACT]
     ```bash
     curl -L https://raw.githubusercontent.com/dragoonDorise/EmuDeck/main/install.sh | bash
     ```
  2. EmuDeck 마법사에서 `Easy Mode`, `Internal Storage`, `ES-DE` 통합(`Epic Noir` 테마)을 선택한다. [USER REPORTED]
  3. ES-DE에서 불필요한 `Emulators` 메뉴 노출을 숨기려면 해당 폴더 경로에 빈 `noload.txt` 파일을 배치한다. [FACT]

##### 3. PC↔DeskMini 유선 직결 SFTP 환경 및 롬/BIOS 1차 이관 [FACT + PC LOG VERIFIED]
  1. DeskMini의 NetworkManager에서 유선 연결(`Steam Wired Connection`) IPv4 방식을 `Shared to other computers`로 전환해 `10.42.0.1` 직결 대역을 연다. [FACT]
  2. 메인 PC에서 영문 통합 롬셋(약 300GB 규모, 28개 기종 폴더 및 PS1 `.chd` 237개·`.m3u` 200개 무결성 확인)과 BIOS 묶음을 `~/Emulation/roms` 및 `~/Emulation/bios` 경로로 SFTP(포트 `22`, WinSCP) 전송한다. [PC LOG VERIFIED]
  3. 실시간 OCR 번역 서비스(무료 월 500회 제한) 대신 사전 한글 패치 롬셋 교체 전략을 채택하고, PC에 영문 원본 전량을 백업으로 유지한 채 DeskMini 측에는 저용량 4개 기종(`nes` 98개, `snes` 51개, `megadrive` 32/41개, `gba` 146/150개 + 기존 보유분 7개 = 총 334개)을 업로드한다. [PC LOG VERIFIED]

##### 4. PC 측 WinSCP 감사·복구 스크립트 작성 및 DeskMini 현지 후속 작업 [PC LOG VERIFIED + USER REPORTED]
  1. 1차 WinSCP 배치 실행 결과, 한글 롬 334개(MD 32개는 `genesis`/`megadrive` 양쪽 전송으로 총 366건) 업로드 후 영문 롬 삭제 대상 100개(116줄) 중 78개 삭제(정상 75 + 오삭제 3), 22개 실패를 확인했다. [PC LOG VERIFIED]
  2. PC에서 오삭제 3종(`Contra - Hard Corps (USA, Korea).zip`, `Landstalker (USA).zip`, `Pokemon - Sapphire Version (USA, Europe) (Rev 2).zip`) 복구용 `lcd`/`cd` 방식 배치 스크립트와 와일드카드(`*`) 기반 중복 삭제 스크립트(158줄)를 생성해 원본과 대조했다. [PC LOG VERIFIED]
  3. 이후 DeskMini 현지에 Antigravity를 직접 설치해 로컬에서 직접 제어하며 ① 잔여 중복 롬 제거, ② PS1 `.chd` 파일 별도 폴더 이동 및 `.m3u` 내부 참조 경로 일괄 수정, ③ 최종 롬 파일 한글화 작업을 진행했다. [USER REPORTED — PC 세션에서 원격 직접 검사는 미수행]

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] Bazzite `ujust` EmuDeck 설치 중 `_install_appimage_file` 오류**
    * 원인: 내장 `ujust` 레시피가 AppImage/아키텍처 다운로드 또는 캐시 단계에서 실패했다.
    * 해결법: `ujust` 래퍼 대신 EmuDeck 공식 `install.sh`(`curl -L https://raw.githubusercontent.com/dragoonDorise/EmuDeck/main/install.sh | bash`)를 터미널에서 직접 실행해 설치했다. [FACT]
    * 신뢰도: [★★★★★]
  * **[오류 2] `bazzite:stable` 오설치로 Gaming Mode 미부팅**
    * 원인: HTPC/핸드헬드용 `bazzite-deck`이 아닌 일반 데스크톱용 `bazzite:stable` 이미지를 먼저 설치했다.
    * 해결법: `rpm-ostree rebase` 전환 대신 `bazzite-deck-stable-amd64.iso`로 클린 재설치하여 초기 상태를 정리했다. [FACT/USER REPORTED]
    * 신뢰도: [★★★★★]
  * **[오류 3] WinSCP 일괄 삭제(`rm`) 중 22개 실패 및 영문 롬 3종 오삭제**
    * 원인: WinSCP가 파일명 내 대괄호(`[]`)를 파일 마스크 패턴으로 오인해 매칭에 실패했고, 괄호 태그 불일치·느슨한 매칭으로 인해 한글화 대상과 다른 3개 영문 롬(`Contra - Hard Corps`, `Landstalker`, `Pokemon - Sapphire Version`)이 함께 삭제되었다.
    * 해결법: WinSCP 파일 마스크에서는 `[`를 `[[]`로 이스케이프하거나 와일드카드·디렉터리 이동(`lcd`/`cd`)을 정확히 분리해야 하며, 오삭제 3종에 대해서는 PC 원본 백업에서 복구 스크립트를 생성해 대비했다. [FACT + PC LOG VERIFIED]
    * 신뢰도: [★★★★★]
  * **[오류 4] ES-DE에서 PS1 다중 디스크(`.chd` + `.m3u`) 중복 노출**
    * 원인: 동일 폴더(`psx/`) 내에 `.m3u` 플레이리스트와 개별 `.chd` 디스크 파일이 공존하면 ES-DE가 양쪽을 모두 게임 항목으로 스캔한다.
    * 해결법: 초기 검토한 `custom_systems/es_systems.xml` 확장자 필터링 대신, `.chd` 파일들을 별도 폴더로 이동시키고 `.m3u` 내부 경로(링크)를 이동된 폴더의 상대 경로로 수정하는 방식을 선택했다. [FACT/USER REPORTED]
    * 신뢰도: [★★★★☆]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] Bazzite 환경에서 유선 직결(`10.42.0.1`)로 대용량 롬셋을 옮긴 뒤, 세부 중복 제거와 경로 수정은 원격 WinSCP 배치 반복보다 기기 현지에 개발 도구나 에이전트를 직접 두어 로컬 파일시스템을 다루는 편이 파일명 인코딩·대괄호 이스케이프 오류를 줄이는 데 유리했다. — 출처: 사용자 구축 세션 보고
  * [FACT] 외부 아카이브 소스에서 대량 다운로드 시 `HTTP 500`이나 `ECONNRESET`으로 일부 파일(예: MD 9종, GBA 4종)이 누락될 수 있으므로 다운로드 현황 로그를 남겨 재시도 대상을 분리해야 한다. — 출처: PC 다운로드 로그 기록

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] **"최종 롬 파일들의 한글화" 구체 범위 확인:** DeskMini 현지 작업에서 적용한 한글화가 `gamelist.xml`/ROM 파일명(메타데이터) 한글화인지, 아니면 PS1·PSP·GameCube·Saturn 등 고용량 기종의 한글 패치 롬 추가 교체까지 포함하는지 확인이 필요하다.
  * [ ] **PS1 `.m3u`/`.chd` 실구동 및 디스크 교체 검증:** 별도 폴더로 이동된 `.chd` 경로를 가리키도록 수정한 `.m3u` 파일이 RetroArch(SwanStation 코어)에서 정상 부팅되며 인게임 디스크 교체(Disc Control)까지 동작하는지 실제 게임 실행으로 검증이 필요하다. (`/` 슬래시, UTF-8 무BOM, 대소문자 일치 여부 포함)
  * [ ] **오삭제 3종 복구 및 다운로드 실패 13종 반영 여부 확인:** 1차 WinSCP 작업에서 오삭제된 3종(`Contra - Hard Corps`, `Landstalker`, `Pokemon - Sapphire Version`)의 복구 완료 여부와, 다운로드 실패분(MD 9종, GBA 4종)의 보완 반영 여부를 DeskMini 실제 파일 목록 기준으로 확인해야 한다.
  * [ ] **`genesis`/`megadrive` 이중 폴더, 대문자 확장자(`.GBA`), MAME 코어, `Update 1` 25종 확인:** `genesis`와 `megadrive` 양쪽에 업로드된 32종이 ES-DE에서 중복 노출되지 않는지, `Final Fantasy6(K).GBA` 같은 대문자 확장자가 Linux 대소문자 구분 환경에서 정상 인식되는지, MAME 2003-Plus 코어 및 `Sega - Genesis (Update 1)` 25종이 `Reload Game Media` 후 정상 실행되는지 확인이 필요하다.
  * [ ] **아트워크 스크래핑 완료 여부 및 네트워크/무선 하드웨어 후속 상태:** ScreenScraper/TheGamesDB 일일 한도 이후 전체 아트워크 스크래핑 완료 여부와, 가정 내 공유기 3대 LAN 배선 및 DeskMini 내장 Wi-Fi/BT 모듈 교체 여부를 확인해야 한다.

---

#### 🏷️ Tags
Bazzite, Bazzite-Deck, EmuDeck, ES-DE, RetroArch, SwanStation, DeskMini, WinSCP, Linux, SteamOS

===== KNOWLEDGE PACKAGE END =====
