---
topic: webtranslator-15-dark-mode-effective-bg-and-adaptive-color
title_kr: 다크모드 가독성 붕괴와 상위 DOM 유효 배경색 역추적 및 적응형 보색 계산
category: Troubleshooting
sub_category: Adaptive-Theme-Engine
version: 2.2.0
status: Experimental
created_date: 2026-08-20
last_modified: 2026-08-20
language: KR+EN
tags: [WebTranslator, ChromeExtension, 크롬확장프로그램, DarkMode, 다크모드, AdaptiveColor, 유효배경색, YIQ, CSS변수, 트러블슈팅]
sources_count: 3
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
* **Title_KR:** 다크모드 가독성 붕괴와 상위 DOM 유효 배경색 역추적 및 적응형 보색 계산
* **Category:** Troubleshooting
* **Sub-Category:** Adaptive-Theme-Engine
* **Version:** 2.2.0
* **Status:** Experimental
* **Date:** 2026-08-20
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
* [★★★★★] WebTranslator 다크모드 가독성 엔진 설계 대화 로그 (`37bafb78-0ac0-4e1a-af49-e3230b742da0`)
* [★★★★★] WebTranslator DOM 렌더링 및 유효 배경색 탐색기 소스 코드 (`src/content/dom.js:L401-L469`, 커밋 `2af2d25`, `b49e42e`, `fe87483`)
* [★★★★★] WebTranslator 동적 스타일 및 인라인 가독성 CSS 정의 (`content.css:L66-L95`)

---

#### 🔑 Core Concepts (핵심 개념)

* **[다크모드 및 다양한 웹 테마에서의 가독성 붕괴 문제]:** 스팀(Steam) 커뮤니티나 깃허브(GitHub) 다크모드 같은 어두운 배경 사이트에서 인라인 번역을 실행하면 번역문 글자가 어두운 배경에 묻혀 식별이 불가능해지고, 반대로 밝은 사이트(위키피디아 등)에 흰색 고정 텍스트를 적용하면 흰 배경에 묻히는 가독성 붕괴 현상이 발생함. [FACT]
* **[수동 컬러피커 제안의 한계와 지시 번복]:** 옵션 페이지에 사용자가 직접 글자 색을 선택하는 Color Picker를 두는 방식은 웹사이트를 이동할 때마다 사용자가 수동으로 설정을 변경해야 하므로 사용자 경험을 심각하게 저해함. 이에 수동 설정을 전면 취소하고 확장 프로그램이 페이지 환경을 스스로 감지하여 텍스트 색상을 자동 계산하는 적응형 엔진으로 방향을 전환. [FACT]
* **[상위 DOM 유효 배경색(Effective Background Color) 역추적]:** 웹 페이지의 텍스트 요소들은 대부분 자체 배경색이 투명(`transparent` 또는 `rgba(0, 0, 0, 0)`)으로 설정되어 있음. 실제 눈에 보이는 배경색을 찾기 위해 현재 텍스트 노드의 부모 요소를 상위(DOM Tree)로 거슬러 올라가며 유효한 불투명 배경색(`alpha > 0.1`)을 탐색하는 알고리즘을 도입. [FACT]
* **[`background-image` 예외 감지 및 원문 글자색 기반 폴백]:** 상위 탐색 중 CSS 그라데이션이나 배경 이미지(`background-image !== "none"`)가 지정된 요소를 만나면, 이미지 뒤에 숨겨진 엉뚱한 부모 배경색을 가져와 대비 계산이 왜곡되는 문제를 방지하기 위해 탐색을 즉시 중단하고 원문 글자색(`computedStyle.color`)의 명도를 분석하여 반전시키는 폴백 로직을 적용. [FACT]
* **[YIQ 상대 명도 및 중간 회색(Mid-gray) 대비 강제 보정]:**
  * YIQ 공식(`(r * 299 + g * 587 + b * 114) / 1000`)을 기반으로 배경색의 명도를 분석하고 반전 보색(`255 - RGB`)을 계산. [FACT]
  * 배경색이 중간 회색(Mid-gray)에 가까워 반전 색상과의 명도 차이가 60 미만인 경우, 배경 명도(YIQ > 128 여부)에 따라 반전 RGB 값을 ±60만큼 추가 푸시(`pushAmt`)하여 시인성을 확보. [FACT]
* **[CSS Custom Property(CSS 변수) 기반 경량 주입]:** 계산된 적응형 색상과 그림자 색상을 개별 인라인 스타일에 직접 하드코딩하지 않고, CSS 변수(`--wt-inline-adaptive-color`, `--wt-inline-glow-color`)로 span 요소에 동적 할당하여 성능 부하를 최소화. [FACT]

