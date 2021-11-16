---
title: Late Stage Reprojection
description: Late Stage Reprojection とその使用方法に関する情報。
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 13a950f67053d9eaaea39e2df34df3dd12ed00c1
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028730"
---
# <a name="late-stage-reprojection"></a>Late Stage Reprojection

*Late Stage Reprojection* (LSR) は、ユーザーが移動したときにホログラムを安定化するのに役立つハードウェア機能です。

静的モデルは、移動時に位置を視覚的に維持する必要があります。 これが不安定であるように見える場合、この動作は LSR の問題を示唆している可能性があります。 アニメーションや分解図などの追加の動的な変形によって、この動作がマスクされる可能性があることに注意してください。

2つの異なる LSR モード (**Planar LSR** または **Depth LSR**) を選択できます。 どちらの LSR モードでも、ホログラムの安定性が向上しますが、それぞれに独自の制限があります。 まず、Depth LSR を試してみてください。ほとんどの場合、より良い結果が得られます。

## <a name="how-to-set-the-lsr-mode"></a>LSR モードを設定する方法

どの LSR モードを使用するかは、クライアント アプリケーションが深度バッファーを送信するかどうかによって決まります。 深度バッファーが送信されると **Depth LSR**、それ以外の場合は **Planar LSR** が使用されます。

次の段落では、Unity とネイティブ アプリケーションでそれぞれ深度バッファーの送信がどのように行われるかについて説明します。

### <a name="unity"></a>Unity

Unity エディターの *:::no-loc text="File > Build Settings":::* に移動します。 左下にある *:::no-loc text="Player Settings":::* を選択し、 *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* で **:::no-loc text="Enable Depth Buffer Sharing":::** がオンになっているかどうかを確認します。

![深度バッファーの共有を有効にするフラグ](./media/unity-depth-buffer-sharing-enabled.png)

オンである場合、アプリでは Depth LSR が使用されます。それ以外の場合は、Planar LSR が使用されます。

OpenXR を使用する場合は、常に深度バッファーを送信する必要があります。 この設定は、 *:::no-loc text="XR Plug-in Management > OpenXR":::* にあります。 次に、OpenXR プラグインの拡張機能によって、再投影モードを変更できます。

```cs
using Microsoft.MixedReality.OpenXR;

public class OverrideReprojection : MonoBehaviour
{
    void OnEnable()
    {
        RenderPipelineManager.endCameraRendering += RenderPipelineManager_endCameraRendering;
    }
    void OnDisable()
    {
        RenderPipelineManager.endCameraRendering -= RenderPipelineManager_endCameraRendering;
    }

    // When using the Universal Render Pipeline, OnPostRender has to be called manually.
    private void RenderPipelineManager_endCameraRendering(ScriptableRenderContext context, Camera camera)
    {
        OnPostRender();
    }

    // Called directly when using Unity's legacy renderer.
    private void OnPostRender()
    {
        ReprojectionSettings reprojectionSettings = default;
        reprojectionSettings.ReprojectionMode = ReprojectionMode.PlanarManual; // Or your favorite reprojection mode.
        
        // In case of PlanarManual you also need to provide a focus point here.
        reprojectionSettings.ReprojectionPlaneOverridePosition = ...;
        reprojectionSettings.ReprojectionPlaneOverrideNormal = ...;
        reprojectionSettings.ReprojectionPlaneOverrideVelocity = ...;

        foreach (ViewConfiguration viewConfiguration in ViewConfiguration.EnabledViewConfigurations)
        {
            if (viewConfiguration.IsActive && viewConfiguration.SupportedReprojectionModes.Contains(reprojectionSettings.ReprojectionMode))
            {
                viewConfiguration.SetReprojectionSettings(reprojectionSettings);
            }
        }
    }
}
```

### <a name="native-c-applications"></a>ネイティブ C++ アプリケーション

深度バッファーの送信は、WMR または OpenXR のバージョンとは関係なく、ネイティブ C++ バインド コードによって完全に制御されます。 満たす必要のある条件は、`GraphicsBinding::BlitRemoteFrame` が呼び出されたときに、深度バッファーをグラフィックス API にバインドしなければならないことだけです。

