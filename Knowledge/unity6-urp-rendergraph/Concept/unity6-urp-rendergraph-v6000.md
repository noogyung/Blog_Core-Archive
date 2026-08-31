---
topic: unity6-urp-rendergraph
title_kr: Unity 6 URP RenderGraph 핵심 분석: 아키텍처와 실전 API 가이드
category: Concept
sub_category: Graphics
version: v6000.0
status: Experimental
created_date: 2026-08-31
last_modified: 2026-08-31
language: KR+EN
tags: [Unity, Unity6, URP, RenderGraph, Graphics, Shader, RenderingPipeline, Optimization]
sources_count: 5
blog_draft_path: null
blog_draft_date: null
blog_id: core-archive
blog_published: false
series_id: null
---

===== KNOWLEDGE PACKAGE START =====

### 📦 [Knowledge Package]

* **Topic:** unity6-urp-rendergraph
* **Title_KR:** Unity 6 URP RenderGraph 핵심 분석: 아키텍처와 실전 API 가이드
* **Category:** Concept
* **Sub-Category:** Graphics
* **Version:** v6000.0
* **Status:** Experimental
* **Date:** 2026-08-31
* **Language:** KR+EN

---

#### 📚 Sources & Confidence
  * [★★★★★] Unity 6 공식 매뉴얼 - Render Graph System Introduction: https://docs.unity3d.com/6000.0/Documentation/Manual/urp/render-graph-introduction.html
  * [★★★★★] Unity 6 공식 매뉴얼 - Writing a Custom Render Pass with Render Graph: https://docs.unity3d.com/6000.0/Documentation/Manual/urp/render-graph-write-render-pass.html
  * [★★★★★] Unity 6 공식 매뉴얼 - Texture Management and Pass Culling: https://docs.unity3d.com/6000.0/Documentation/Manual/urp/render-graph-pass-culling.html
  * [★★★★☆] Frostbite Engine GDC 2017 - FrameGraph: Extensible Rendering Architecture: https://www.gdcvault.com/play/1024656/Advanced-Graphics-Tech-Moving-to
  * [★★★★☆] Unity Technologies Blog - Unity 6 Graphics Improvements & URP Updates: https://unity.com/blog

---

#### 🔑 Core Concepts (핵심 개념)

##### 1. RenderGraph의 정의 및 아키텍처 (Architecture & 3-Step Pipeline)
* **개념:** Unity 6 URP의 기본 렌더링 백엔드로 전면 채택된 프레임 그래프(Frame Graph) 기반의 렌더링 파이프라인 추상화 시스템. 렌더 패스(Render Pass)와 리소스 의존성을 명시적으로 선언하여 GPU 커맨드를 최적 스케줄링함. [FACT]
* **기존 ScriptableRenderPass(명령형)와의 차이:** 기존 방식은 GPU 커맨드를 즉시 큐에 넣는 명령형(Imperative) 방식이었으나, RenderGraph는 패스와 리소스 사용 관계를 먼저 선언하고 최적화 후 실행하는 선언형(Declarative) 패러다임임. [FACT]
* **3단계 실행 파이프라인:**
  1. **Record 단계:** 각 렌더 패스가 어떤 리소스를 읽고(Read) 쓰는지(Write) 명시적으로 선언함. CPU에서 실행됨.
  2. **Compile 단계:** 렌더 그래프의 의존성 DAG(Directed Acyclic Graph)를 분석하여 최종 타겟에 기여하지 않는 패스를 컬링(Culling)하고, 생명 주기가 겹치지 않는 임시 리소스들의 메모리를 병합(Aliasing)함.
  3. **Execute 단계:** 컴파일된 순서에 따라 실제 GPU 렌더링 커맨드를 일괄 기록 및 디스패치함. [FACT]

##### 2. 패스 유형 (Pass Types)
* **Raster Render Pass (`AddRasterRenderPass`):** 일반적인 래스터화 렌더링(DrawMesh, DrawRendererList, Blit 등). Native Render Pass와 결합하여 타일 메모리 최적화 지원. [FACT]
* **Compute Pass (`AddComputePass`):** Compute Shader를 디스패치하고 UAV 버퍼/텍스처를 조작하는 전용 패스. [FACT]
* **Unsafe Pass (`AddUnsafePass`):** 직접 `CommandBuffer` 및 네이티브 컨텍스트에 접근해야 하는 레거시/특수 목적용 패스. 의존성 자동 최적화가 제한되므로 사용 최소화 권장. [FACT]