---

#### 🛠️ Procedures (절차)

1. **인라인 번역 요소의 실제 텍스트 컨테이너 특정 (`dom.js`):** [★★★★★]
   - `createTreeWalker`를 통해 요소 내 실제 텍스트 노드를 감싸는 요소를 탐색.
   - 단, 전체 텍스트 중 일부만 `<a>` 태그인 경우 파란색 하이퍼링크 색상이 번역문 전체로 전파되는 왜곡을 방지하기 위해 `<a>` 내부 텍스트를 우선 분리 처리.
2. **상위 DOM 순회를 통한 유효 배경색 탐색:** [★★★★★]
   - `while` 루프로 상위 부모 노드를 탐색하며 `window.getComputedStyle(bgNode)`의 `backgroundColor` 확인.
   - 알파 채널이 0.1보다 큰 불투명 배경색이 발견되면 해당 색상을 유효 배경색으로 확정.
3. **배경 이미지 및 그라데이션 예외 차단:** [★★★★★]
   - 탐색 도중 `backgroundImage`가 `none`이나 `initial`이 아닌 요소를 만나면 부모 탐색을 즉시 중단.
   - 텍스트의 `computedStyle.color`를 추출하여 YIQ 명도 분석 후 가상 배경색(밝은 텍스트면 검은 배경, 어두운 텍스트면 흰 배경)으로 폴백.
4. **보색 계산 및 중간 회색 대비 보정:** [★★★★★]
   - 추출된 배경색 RGB를 반전(`invR = 255 - r, invG = 255 - g, invB = 255 - b`) 처리.
   - `Math.abs(yiq - invYiq) < 60` 조건으로 중간 명도 영역을 감지하고, 배경이 밝으면 반전 글자를 더 어둡게(`Math.max(0, inv - 60)`), 배경이 어두우면 더 밝게(`Math.min(255, inv + 60)`) 보정.
5. **CSS 변수 바인딩 및 가독성 옵션 적용 (`content.css`):** [★★★★★]
   - `span.style.setProperty("--wt-inline-adaptive-color", ...)`로 동적 값 주입.
   - CSS의 `html[data-wt-inline-adaptive="true"]` 선택자를 통해 자동 적응형 색상이 최우선 적용되도록 스타일 규칙 구성.

```javascript
// src/content/dom.js (환경 적응 색상 및 유효 배경색 탐색 로직)
if (state.cachedSettings?.inlineAdaptiveColor) {
  try {
    var bgNode = actualTextElement;
    var bgColor = null;
    while (bgNode && bgNode.nodeType === Node.ELEMENT_NODE) {
      var computedBg = window.getComputedStyle(bgNode);
      var bg = computedBg.backgroundColor;
      var bgImg = computedBg.backgroundImage;
      var aMatch = bg.match(/rgba\([^,]+,[^,]+,[^,]+,\s*([^)]+)\)/);
      
      if (!aMatch || parseFloat(aMatch[1]) > 0.1) {
        if (bg !== "rgba(0, 0, 0, 0)" && bg !== "transparent") {
          bgColor = bg;
          break;
        }
      }
      // 배경색은 투명하지만 background-image(그라데이션 등)가 존재하면
      // 부모의 배경색을 가져올 때 잘못된 대비가 계산되므로 탐색을 중단
      if (bgImg && bgImg !== "none" && bgImg !== "initial") {
        break;
      }
      bgNode = bgNode.parentElement;
    }
    
    // 배경색을 찾지 못했거나 배경 이미지가 있는 경우 원문 글자 색상 기준으로 폴백
    if (!bgColor) {
      var textColor = window.getComputedStyle(actualTextElement).color;
      var tcMatch = textColor.match(/\d+/g);
      if (tcMatch && tcMatch.length >= 3) {
        var tr = parseInt(tcMatch[0]), tg = parseInt(tcMatch[1]), tb = parseInt(tcMatch[2]);
        var tYiq = (tr * 299 + tg * 587 + tb * 114) / 1000;
        bgColor = tYiq > 128 ? "rgb(0, 0, 0)" : "rgb(255, 255, 255)";
      } else {
        bgColor = "rgb(255, 255, 255)";
      }
    }

    var rgbMatch = bgColor.match(/\d+/g);
    if (rgbMatch && rgbMatch.length >= 3) {
      var r = parseInt(rgbMatch[0]), g = parseInt(rgbMatch[1]), b = parseInt(rgbMatch[2]);
      
      // 배경색의 반전 색상(보색) 계산
      var invR = 255 - r;
      var invG = 255 - g;
      var invB = 255 - b;
      
      var yiq = (r * 299 + g * 587 + b * 114) / 1000;
      var invYiq = (invR * 299 + invG * 587 + invB * 114) / 1000;
      
      // 중간 회색(mid-gray) 영역 명도 차이 부족 시 대비 강제 보정
      if (Math.abs(yiq - invYiq) < 60) {
        var pushAmt = 60;
        if (yiq > 128) {
          invR = Math.max(0, invR - pushAmt);
          invG = Math.max(0, invG - pushAmt);
          invB = Math.max(0, invB - pushAmt);
        } else {
          invR = Math.min(255, invR + pushAmt);
          invG = Math.min(255, invG + pushAmt);
          invB = Math.min(255, invB + pushAmt);
        }
      }
      
      span.style.setProperty("--wt-inline-adaptive-color", `rgb(${invR}, ${invG}, ${invB})`);
    }
  } catch(e) {}
}
```

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **[다크모드 사이트에서 번역문 텍스트가 배경에 묻혀 식별 불가 현상]**
  * 원인: 고정된 단일 텍스트 색상(예: 보라/파랑 계열)을 사용하거나 투명 배경을 상속받을 때 다크모드 웹사이트의 짙은 배경과 대비가 사라짐.
  * 해결법: 상위 DOM 트리를 순회하여 실제 불투명 배경색을 감지하고, YIQ 기반 보색 계산을 통해 배경 명도에 최적화된 텍스트 색상을 동적으로 부여함. [FACT]
  * 신뢰도: [★★★★★]