## <a name="depth-lsr"></a>Depth LSR

Depth LSR を機能させるには、クライアント アプリケーションが、LSR 中に考慮する必要があるすべての関連ジオメトリを含む有効な深度バッファーを指定する必要があります。

Depth LSR は、指定された深度バッファーの内容に基づいて、ビデオ フレームの安定化を試みます。 結果として、透明なオブジェクトなど、レンダリングされていないコンテンツは、LSR によって調整することはできず、不安定で再投影される可能性があります。

透過的なオブジェクトの再プロジェクションの不安定性を軽減するには、深度バッファーの書き込みを強制することができます。 [カラー](color-materials.md) および [PBR](pbr-materials.md) のマテリアルについては、*TransparencyWritesDepth* のマテリアル フラグを参照してください。 ただし、このフラグを有効にすると、透明/不透明なオブジェクトの相互作用のビジュアル品質が低下する可能性があります。

## <a name="planar-lsr"></a>Planar LSR

Planar LSR には、Depth LSR のように、ピクセルごとの深度情報は含まれません。 代わりに、各フレームで提供する必要がある平面に基づいてすべてのコンテンツを再投影します。

Planar LSR は、指定された平面に近いオブジェクトを適切に再投影します。 オブジェクトが離れていればいるほど、不安定になります。 Depth LSR の方が、異なる深さでオブジェクトを再投影することに優れていますが、Planar LSR は、平面と整列しているコンテンツに適しています。

### <a name="configure-planar-lsr-in-unity"></a>Unity で Planar LSR を構成する

プレーン パラメーターは、いわゆる *フォーカス ポイント* から派生しています。 WMR を使用する場合、フォーカス ポイントを `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` によってフレームごとに設定する必要があります。 詳細については、[Unity のフォーカス ポイント API](/windows/mixed-reality/focus-point-in-unity) に関するページを参照してください。 OpenXR の場合、フォーカス ポイントは、前のセクションで示した `ReprojectionSettings` を使用して設定する必要があります。
フォーカス ポイントを設定しない場合は、フォールバックが自動で選択されます。 ただし多くの場合、自動フォールバックの結果は最適ではありません。

フォーカス ポイントは自分で計算できますが、Remote Rendering ホストによって計算されたものを基にした方が適切な場合もあります。 `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` を呼び出してこれを取得します。

通常、クライアントとホストの両方が、もう片方が認識していないコンテンツ (クライアント上の UI 要素など) を表示します。 そのため、リモートのフォーカス ポイントをローカルで計算されたフォーカス ポイントと結合することは理にかなっていると考えられます。

2 つの連続するフレームで計算されたフォーカス ポイントは、大きく異なる場合があります。 単にそのまま使用すると、ホログラムがあちこち飛び回って表示される可能性があります。 この動作を回避するには、前のフォーカス ポイントと現在のフォーカス ポイントを補間することをお勧めします。

## <a name="reprojection-pose-modes"></a>再プロジェクションのポーズ モード

ハイブリッド レンダリングにおける一般的な問題として、リモート ポーズ (つまり座標空間) の内容はサーバーによって予測されるのに対して、ローカル ポーズは実際の現在の内容であるため、リモートとローカルの内容が異なるポーズに配置されるという点が挙げられます。 ただし、レンダリング フレームの終了時には、リモートとローカルの両方のコンテンツを配置し、ディスプレイに表示にする必要があります。 次の図は、ローカルとリモートのポーズをディスプレイ ビューポートと比較して変換する例を示しています。

![リモートおよびローカルのポーズとターゲット ビューポートとの関係を示す図。](./media/reprojection-remote-local.png)

ARR には、前述の LSR モードに対して直交的に機能する 2 つの再プロジェクションのモードが用意されています。 これらのモードは、 **:::no-loc text="Remote pose mode":::** および **:::no-loc text="Local pose mode":::** と呼ばれます。 LSR モードとは異なり、これらのポーズ モードでは、リモート コンテンツとローカル コンテンツの結合方法が定義されます。 このモードを選択すると、実行時のパフォーマンスに関してローカル コンテンツの視覚品質が低下するため、アプリケーションでどちらのオプションが適切か慎重に検討する必要があります。 以下の考慮事項を参照してください。