##### 3. 리소스 핸들과 수명 주기 (Resource Handles & Lifecycle)
* **Resource Handle (`TextureHandle`, `BufferHandle`):** 실제 GPU 리소스를 가리키는 경량 핸들 객체. [FACT]
* **Transient Resources (임시 리소스):** `renderGraph.CreateTexture()`로 생성되는 프레임 전용 리소스. 프레임 종료 시 Texture Pool에 자동 반납되며, 생명 주기가 겹치지 않는 다른 패스의 리소스와 GPU 메모리를 공유(Aliasing)함. [FACT]
* **Imported Resources (외부 리소스):** `renderGraph.ImportTexture()` 또는 `ImportBackbuffer()`를 통해 그래프 외부의 RTHandle이나 백버퍼를 핸들로 변환하여 사용. [FACT]
* **AccessFlags:** `AccessFlags.Read`, `AccessFlags.Write`, `AccessFlags.ReadWrite`를 통해 명시적으로 접근 권한을 선언하여 암묵적 동기화 배리어 및 메모리 덮어쓰기 버그를 방지. [FACT]

##### 4. Unity 6 변경사항 및 프레임워크 갱신
* **ContextContainer:** 기존 `RenderingData` 구조체를 대체하는 타입 안전 프레임 데이터 컨테이너. `frameData.Get<UniversalResourceData>()`, `frameData.Get<UniversalCameraData>()` 등으로 필요한 데이터만 세분화하여 조회. [FACT]
* **Blitter API:** 기존 `cmd.Blit()`을 전면 대체하며, `Blitter.BlitCameraTexture()` 및 `Blitter.BlitTexture()`를 통해 풀스크린 Quad 드로우를 표준화. [FACT]
* **RenderGraph Viewer:** Unity Editor의 `Window > Analysis > Render Graph Viewer`를 통해 프레임별 패스 연결 관계, 컬링 여부, 리소스 메모리 Aliasing 현황을 시각적으로 디버깅 가능. [FACT]
* **모바일 타일 GPU 최적화:** RenderPass 간 동일 타겟 사용 시 Vulkan/Metal Subpass와 FrameBuffer Fetch를 자동 활성화하여 DRAM 대역폭 소비를 최소화. [FACT]

---

#### 🛠️ Procedures (절차)

##### 1. 기본 Custom RenderPass 구조 작성 (Unity 6 표준)
```csharp
using UnityEngine;
using UnityEngine.Rendering;
using UnityEngine.Rendering.Universal;
using UnityEngine.Rendering.RenderGraphModule;

public class CustomColorInvertPass : ScriptableRenderPass
{
    private Material _material;

    // 1. PassData는 반드시 참조 형식(class)으로 선언
    private class PassData
    {
        public TextureHandle sourceTexture;
        public TextureHandle destinationTexture;
        public Material passMaterial;
    }

    public CustomColorInvertPass(Material mat)
    {
        _material = mat;
        renderPassEvent = RenderPassEvent.AfterRenderingPostProcessing;
    }

    // 2. Unity 6 RenderGraph 진입점 구현
    public override void RecordRenderGraph(RenderGraph renderGraph, ContextContainer frameData)
    {
        UniversalResourceData resourceData = frameData.Get<UniversalResourceData>();
        UniversalCameraData cameraData = frameData.Get<UniversalCameraData>();

        if (resourceData.isActiveTargetBackBuffer)
            return;

        TextureHandle source = resourceData.activeColorTexture;

        // 3. Transient 중간 텍스처 생성
        var desc = renderGraph.GetTextureDesc(source);
        desc.name = "Invert_Intermediate";
        desc.clearBuffer = false;
        TextureHandle intermediate = renderGraph.CreateTexture(desc);

        // 4. Raster Render Pass 등록 (Pass 1: Source -> Intermediate)
        using (var builder = renderGraph.AddRasterRenderPass<PassData>("Invert Color Pass", out var passData))
        {
            passData.sourceTexture = source;
            passData.destinationTexture = intermediate;
            passData.passMaterial = _material;

            // 의존성 명시
            builder.UseTexture(source, AccessFlags.Read);
            builder.SetRenderAttachment(intermediate, 0, AccessFlags.Write);

            // 5. 실행 함수 (람다 캡처 금지, passData만 사용)
            builder.SetRenderFunc((PassData data, RasterGraphContext ctx) =>
            {
                Blitter.BlitTexture(ctx.cmd, data.sourceTexture, new Vector4(1, 1, 0, 0), data.passMaterial, 0);
            });
        }

        // 6. Writeback Pass 등록 (Pass 2: Intermediate -> Source)
        using (var builder = renderGraph.AddRasterRenderPass<PassData>("Invert Writeback Pass", out var passData))
        {
            passData.sourceTexture = intermediate;
            passData.destinationTexture = source;
            passData.passMaterial = _material;

            builder.UseTexture(intermediate, AccessFlags.Read);
            builder.SetRenderAttachment(source, 0, AccessFlags.Write);

            builder.SetRenderFunc((PassData data, RasterGraphContext ctx) =>
            {
                Blitter.BlitTexture(ctx.cmd, data.sourceTexture, new Vector4(1, 1, 0, 0), data.passMaterial, 1);
            });
        }
    }
}
```

