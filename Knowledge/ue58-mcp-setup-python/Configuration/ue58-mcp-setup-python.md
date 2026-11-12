---
topic: ue58-mcp-setup-python
title_kr: "Unreal Engine 5.8 공식 Unreal MCP 도입 절차와 Python 클라이언트·Unreal Python 구별"
category: Configuration
sub_category: Model-Context-Protocol-Editor-Automation
version: "5.8"
status: Experimental
created_date: 2026-11-12
last_modified: 2026-11-12
language: KR+EN
tags: [UnrealEngine, UE5.8, UnrealMCP, ModelContextProtocol, AllToolsets, Automation, Python]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** ue58-mcp-setup-python
* **Title_KR:** Unreal Engine 5.8 공식 Unreal MCP 도입 절차와 Python 클라이언트·Unreal Python 구별
* **Category:** Configuration
* **Sub-Category:** Model-Context-Protocol-Editor-Automation
* **Version:** 5.8
* **Status:** Experimental
* **Date:** 2026-11-12
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Epic Games 공식 문서 - Unreal MCP in Unreal Editor (UE 5.8): https://dev.epicgames.com/documentation/unreal-engine/unreal-mcp-in-unreal-editor?lang=ko
  * [★★★★★] Epic Games 공식 문서 - Scripting the Unreal Editor Using Python: https://dev.epicgames.com/documentation/unreal-engine/scripting-the-unreal-editor-using-python
  * [★★★★★] 로컬 프로젝트 설정 및 MCP 요청/응답 보존물 (`Bootcamp_BaseProject/.codex/config.toml`, `Guides/MCP_공생성_요청.json`, `Guides/MCP_공생성_응답.json`, `Guides/MCP_실제명령_예시.json`)
  * [★★★★★] 로컬 작업 취합 기록 (`00_전달자료.md`, `01_검증과자료목록.md`, `conversation-index.json`)
  * [★★★★☆] 참고 이미지 (`images/index.md`: `mcp-plugin.png`, `mcp-console.png`, `mcp-result.png`)

---

#### 🔑 Core Concepts (핵심 개념)
  * **Unreal Engine 5.8 공식 Unreal MCP 아키텍처 (`ModelContextProtocol` + `AllToolsets`) [FACT - 공식 문서]:**
    * **`ModelContextProtocol` 플러그인:** Unreal Editor 프로세스 내부에 로컬 HTTP 기반 MCP 서버(기본 엔드포인트 `/mcp`)를 구동하는 코어 백엔드다.
    * **`AllToolsets` 플러그인:** 액터 생성, 조명 설정, 머티리얼 인스턴스, 블루프린트/UMG 등 AI 에이전트가 호출할 수 있는 실제 에디터 도구 묶음을 제공하는 어그리에이터다. 기본적으로 비활성화(`EnabledByDefault: false`) 상태이므로 명시적으로 활성화해야 도구가 노출된다.
    * **실행 특성 및 보안:** 에디터 게임 스레드 안전을 위해 요청을 순차 동기화 처리하며, 별도 인증 레이어 없이 동일 로컬 머신(`127.0.0.1`) 전용으로 설계되어 외부 네트워크 노출을 피해야 한다.
  * **초기 Unreal Python 직접 조작 vs 공식 Unreal MCP 전환 이력 분리 [FACT - 로컬 작업 이력]:**
    * 작업 초기(67장~165장 단계)에는 아직 Unreal MCP가 연결되지 않은 상태에서 `Unreal Python` 명령을 사용해 에디터 작업을 진행했다.
    * 이후 사용자 요청에 따라 Epic 공식 Unreal MCP 문서를 확인하고 UE 5.8의 `ModelContextProtocol`·`AllToolsets`를 활성화한 뒤, 모션·MCP 개정(182장 단계)부터 작업실·PCG·Blueprint·재질·입력·참조 수정과 저장에 공식 MCP 도구를 사용했다.
    * 따라서 **초기 Unreal Python 기반 작업 시점을 MCP 사용 이력으로 소급해 서술하면 사실과 다르다.**
  * **Python HTTP 클라이언트 vs Unreal Editor 내부 Python API 구별 [FACT - 기술 경계]:**
    * MCP 전환 이후에도 외부에서 `http://127.0.0.1:8000/mcp`로 HTTP JSON-RPC 요청을 전송하는 클라이언트 스크립트가 Python으로 작성될 수 있다.
    * 그러나 이는 **MCP 프로토콜을 통해 에디터 내장 MCP 서버를 호출하는 외부 클라이언트**일 뿐, 에디터 내부 `PythonEditorScriptPlugin`으로 씬을 직접 수정하는 `Unreal Python` 실행과 다르다.
  * **에디터 화면 조작과 MCP 병행 원칙 [FACT - 로컬 실행 기록]:**
    * 공식 MCP를 도입한 이후에도 에디터 뷰포트 UI 정렬, 일부 네이티브 기능, 슬라이드용 화면 캡처, 실제 PIE 키 입력 확인에는 직접 화면 조작을 병행했다. 모든 조작이 MCP만으로 이루어졌다고 쓰지 않는다.

---