### :::no-loc text="Remote pose mode":::

:::no-loc text="Remote pose mode"::: は ARR における既定のモードです。 このモードでは、ローカル コンテンツは、リモート フレームからのリモート ポーズを使用して、受信するリモート イメージ ストリーム上にレンダリングされます。 次に、最終的な再プロジェクションのために、結合された結果が OS に転送されます。 この方法では 1 つの再プロジェクションのみが使用されますが、最終的な修正はラウンドトリップ間隔に基づいて行われるため、完全な再プロジェクション エラーがローカル コンテンツにも適用されます。 その結果、大きな修正デルタによって、UI 要素を含むローカル ジオメトリの大きなゆがみが発生する可能性があります。

上の図を使用すると、次の変換が :::no-loc text="Remote pose mode"::: に適用されます。

![リモート ポーズ モードの手順を再プロジェクションします。](./media/reprojection-pose-mode-remote.png)

### :::no-loc text="Local pose mode":::

このモードでは、再プロジェクションは 2 つの異なる手順に分割されます。最初の手順では、リモート コンテンツはローカル ポーズの場所に再投影されます。つまり、既定では、ローカル コンテンツが VR/AR デバイスでレンダリングされる領域です。 その後、通常のローカル ポーズを使用して、この事前変換されたイメージの上にローカル コンテンツがレンダリングされます。 2 番目の手順で、最終的な再プロジェクションのために、結合された結果が OS に転送されます。 この 2 回目の再プロジェクションでは、小規模なデルタ (実際には ARR が存在しなかった場合に使用されるのと同じデルタ) しか発生しないため、ローカル コンテンツでの歪みアーティファクトは大幅に軽減されます。

その結果、次の図のようになります。

![ローカル ポーズ モードでの再プロジェクションのステップ。](./media/reprojection-pose-mode-local.png)

### <a name="performance-and-quality-considerations"></a>パフォーマンスと品質に関する考慮事項

ポーズ モードの選択は、視覚品質とパフォーマンスに影響します。 HoloLens 2 デバイスの:::no-loc text="Local pose mode":::で追加の再プロジェクションを実行するために、クライアント側にかかる追加のランタイム コストは、1 フレームあたりの GPU 時間にして約 1 ミリ秒程度です。 クライアント アプリケーションのフレーム予算が既に 16 ミリ秒に近い場合は、この追加コストを考慮に入れる必要があります。 一方、ローカル コンテンツがない、または歪みアーティファクトを発生させない程度のローカル コンテンツしかないようなアプリケーションもあります。 このような場合、リモート コンテンツの再プロジェクションの品質に影響がないため、:::no-loc text="Local pose mode":::では視覚効果は一切得られません。

このため、一般的なアドバイスとして、ユース ケースごとにモードをテストすることで、ビジュアル品質で得られるメリットが、余分なパフォーマンス上のオーバーヘッドに見合うかどうかを確認することをお勧めします。 また、重要な UI が表示されている場合にのみローカル モードを有効にするなど、モードを動的に切り替えることもできます。

### <a name="how-to-change-the-no-loc-textpose-mode-at-runtime"></a>実行時に:::no-loc text="Pose mode":::を変更する方法

次のクライアント API を使用して、実行時にモードを変更できます。

```cs
RenderingSession session = ...;
session.GraphicsBinding.SetPoseMode(PoseMode.Local); // set local pose mode
```
 
```cpp
ApiHandle<RenderingSession> session = ...;
session->GetGraphicsBinding()->SetPoseMode(PoseMode::Local); // set local pose mode
```

モードは、グラフィックス バインド オブジェクトが使用可能な場合に、いつでも変更できます。

## <a name="next-steps"></a>次のステップ

* [サーバー側のパフォーマンス クエリ](performance-queries.md)