##### 2. Compute Shader Pass 등록 절차
```csharp
private class ComputePassData
{
    public TextureHandle inputTexture;
    public TextureHandle outputTexture;
    public ComputeShader computeShader;
    public int kernelIndex;
    public Vector2Int threadGroups;
}

public override void RecordRenderGraph(RenderGraph renderGraph, ContextContainer frameData)
{
    var resourceData = frameData.Get<UniversalResourceData>();
    var cameraData = frameData.Get<UniversalCameraData>();

    int width = cameraData.cameraTargetDescriptor.width;
    int height = cameraData.cameraTargetDescriptor.height;

    var outputDesc = new TextureDesc(width, height)
    {
        colorFormat = GraphicsFormat.R16G16B16A16_SFloat,
        enableRandomWrite = true,
        name = "ComputeUAVOutput"
    };
    TextureHandle outputHandle = renderGraph.CreateTexture(outputDesc);

    using (var builder = renderGraph.AddComputePass<ComputePassData>("Custom Compute Pass", out var passData))
    {
        passData.inputTexture = resourceData.activeColorTexture;
        passData.outputTexture = outputHandle;
        passData.computeShader = myComputeShader;
        passData.kernelIndex = myComputeShader.FindKernel("CSMain");
        passData.threadGroups = new Vector2Int(Mathf.CeilToInt(width / 8f), Mathf.CeilToInt(height / 8f));

        builder.UseTexture(passData.inputTexture, AccessFlags.Read);
        builder.UseTexture(passData.outputTexture, AccessFlags.Write);

        builder.SetRenderFunc((ComputePassData data, ComputeGraphContext ctx) =>
        {
            ctx.cmd.SetComputeTextureParam(data.computeShader, data.kernelIndex, "_InputTex", data.inputTexture);
            ctx.cmd.SetComputeTextureParam(data.computeShader, data.kernelIndex, "_OutputTex", data.outputTexture);
            ctx.cmd.DispatchCompute(data.computeShader, data.kernelIndex, data.threadGroups.x, data.threadGroups.y, 1);
        });
    }
}
```

##### 3. RendererList 기반 지오메트리 렌더링 패스
```csharp
private class DepthPassData
{
    public TextureHandle depthTarget;
    public RendererListHandle rendererList;
}

public override void RecordRenderGraph(RenderGraph renderGraph, ContextContainer frameData)
{
    var renderingData = frameData.Get<UniversalRenderingData>();
    var cameraData = frameData.Get<UniversalCameraData>();
    var lightData = frameData.Get<UniversalLightData>();

    var depthDesc = new TextureDesc(cameraData.cameraTargetDescriptor.width, cameraData.cameraTargetDescriptor.height)
    {
        depthBufferBits = DepthBits.Depth32,
        colorFormat = GraphicsFormat.None,
        name = "CustomDepthBuffer"
    };

    using (var builder = renderGraph.AddRasterRenderPass<DepthPassData>("Custom Depth Pass", out var passData))
    {
        passData.depthTarget = renderGraph.CreateTexture(depthDesc);
        builder.SetRenderAttachmentDepth(passData.depthTarget, AccessFlags.Write);

        // RendererList 생성 및 바인딩
        var sortSettings = new SortingSettings(cameraData.camera) { criteria = SortingCriteria.CommonOpaque };
        var drawSettings = RenderingUtils.CreateDrawingSettings(new ShaderTagId("DepthOnly"), renderingData, cameraData, lightData, sortSettings);
        var filterSettings = new FilteringSettings(RenderQueueRange.opaque);

        passData.rendererList = renderGraph.CreateRendererList(new RendererListParams(renderingData.cullResults, drawSettings, filterSettings));
        builder.UseRendererList(passData.rendererList);

        builder.SetRenderFunc((DepthPassData data, RasterGraphContext ctx) =>
        {
            ctx.cmd.DrawRendererList(data.rendererList);
        });
    }
}
```

