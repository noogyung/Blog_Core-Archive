---
topic: antigravity-ide-vs-app
title_kr: Antigravity IDE와 Antigravity 2.0 앱의 차이점
category: Concept
sub_category: Product-Comparison
version: 2026-06-19
status: Verified (Partial)
created_date: 2026-06-19
last_modified: 2026-06-19
language: KR+EN
tags: [Antigravity, Antigravity-IDE, Antigravity-2.0, VS-Code-Fork, Multi-Agent, 에이전트, 개발환경, 비교]
sources_count: 4
blog_draft_path: Blog_Posts/core-archive/2026-06/antigravity-ide-vs-app-workflow.html
blog_draft_date: 2026-06-19
blog_id: core-archive
blog_published: true
series_name: null
series_part: null
series_total: null
series_prev_slug: null
series_next_slug: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** antigravity-ide-vs-app
* **Title_KR:** Antigravity IDE와 Antigravity 2.0 앱의 차이점
* **Category:** Concept
* **Sub-Category:** Product-Comparison
* **Version:** 2026-06-19
* **Status:** Verified (Partial)
* **Date:** 2026-06-19
* **Language:** KR+EN

---

#### 📚 Sources & Confidence

* [★★★★★] 공식 문서: antigravity.google (공식 홈페이지 — JS 렌더링 필요로 텍스트 추출 불완전)
* [★★★★☆] Google 공식 블로그: blog.google / google.dev
* [★★★☆☆] 기술 미디어: howtogeek.com, datacamp.com, visualstudiomagazine.com
* [★★☆☆☆] 커뮤니티: reddit.com, dev.to, medium.com

---

#### 🔑 Core Concepts (핵심 개념)

* **[Antigravity 생태계 구성]:** Google Antigravity는 하나의 제품이 아니라 공통 에이전트 하네스를 공유하는 여러 "Surface(접근 방식)"로 구성된 플랫폼이다. 구성 요소: Antigravity 2.0 (Desktop App), Antigravity IDE (VS Code Fork), Antigravity CLI (터미널 도구, Go 언어 기반), Antigravity SDK (개발자용 프레임워크). [FACT]

* **[Antigravity IDE]:** VS Code를 포크(Fork)하여 만든 코드 에디터. 파일 트리, 터미널, 에디터 패널 등 전통적인 IDE 인터페이스를 그대로 제공하면서 AI 에이전트 기능(자동 완성, 인라인 명령, 에이전트 사이드바)을 통합한 도구. [FACT]

* **[Antigravity 2.0 App]:** 2026년 Google I/O(5월 19일)에서 발표된 독립형 데스크톱 애플리케이션. 코드 편집보다 **다중 AI 에이전트 오케스트레이션(Multi-Agent Orchestration)**에 특화된 "미션 컨트롤(Mission Control)" 역할을 한다. Windows, macOS, Linux 지원. [FACT]

* **[플랫폼 분리의 배경]:** Antigravity 2.0 출시로 원래 하나였던 에이전트 플랫폼이 두 도구로 분리됐다. 개발자 커뮤니티 일부에서는 기존 IDE 방식을 선호하여 논란이 있었으나, 현재 두 도구 모두 별도 설치본으로 제공된다. [FACT]

* **[공통 기반 모델]:** 두 도구 모두 Gemini 3.5 Flash 모델을 기반으로 동작한다. [FACT]

---

#### 🛠️ Procedures (절차) — 해당 없음

이 문서는 개념 비교(Concept) 파일로, 절차(Procedures) 섹션은 해당 사항 없음.

---

#### 🐛 Errors & Solutions (오류 및 해결법) — 해당 없음

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] Antigravity 2.0과 Antigravity IDE는 경쟁 관계가 아니라 **병행 사용**이 권장된다. 코드 직접 작성은 IDE에서, 복잡한 병렬 에이전트 작업 관리는 2.0 앱에서 수행하는 방식. — 출처: reddit.com, howtogeek.com

* [OPINION] Antigravity IDE는 Open VSX Registry를 기본 확장 마켓플레이스로 사용하므로, Microsoft 공식 마켓플레이스의 일부 확장은 별도 설정이 필요할 수 있다. 단, 대부분의 인기 확장은 Open VSX에도 있거나 대체 확장이 존재하여 실사용 상 큰 불편은 없는 편이다. — 출처: jimmysong.io, dev.to / [USER VERIFIED 2026-06-19]

* [OPINION] 기존 VS Code 또는 Cursor 설정 및 키바인딩을 Antigravity IDE로 가져올 수 있어 전환 비용이 낮다. — 출처: habr.com, google.dev

