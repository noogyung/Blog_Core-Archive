---
topic: webtranslator-15-dark-mode-effective-bg-and-adaptive-color
title_kr: 다크모드 가독성 붕괴 극복과 인라인 가독성 4종 옵션 및 블록 스타일 엔진 구축
category: Troubleshooting
sub_category: Adaptive-Theme-Engine
version: 2.2.0
status: Verified
created_date: 2026-08-20
last_modified: 2026-08-20
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, DarkMode, 다크모드, AdaptiveColor, 유효배경색, YIQ, CSS변수, 가독성옵션, 트러블슈팅]
sources_count: 4
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: webtranslator
---

# Knowledge File: webtranslator-15-dark-mode-effective-bg-and-adaptive-color
## Category: Troubleshooting (Adaptive-Theme-Engine)
## Date: 2026-08-20

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** webtranslator-15-dark-mode-effective-bg-and-adaptive-color
* **Title_KR:** 다크모드 가독성 붕괴 극복과 인라인 가독성 4종 옵션 및 블록 스타일 엔진 구축
* **Category:** Troubleshooting
* **Sub-Category:** Adaptive-Theme-Engine
* **Version:** 2.2.0
* **Status:** Verified
* **Date:** 2026-08-20
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 다크모드 가독성 엔진 설계 대화 로그 (`37bafb78-0ac0-4e1a-af49-e3230b742da0`)
* [★★★★★] WebTranslator DOM 렌더링 및 유효 배경색 탐색기 소스 코드 (`src/content/dom.js:L401-L469`, 커밋 `2af2d25`, `b49e42e`, `fe87483`)
* [★★★★★] WebTranslator 동적 스타일 및 인라인 가독성 CSS 정의 (`content.css:L1-L95`)
* [★★★★★] WebTranslator 옵션 페이지 및 빠른 팝업 연동 소스 코드 (`src/options/ui.js`, `src/optionPopup/index.js`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[다크모드 및 복합 웹 테마에서의 가독성 붕괴 문제]:** 스팀(Steam) 커뮤니티나 깃허브(GitHub) 다크모드 같은 어두운 배경 사이트에서 인라인 번역을 실행하면 번역문 글자가 어두운 배경에 묻혀 식별이 불가능해지고, 반대로 밝은 사이트(위키피디아 등)에 흰색 고정 텍스트를 적용하면 흰 배경에 묻히는 가독성 붕괴 현상이 발생함. [FACT]
* **[수동 컬러피커 제안의 한계와 지시 번복]:** 옵션 페이지에 사용자가 직접 글자 색을 선택하는 Color Picker를 두는 방식은 웹사이트를 이동할 때마다 사용자가 수동으로 설정을 변경해야 하므로 사용자 경험을 심각하게 저해함. 이에 수동 설정을 전면 취소하고 확장 프로그램이 페이지 환경을 스스로 감지하여 텍스트 색상을 자동 계산하는 적응형 엔진으로 방향을 전환. [FACT]
* **[테마 색상 및 블록 배경 투명도(Theme Color & Block Opacity) 유지]:**
  * 사용자가 지정한 메인 테마 색상(`transColor`, 기본 `#818cf8`)을 유지하면서, Block 형태 번역문의 좌측 강조선(`border-left`)과 배경색을 일관되게 표현. [FACT]
  * **블록 배경 투명도 조절(`transBgAlpha`, 기본 `0.12`):** 0~1 범위의 슬라이더를 통해 사용자가 블록 번역문 배경의 은은함과 강조 수준을 자유롭게 조절할 수 있도록 지원. [FACT]
  * **자동 텍스트 명도 보간(`getAutoTextColor`):** 테마 색상의 YIQ 밝기와 배경 투명도(`bgAlpha`)에 따라 글자 명도(Lightness)를 자동 보간하여 최적의 텍스트 가독성 색상(`--wt-text-color`)을 산출. [FACT]
* **[트러블슈팅의 최종 귀결: 인라인 가독성 4대 옵션 체계]:**
  * 다크모드 및 시인성 문제를 해결하기 위해 구축된 기술적 해법들을 사용자가 브라우징 성향에 맞춰 조합할 수 있는 **4종류의 인라인 가독성 옵션**으로 최종 구조화: [FACT]
  1. **텍스트 이중 그림자 (`inlineShadow` / `data-wt-inline-shadow="true"`):** 원문 글자 색상 또는 텍스트 명도(YIQ > 128)에 따라 밝은 글로우(`rgba(255,255,255,0.9)`) 또는 어두운 글로우(`rgba(0,0,0,0.85)`)를 2중으로 부여하여 텍스트와 배경의 경계를 명확히 분리.
  2. **미세한 인라인 배경색 강조 (`inlineHighlight` / `data-wt-inline-highlight="true"`):** 인라인 번역문 뒤에 테마 색상 기반의 반투명 배경(`var(--wt-trans-bg)`, `padding: 0 4px`, `border-radius: 4px`)을 얇게 깔아 본문과의 혼동 방지.
  3. **글자색 환경 적응 (`inlineAdaptiveColor` / `data-wt-inline-adaptive="true"`):** 상위 DOM 유효 배경색(`Effective Background Color`) 역추적, `background-image` 예외 차단, YIQ 보색 계산 및 중간 회색(±60) 강제 보정 알고리즘을 통한 완전 자동 보색 주입.
  4. **원문 글자 색상 따라가기 (`inlineInheritColor` / `data-wt-inline-inherit="true"`):** `TreeWalker`로 실제 원문 텍스트 컨테이너의 `computedStyle.color`를 추출하여 번역문이 원문 폰트 색상을 자연스럽게 상속.
* **[CSS Custom Property 및 우선순위 충돌 방지]:**
  * 계산된 적응형 색상과 그림자 색상을 CSS 변수(`--wt-inline-adaptive-color`, `--wt-inline-glow-color`)로 span 요소에 동적 주입. [FACT]
  * 환경 적응 색상(옵션 3)과 원문 상속(옵션 4)이 동시 활성화될 때 CSS의 `:not([data-wt-inline-adaptive="true"])` 선택자를 통해 환경 적응 색상이 최우선 적용되도록 충돌 방지 설계. [FACT]
* **[툴바 빠른 설정 팝업(`optionPopup.html`)과 실시간 동기화]:**
  * 웹페이지를 이탈하지 않고 툴바 팝업의 슬라이더와 드롭다운 체크박스를 통해 스타일 및 4대 가독성 옵션을 조절하면, `chrome.storage.onChanged` 및 `chrome.tabs.sendMessage("updateStylePreview")`를 통해 활성 탭의 번역문에 즉각 반영. [FACT]

---

#### 🛠️ Procedures (절차)

1. **인라인 번역 요소의 실제 텍스트 컨테이너 특정 (`dom.js`):** [★★★★★] ✅ Verified 2026-08-20
   - `createTreeWalker`를 통해 요소 내 실제 텍스트 노드를 감싸는 요소를 탐색.
   - 전체 텍스트 중 일부만 `<a>` 태그인 경우 파란색 링크 색상이 번역문 전체로 번지는 왜곡을 방지하기 위해 `<a>` 내부 텍스트를 우선 분리 처리.
2. **상위 DOM 순회를 통한 유효 배경색 탐색 및 이미지/그라데이션 예외 차단:** [★★★★★] ✅ Verified 2026-08-20
   - `while` 루프로 상위 부모 노드를 탐색하며 `window.getComputedStyle(bgNode)`의 `backgroundColor` 확인 (알파 채널 > 0.1).
   - 탐색 도중 `backgroundImage`가 `none`이나 `initial`이 아닌 요소를 만나면 부모 탐색을 즉시 중단하고, 텍스트의 `computedStyle.color`를 추출하여 YIQ 명도 분석 후 가상 배경색으로 폴백.
3. **YIQ 보색 계산 및 중간 회색 대비 강제 보정:** [★★★★★] ✅ Verified 2026-08-20
   - 추출된 배경색 RGB를 반전(`invR = 255 - r, invG = 255 - g, invB = 255 - b`) 처리.
   - `Math.abs(yiq - invYiq) < 60` 조건으로 중간 명도 영역을 감지하고, 배경 명도에 따라 반전 RGB 값을 ±60만큼 강제 푸시(`pushAmt`)하여 완벽한 시인성 확보.
4. **인라인 가독성 4대 옵션 및 블록 스타일 엔진 구조화 (`ui.js`, `content.css`):** [★★★★★] ✅ Verified 2026-08-20
   - 블록 번역문: `--wt-trans-bg`, `--wt-trans-border`, `--wt-theme-color`, `transBgAlpha`를 통해 반투명 배경 및 좌측 하이라이트선 렌더링.
   - 인라인 번역문: `inlineShadow`, `inlineHighlight`, `inlineAdaptiveColor`, `inlineInheritColor` 4가지 플래그를 `document.documentElement`의 데이터 속성(`data-wt-inline-*`)으로 바인딩.
5. **툴바 빠른 설정 팝업 연동 및 실시간 반영 파이프라인 구축 (`optionPopup/index.js`):** [★★★★★] ✅ Verified 2026-08-20
   - 팝업 내 드롭다운 메뉴 및 슬라이더 조작 시 `notifyPreview` 메시지를 통해 열려 있는 탭에 즉시 스타일 업데이트 적용.

```javascript
// src/content/ui.js (테마 스타일 및 가독성 4대 옵션 동적 적용)
export function updateCustomStyles(settings) {
  if (!settings) return;
  var root = document.documentElement;
  var themeColor = settings.transColor || "#818cf8";
  var bgAlpha = settings.transBgAlpha !== undefined ? settings.transBgAlpha : 0.12;
  var textColor = getAutoTextColor(themeColor, bgAlpha);

  root.style.setProperty("--wt-theme-color", themeColor);
  root.style.setProperty("--wt-text-color", textColor);
  root.style.setProperty("--wt-trans-bg", hexToRgba(themeColor, bgAlpha));
  root.style.setProperty("--wt-trans-border", hexToRgba(themeColor, 0.45));

  // 옵션 1. 텍스트 이중 그림자 색상
  var glowColor = "rgba(255,255,255,0.9)";
  var match = textColor.match(/hsl\(\d+,\s*\d+%,\s*(\d+)%\)/);
  if (match && parseInt(match[1]) > 50) glowColor = "rgba(0,0,0,0.85)";
  root.style.setProperty("--wt-inline-glow-color", glowColor);

  if (settings.transFontSize) root.style.setProperty("--wt-trans-font-size", settings.transFontSize);
  if (settings.transItalic !== undefined) root.style.setProperty("--wt-trans-font-style", settings.transItalic ? "italic" : "normal");

  // 가독성 4대 옵션 데이터 속성 바인딩
  if (settings.inlineShadow) root.setAttribute("data-wt-inline-shadow", "true");
  else root.removeAttribute("data-wt-inline-shadow");

  if (settings.inlineHighlight) root.setAttribute("data-wt-inline-highlight", "true");
  else root.removeAttribute("data-wt-inline-highlight");

  if (settings.inlineAdaptiveColor) root.setAttribute("data-wt-inline-adaptive", "true");
  else root.removeAttribute("data-wt-inline-adaptive");

  if (settings.inlineInheritColor) root.setAttribute("data-wt-inline-inherit", "true");
  else root.removeAttribute("data-wt-inline-inherit");
}
```

```css
/* content.css (가독성 옵션별 CSS 규칙 및 우선순위 제어) */
/* 옵션 1. 텍스트 이중 그림자 (글로우) */
html[data-wt-inline-shadow="true"] .wt-translation,
html[data-wt-inline-shadow="true"] [class*="wt-dual-"] {
  text-shadow: 0 1px 2px var(--wt-inline-glow-color, rgba(255,255,255,0.8)),
               0 0 3px var(--wt-inline-glow-color, rgba(255,255,255,0.8));
}

/* 옵션 2. 미세한 인라인 배경 하이라이트 */
html[data-wt-inline-highlight="true"] .wt-translation.wt-inline,
html[data-wt-inline-highlight="true"] .wt-dual-inline {
  background-color: var(--wt-trans-bg) !important;
  padding: 0 4px;
  border-radius: 4px;
}

/* 옵션 4. 원문 글자 색상 따라가기 (환경 적응 옵션이 꺼져 있을 때만 적용) */
html[data-wt-inline-inherit="true"]:not([data-wt-inline-adaptive="true"]) .wt-translation,
html[data-wt-inline-inherit="true"]:not([data-wt-inline-adaptive="true"]) [class*="wt-dual-"] {
  color: var(--wt-inline-inherit-color, inherit) !important;
}

/* 옵션 3. 환경 적응 인라인 글자 색상 (최우선 적용) */
html[data-wt-inline-adaptive="true"] .wt-translation,
html[data-wt-inline-adaptive="true"] [class*="wt-dual-"] {
  color: var(--wt-inline-adaptive-color, #000) !important;
}
```

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[다크모드 사이트에서 번역문 텍스트가 배경에 묻혀 식별 불가 현상]**
  * 원인: 고정된 단일 텍스트 색상(예: 보라/파랑 계열)을 사용하거나 투명 배경을 상속받을 때 다크모드 웹사이트의 짙은 배경과 대비가 사라짐.
  * 해결법: 상위 DOM 트리를 순회하여 실제 불투명 배경색을 감지하고, YIQ 기반 보색 계산을 통해 배경 명도에 최적화된 텍스트 색상을 동적으로 부여함 (`inlineAdaptiveColor`). [FACT]
  * 신뢰도: [★★★★★]
* **[배경 그라데이션/이미지 영역에서 부모 노드의 엉뚱한 배경색을 참조하는 왜곡 오류]**
  * 원인: 요소 자체에 CSS 그라데이션이나 이미지가 적용되어 투명 배경색(`rgba(0,0,0,0)`)으로 판정되면, 루프가 멈추지 않고 최상단 부모의 배경색을 읽어와 실제 시각적 배경과 정반대의 글자색이 계산됨.
  * 해결법: `backgroundImage` 속성이 활성화된 요소를 만나면 즉시 부모 탐색을 중단하고, 원문 글자 색상의 명도를 분석하여 가상 배경을 역산하는 폴백 로직을 구축. [FACT]
  * 신뢰도: [★★★★★]
* **[중간 명도 회색(Mid-gray) 배경에서 반전 보색의 대비 부족 문제]**
  * 원인: RGB 값이 128 부근인 회색 배경의 경우 반전 색상도 128 부근의 회색이 되어 배경과 글자색의 YIQ 명도 차이가 거의 발생하지 않음.
  * 해결법: 배경과 반전색 간의 YIQ 차이가 60 미만일 때, 배경 명도 기준에 따라 RGB 값을 ±60만큼 강제로 밀어내는(Push) 명도 보정 로직을 추가. [FACT]
  * 신뢰도: [★★★★★]
* **[원문 상속 옵션과 환경 적응 색상 옵션의 동시 적용 시 CSS 충돌]**
  * 원인: 두 옵션이 모두 활성화되었을 때 CSS 계단식(Cascading) 규칙에 의해 의도치 않은 색상이 덮어씌워질 위험.
  * 해결법: CSS 선택자에 `:not([data-wt-inline-adaptive="true"])` 가드 조건을 적용하여 환경 적응 옵션이 항상 높은 우선순위를 가지도록 격리. [FACT]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] 가독성 개선의 핵심은 단일한 '완벽한 색상'을 강제하는 것이 아니라, 사용자가 웹사이트의 성격과 본인의 시력 환경에 맞춰 그림자, 배경 강조, 환경 적응, 색상 상속을 손쉽게 조합할 수 있는 유연한 옵션 체계를 제공하는 데 있다.
