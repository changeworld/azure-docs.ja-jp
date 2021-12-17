---
title: オブジェクト検出のトラブルシューティング
description: Azure Object Anchors を使用したオブジェクトの検出に関する問題のトラブルシューティング。
author: craigktreasure
manager: rgarcia
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: b70babbf3f105903cc7b14d175908b972bde9158
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601997"
---
# <a name="troubleshooting-object-detection"></a>オブジェクト検出のトラブルシューティング

この記事では、既に 3D モデルを Azure Object Anchors 検出モデルに変換し、モデルをアプリケーションに正常に読み込んであるものとします。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

* 検出しているモデルが、最適なエクスペリエンスのためにサポートされるサイズ (1 - 10 m) であることを確認します。
* ポスターをいくつか追加して、部屋に十分なテクスチャがあるようにします。
* [後で](#remove-holograms-to-reset-the-map)説明するように、現在のホログラムを削除してマップをリセットします。
* オブジェクトをより完全にスキャンします。
* 検索領域として、オブジェクトの全体または大部分を含む狭い境界ボックスを指定します。
* 空間マッピングのキャッシュをクリアし、オブジェクトを再スキャンします。
* [後で](#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct)説明するように、モデルの変換中に正しい重力の方向と資産の寸法が使用されたことを確認します。
* [後で](#visually-inspect-the-detection-models-mesh)説明するように、検出モデルを視覚的に調べます。
* [後で](#adjust-object-query-values)説明するように、モデルのクエリ値を調整します。
* [後で](#capture-diagnostics)説明するように、診断をキャプチャします。

### <a name="remove-holograms-to-reset-the-map"></a>ホログラムを削除してマップをリセットする

オブジェクトの検出で次のいずれかの問題が発生している場合は、マップを削除してリセットすると、問題が解決することがあります。
* 向きの反転
* 正しくないポーズ
* 傾斜したモデル

ホログラムを削除してマップをリセットするには、**設定** アプリを開き、 **[システム]**  ->  **[ホログラム]** に移動します。 次に、 **[Remove all holograms]\(すべてのホログラムを削除\)** を選択して新しいマップから開始します。

ホログラムをクリアすると、オブジェクトが最近移動された場合は、現在の位置で適切に検出されるようになります。

HoloLens を装着して環境内を歩き回ることで、環境を再スキャンします。 目的のオブジェクトの周りを歩いて、1 - 2 メートルの距離から数回検出します。

### <a name="ensure-the-gravity-direction-and-asset-dimension-unit-are-correct"></a>重力の方向と資産の寸法単位が正しいことを確認する

Object Anchors Conversion SDK を使用して変換のために 3D モデルを送信するときは ([こちら](../quickstarts/get-started-model-conversion.md)を参照)、3D モデルの正しい重力方向 (`Gravity`) と測定単位 (`AssetDimensionUnit`) を入力する必要があります。 これらの値が正しくない場合、Object Anchors でオブジェクトが正しく検出されない可能性があります。

重力の方向は、地球に向かう下方向のベクトルです。 CAD モデルの場合、重力方向は通常、上方向の逆になります。 たとえば、多くの場合 +Z が上方向を表しますが、この場合は -Z または `Vector3(0.0, 0.0, -1.0)` が重力方向を表します。 重力を決定するときは、実行時にモデルが見られる向きも考慮する必要があります。 現実世界の平らな場所に置かれた椅子を検出しようとしている場合、重力は `Vector3(0.0, 0.0, -1.0)` かもしれません。 しかし、椅子が 45 度の斜面に置かれている場合、重力は `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` である可能性があります。

重力の方向は、[MeshLab](http://www.meshlab.net/) などの 3D レンダリング ツールを使用して決定できます。

測定の単位は、モデルのスケールを表します。 サポートされている単位は、**Microsoft.Azure.ObjectAnchors.Conversion.AssetLengthUnit** 列挙型を使用して確認できます。

また、[こちら](../visualize-converted-model.md)の手順に従って Unity で検出モデルを視覚化し、重力の方向とスケールの外観が適切かどうかを視覚的に確認することもできます。

### <a name="visually-inspect-the-detection-models-mesh"></a>検出モデルのメッシュを視覚的に検査する

場合によっては、向き、スケール、または特徴の問題を確認できるように、検出モデルのメッシュを視覚的に検査すると役に立つことがあります。 [こちら](../visualize-converted-model.md)の手順に従って、Unity で変換されたモデルを視覚化します。

### <a name="adjust-object-query-values"></a>オブジェクトのクエリ値を調整する

* 検出の速度と精度を向上させるために、理想的にはオブジェクト全体をカバーする、狭い検索領域を指定します。
* 通常は、既定の `ObjectQuery.MinSurfaceCoverage` 値で十分ですが、より小さな値を使用して、検出時間を短くすることができます。
* オブジェクトが直立していると予想される場合は、`ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` に小さい値を使用します。
* アプリでは、常に検出用に `1:1` のオブジェクト モデルを使用する必要があります。 推定されたスケールは、理想的には誤差 1% 以内で 1 に近い必要があります。 アプリでは、`ObjectQuery.MaxScaleChange` を `0` または `0.1` に設定してスケールの推定を無効または有効にし、インスタンスの姿勢を定性的に評価することができます。
* 詳細については、「[難しいオブジェクトを検出する方法](../detect-difficult-object.md)」を参照してください。

### <a name="capture-diagnostics"></a>診断をキャプチャする

アプリケーションでは、[ObjectDiagnosticsSession](../concepts/sdk-overview.md#objectdiagnosticssession) オブジェクトを使用して、診断アーカイブをキャプチャし、保存することができます。

[MRTK を使用した Unity サンプル アプリ](../quickstarts/get-started-unity-hololens-mrtk.md)では、**TempState** フォルダーに診断が書き込まれます。 診断セッションを始めるには、<a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">ハンド メニュー</a>を開き、 **[Start Tracing]\(トレースの開始\)** を選択し、検出の試行を再現してから、 **[Stop Tracing]\(トレースの停止\)** を選択して診断アーカイブを保存します。 その後、[Windows デバイス ポータル](/windows/mixed-reality/develop/platform-capabilities-and-apis/using-the-windows-device-portal)を使用して、アプリの **TempState** フォルダーから診断アーカイブを取得できます。

診断アーカイブを Microsoft と共有して、問題のデバッグに役立てることができます。

## <a name="next-steps"></a>次のステップ

このトラブルシューティング ガイドでは、Azure Object Anchors を使用した物理オブジェクトの検出のトラブルシューティング方法について説明しました。
関連記事を次に示します。

> [!div class="nextstepaction"]
> [難しい物体を検出する方法](../detect-difficult-object.md)

> [!div class="nextstepaction"]
> [Object Anchors モデルを視覚化する方法](../visualize-converted-model.md)