* [FACT] Antigravity CLI는 기존 Gemini CLI를 대체하며, 2026년 6월 18일부로 Gemini CLI는 서비스 종료(Sunset)됐다. — 출처: dev.to, medium.com

* [USER VERIFIED] Antigravity 2.0 앱과 Antigravity IDE는 **동시 설치가 가능하며 충돌이 없다.** Antigravity 2.0 앱 채팅창 오른쪽 상단의 **"Open IDE" 버튼**을 누르면, 현재 작업 중인 폴더를 기준으로 Antigravity IDE가 자동으로 열린다. — 사용자 직접 확인 2026-06-19

* [USER VERIFIED] 실제 사용 워크플로우 패턴: **Antigravity 2.0 앱** → 검색·정보 수집·정리·게시글 작성 등 AI 에이전트 활용 / **Antigravity IDE** → Unity 개발 등 실제 코드 작성을 위한 VS Code 대체 편집기로 활용. 두 도구가 역할 분담 형태로 병행 사용된다. — 사용자 직접 확인 2026-06-19

* [USER OPINION] 터미널 자동 실행 설정: **Review-driven** 권장. 실제 사용 시 Auto 모드도 크게 위험하지 않을 수 있으나, 개인 성향에 따라 다름. 프로젝트별로 "이 명령어는 항상 허용" 을 직접 지정하는 방식이 편의성과 안전성을 동시에 확보하는 방법으로 유효하다. — 사용자 경험 2026-06-19

---

#### ❓ Missing Info (검증 필요 항목)

* [x] Antigravity 2.0 앱과 Antigravity IDE 동시 설치 시 충돌 여부 — Verified 2026-06-19 (충돌 없음, Open IDE 버튼으로 연동 확인)
* [~] Open VSX Registry 이외 Microsoft 마켓플레이스 재설정 방법 — 부분 확인 2026-06-19 (일부 확장 누락 있으나 실사용 무관. 작업 중 추가 피드백 예정)
* [ ] AI Ultra 구독($100/월) 없이 2.0 앱의 멀티에이전트 기능 사용 가능 범위 확인 필요
* [x] 공식 사이트(antigravity.google) JS 렌더링 이후 최신 스펙 교차 확인 — Closed 2026-06-19 (별다른 추가 정보 없음 확인)

---

#### 📊 핵심 비교표

| 구분 | Antigravity IDE | Antigravity 2.0 (App) |
|------|----------------|----------------------|
| **형태** | VS Code 포크 (코드 에디터) | 독립형 데스크톱 앱 |
| **주요 역할** | 코드 직접 작성·편집 | 다중 에이전트 오케스트레이션 |
| **인터페이스** | 파일 트리 + 에디터 + 터미널 | 에이전트 대시보드·인박스 |
| **워크플로우** | 동기적 (사람이 코드 보면서 진행) | 비동기적 (에이전트에게 위임·모니터링) |
| **사용자 유형** | 코드를 직접 제어하고 싶은 개발자 | 에이전트 팀을 관리하는 사용자 |
| **특이사항** | Open VSX Registry 기본 사용 | 음성 명령, 스케줄 작업(Cron) 지원 |
| **연동** | — | 채팅창 우상단 "Open IDE" 버튼으로 현재 폴더 기준 IDE 즉시 실행 ✅ |

---

#### 🏷️ Tags

Antigravity, Antigravity-IDE, Antigravity-2.0, VS-Code-Fork, Multi-Agent, 에이전트, 개발환경, 코드에디터, 오케스트레이션, Google-IO-2026, Gemini

===== KNOWLEDGE PACKAGE END =====

---

## 📝 Feedback History

### 2026-06-19 — Test Result: PARTIAL
* **환경:** Windows, Antigravity 2.0 App + Antigravity IDE 동시 설치
* **검증된 항목:**
  - 동시 설치 충돌 없음 확인
  - "Open IDE" 버튼 위치 및 동작 확인 (채팅창 우상단 → 현재 폴더 기준 IDE 열림)
  - Open VSX Registry 확장 누락 일부 있으나 실사용 무관 확인
  - 공식 사이트 추가 스펙 없음 확인
* **미검증 항목:** AI Ultra 없이 멀티에이전트 기능 범위 (추후 사용 예정)
* **추가 팁:**
  - 실제 워크플로우: 2.0 앱(AI 에이전트 활용) + IDE(코드 편집) 역할 분담
  - 터미널 설정: Review-driven 권장, 프로젝트별 허용 명령어 지정 방식 유효
  - 향후 "Antigravity IDE 활용" 주제로 별도 Knowledge 파일 작성 예정
* **Status 변경:** Experimental → Verified (Partial)