* **[배경 그라데이션/이미지 영역에서 부모 노드의 엉뚱한 배경색을 참조하는 왜곡 오류]**
  * 원인: 요소 자체에 CSS 그라데이션이나 이미지가 적용되어 투명 배경색(`rgba(0,0,0,0)`)으로 판정되면, 루프가 멈추지 않고 최상단 부모의 배경색을 읽어와 실제 시각적 배경과 정반대의 글자색이 계산됨.
  * 해결법: `backgroundImage` 속성이 활성화된 요소를 만나면 즉시 부모 탐색을 중단하고, 원문 글자 색상의 명도를 분석하여 가상 배경을 역산하는 폴백 로직을 구축. [FACT]
  * 신뢰도: [★★★★★]
* **[중간 명도 회색(Mid-gray) 배경에서 반전 보색의 대비 부족 문제]**
  * 원인: RGB 값이 128 부근인 회색 배경의 경우 반전 색상도 128 부근의 회색이 되어 배경과 글자색의 YIQ 명도 차이가 거의 발생하지 않음.
  * 해결법: 배경과 반전색 간의 YIQ 차이가 60 미만일 때, 배경 명도 기준에 따라 RGB 값을 ±60만큼 강제로 밀어내는(Push) 명도 보정 로직을 추가. [FACT]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [OPINION] 사용자가 사이트마다 옵션을 열고 글자색을 고르게 만드는 방식은 명백한 안티패턴이다. 확장 프로그램은 별도의 설정 조작 없이도 브라우징 환경에 스스로 적응해야 한다.
* [FACT] `window.getComputedStyle()`은 연산 비용이 발생하는 DOM 메서드이므로, 모든 노드가 아닌 번역 span 생성 시점의 실제 텍스트 컨테이너에 한정하여 탐색하고 조기 종료(Break) 조건을 꼼꼼히 설정하는 것이 렌더링 성능에 유리하다.
* [FACT] `<a>` 태그 내부 텍스트와 일반 텍스트가 섞여 있을 때 `TreeWalker`에서 `<a>` 노드를 적절히 필터링하지 않으면 링크의 파란색 텍스트가 번역문 전체의 기준 색상으로 오인될 수 있으므로 주의해야 한다.

---

#### ❓ Missing Info (검증 필요 항목)

* [ ] 복합 다크모드 웹사이트(Steam, GitHub, Wikipedia 등)에서 복잡한 다중 그라데이션 및 반투명 모달 레이어 환경의 글자 시인성 및 렌더링 안정성 검증
* [ ] 차기 툴바 빠른 설정 팝업(`optionPopup.html`)과 연동 시 적응형 색상 옵션 토글 동작의 즉시 반영 여부 확인

---

#### 🏷️ Tags
WebTranslator, ChromeExtension, 크롬확장프로그램, DarkMode, 다크모드, AdaptiveColor, 유효배경색, YIQ, CSS변수, 트러블슈팅

===== KNOWLEDGE PACKAGE END =====