#### 🛠️ Procedures (절차)
  1. **플러그인 활성화 (`ModelContextProtocol`, `AllToolsets`):** [★★★★★]
     * Unreal Editor의 **Edit > Plugins**에서 `ModelContextProtocol`과 `AllToolsets`를 모두 체크해 활성화하거나 `.uproject`의 `Plugins` 항목에 `Enabled: true`로 등록한 뒤 에디터를 재시작한다 (`mcp-plugin.png`).
  2. **MCP 서버 시작 및 엔드포인트 확인:** [★★★★★]
     * 에디터 콘솔 또는 Output Log에서 다음 명령을 실행한다 (`mcp-console.png`):
       ```text
       ModelContextProtocol.StartServer 8000
       ```
     * 로컬 MCP 엔드포인트 `http://127.0.0.1:8000/mcp` 활성화 여부를 확인한다. (반복 사용 시 Editor Preferences의 `General > Model Context Protocol > Auto Start Server` 또는 `bAutoStartServer=True` 설정 활용 가능)
  3. **클라이언트 설정 등록 (`.codex/config.toml` 등):** [★★★★★]
     * 프로젝트의 `.codex/config.toml`에 로컬 서버 주소(`http://127.0.0.1:8000/mcp`)를 등록한다. (공식 문서상 `ModelContextProtocol.GenerateClientConfig [ClientName]` 콘솔 명령으로 ClaudeCode/Cursor/VSCode/Gemini/Codex 설정 파일 자동 생성도 지원)
  4. **실제 MCP 요청·응답 테스트 및 교재 반영:** [★★★★★]
     * 구체 생성 MCP 요청을 호출하고 에디터 뷰포트 생성 결과(`mcp-result.png`)와 JSON 요청·응답 로그(`Guides/MCP_공생성_요청.json`, `MCP_공생성_응답.json`, `MCP_실제명령_예시.json`)를 보존해 교재에 수록한다.

---

#### 🐛 Errors & Solutions (오류 및 해결법)
  * **[오류 1] MCP 서버는 열렸으나 AI가 호출할 에디터 도구가 보이지 않는 현상**
    * 원인: `ModelContextProtocol` 코어 플러그인만 켜고, 실제 에디터 도구 묶음을 제공하는 `AllToolsets` 플러그인(기본값 비활성)을 켜지 않은 경우 발생한다.
    * 해결법: `ModelContextProtocol`과 `AllToolsets` 두 플러그인을 함께 활성화하고 에디터를 재시작한다. [FACT - 공식 문서]
    * 신뢰도: [★★★★★]
  * **[오류 2] 초기 Unreal Python 작업과 MCP 기반 작업 이력의 혼동**
    * 원인: 작업 후반에 공식 Unreal MCP로 전환하고 외부 테스트 호출도 Python HTTP 클라이언트로 수행하다 보니, 전체 제작 과정을 처음부터 MCP로 진행했거나 반대로 끝까지 Unreal Python으로만 씬을 수정한 것처럼 오해될 수 있다.
    * 해결법: 작업 시점(초기 Unreal Python → 후반 공식 Unreal MCP), 통신 계층(HTTP MCP 클라이언트 vs 에디터 내장 Python 스크립팅), 수동 화면 조작 병행 범위를 명확히 구분해 기록한다. [FACT - 로컬 작업 이력]
    * 신뢰도: [★★★★★]
  * **[오류 3] MCP 명령 반환값만으로 에디터 디스크 상태·참조 정리가 끝났다고 판단하는 오류**
    * 원인: MCP의 파일 이동(`move`) 도구나 액터 수정 호출이 성공 응답을 반환하더라도, 언리얼 패키지 직렬화·참조(Redirector/DataAsset/Sequencer 바인딩)나 디스크 상 구파일이 자동으로 정리되지 않을 수 있다.
    * 해결법: MCP 호출 후에도 명시적 저장, 참조 뷰어/문자열 스캔, 대상 맵 재오픈 확인을 별도 검증 절차로 둔다. [FACT - 로컬 디버깅 경험]
    * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)
  * [OPINION] 사용자가 이미 사용 중인 도구 환경이나 에디터 버전에 맞춰 공식 기능(UE 5.8 내장 Unreal MCP)을 직접 연동하고 실제 요청·응답 JSON과 화면 캡처까지 교재에 담으니, 단순 개념 소개보다 재현 가능한 실습 가이드가 되었다. — 출처: 작업 취합 기록 (`00_전달자료.md`)
  * [FACT] 공식 Unreal MCP는 로컬 머신(`127.0.0.1`) 전용이며 인증 레이어가 없으므로 학습/개발 환경에서 방화벽이나 외부 포트 포워딩으로 노출하지 않아야 한다. — 출처: Epic Games 공식 문서

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 다른 PC 환경에서 배포 ZIP을 풀고 UE 5.8로 처음 열었을 때 `ModelContextProtocol`·`AllToolsets` 플러그인 활성화 및 `.codex/config.toml` 연동이 추가 수동 설정 없이 바로 동작하는지 검증
  * [ ] `bAutoStartServer=True` 자동 시작 방식과 콘솔 `ModelContextProtocol.StartServer 8000` 수동 시작 방식 중 교재 실습 단계에서 권장할 기본 흐름 최종 확정
  * [ ] `Guides/MCP_공생성_요청.json`·`MCP_공생성_응답.json`·`MCP_실제명령_예시.json` 공개 블로그 본문 인용 시 포트·로컬 경로 표현 정리

---

#### 🏷️ Tags
UnrealEngine, UE5.8, UnrealMCP, ModelContextProtocol, AllToolsets, Automation, Python

===== KNOWLEDGE PACKAGE END =====