---

#### 🐛 Errors & Solutions (오류 및 해결법)

* **람다 내 외부 변수 캡처로 인한 런타임 크래시 또는 미정의 동작**
  * 원인: `SetRenderFunc`의 람다 식 내부에서 클래스 멤버 변수나 로컬 스코프 변수(`this`, `myMaterial` 등)를 직접 참조할 경우, Execute 시점의 컨텍스트 분리로 인해 GC 캡처 할당 및 경쟁 상태 발생.
  * 해결법: 필요한 모든 데이터(Material, Mesh, Matrix, Params)는 `PassData` 객체의 필드로 할당하고 람다 인자인 `data`를 통해서만 접근해야 함. [FACT]
  * 신뢰도: [★★★★★]

* **TextureHandle 프레임 간 캐싱 시 Invalid Handle 예외**
  * 원인: `TextureHandle` 및 `RendererListHandle`은 `RecordRenderGraph`가 실행되는 단일 프레임 내에서만 유효한 임시 식별자임.
  * 해결법: 인스턴스 멤버 변수에 `TextureHandle`을 영구 보관하지 말고 매 프레임 `RecordRenderGraph` 내에서 새로 획득/생성해야 함. [FACT]
  * 신뢰도: [★★★★★]

* **PassData를 struct(값 형식)로 선언하여 발생하는 제네릭 제약 컴파일 오류**
  * 원인: RenderGraph 내부에서 풀링 및 메모리 관리를 위해 `where PassData : class, new()` 제약을 강제함.
  * 해결법: `PassData` 선언부를 `class`로 정의. [FACT]
  * 신뢰도: [★★★★★]

* **작성한 커스텀 패스가 화면에 전혀 실행되지 않는 현상 (Pass Culling)**
  * 원인: 패스의 출력 텍스처를 후속 패스나 최종 카메라 백버퍼에서 전혀 읽지 않을 경우 RenderGraph 컴파일러가 해당 패스를 불필요한 작업으로 판단하여 자동 컬링함.
  * 해결법: 최종 컬러 버퍼로 Writeback을 수행하거나, 디버깅 목적일 경우 `builder.AllowPassCulling(false)`를 호출하여 컬링을 강제 비활성화. [FACT]
  * 신뢰도: [★★★★★]

---

#### 💬 Experiences & Tips (경험 및 팁)

* [FACT] **Compatibility Mode는 임시 전환용:** URP Asset 인스펙터에서 `Render Graph` 체크박스를 해제하면 기존 `ScriptableRenderPass.Execute()` 방식으로 롤백할 수 있으나, 향후 Unity 메이저 버전에서 제거될 예정이므로 신규 개발은 RenderGraph 기반으로 작성해야 함.
* [FACT] **Blitter API 활용:** `cmd.Blit()`은 타일 기반 GPU에서 불필요한 Resolve 및 성능 저하를 유발하므로 반드시 `Blitter.BlitCameraTexture()` 또는 `Blitter.BlitTexture()`를 사용해야 함.
* [FACT] **RenderGraph Viewer 활용:** 드로우콜 병합 및 메모리 Aliasing이 정상 작동하는지 `Window > Analysis > Render Graph Viewer`에서 프레임별 리소스 그래프를 직접 점검하는 습관이 중요함.

---

#### ❓ Missing Info (검증 필요 항목)
  * [ ] 대규모 씬에서 `RendererListHandle`을 사용한 지오메트리 렌더링 시 기존 `DrawingSettings` 대비 드로우콜 레코딩 CPU 오버헤드 실측치 비교 검증
  * [ ] Android Vulkan 및 iOS Metal 타겟 빌드에서 `SetRenderAttachment`의 Subpass 자동 병합으로 인한 메모리 대역폭 절감치 프로파일링

---

#### 🏷️ Tags
Unity, Unity6, URP, RenderGraph, Graphics, Shader, RenderingPipeline, Optimization, ScriptableRenderPass, FrameGraph

===== KNOWLEDGE PACKAGE END =====

---
## 📝 Feedback History

### 2026-08-31 — Content Refinement
* **수정 내용:** 전역 가드레일 준수를 위한 과장 및 단정적 수식어 수정 ('완전' → '핵심', '100%' 제거)
* **Status 변경:** Experimental 유지