* [FACT] 블록 형태 번역문은 테마 색상과 투명도 슬라이더(`transBgAlpha`)만으로도 본문과의 구분이 충분히 명확하지만, 인라인 번역문은 주변 텍스트와 레이아웃을 공유하므로 4대 가독성 옵션의 지원이 필수적이다.
* [FACT] `window.getComputedStyle()`은 연산 비용이 발생하는 DOM 메서드이므로, 모든 노드가 아닌 번역 span 생성 시점의 실제 텍스트 컨테이너에 한정하여 탐색하고 조기 종료(Break) 조건을 꼼꼼히 설정하는 것이 렌더링 성능에 유리하다.

---

#### ❓ Missing Info (검증 필요 항목)

* [x] 복합 다크모드 웹사이트(Steam, GitHub, Wikipedia 등)에서 복잡한 다중 그라데이션 및 반투명 모달 레이어 환경의 글자 시인성 및 렌더링 안정성 검증 — Verified 2026-08-20
* [x] 차기 툴바 빠른 설정 팝업(`optionPopup.html`)과 연동 시 적응형 색상 옵션 토글 동작의 즉시 반영 여부 확인 — Verified 2026-08-20

---

## 📝 Feedback History

### 2026-08-20 — Test Result: PASS
* **환경:** Windows 11, Chrome Extension MV3, Steam / GitHub Dark / Wikipedia
* **검증된 단계:** 1단계 ~ 5단계 전체 검증 완료
* **피드백 내용:**
  1. 테마 색상(`transColor`) 유지 및 Block 형태 번역문의 배경 색상 조절 기능(`transBgAlpha` 슬라이더, `getAutoTextColor`) 상세 명시.
  2. 단순 오류 해결책 수준을 넘어, 최종 사용자 관점에서 적용되는 **인라인 가독성 4종 옵션 체계**(텍스트 이중 그림자, 배경색 강조, 글자색 환경 적응, 원문 글자 색상 상속)로의 연결 구조 완비.
  3. 옵션 간 충돌 방지를 위한 CSS `:not()` 선택자 우선순위 처리 및 CSS 변수 바인딩 파이프라인 정리.
  4. 툴바 빠른 설정 팝업([`optionPopup.html`](file:///d:/Noogs/NextCloud/Projects/WebTranslator/src/optionPopup/index.js))과의 실시간 렌더링 연동 검증 완료.
* **Status 변경:** Experimental → Verified

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, DarkMode, 다크모드, AdaptiveColor, 유효배경색, YIQ, CSS변수, 가독성옵션, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
