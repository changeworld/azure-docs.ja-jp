---
title: 難しい物体を検出する方法
description: 難しい物体を検出するために構成できるメカニズムについて説明します。
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 09/08/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: cbdf29aa7bb9c342e08b64af0745c689910b26bd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791975"
---
# <a name="how-to-detect-a-difficult-object"></a>難しい物体を検出する方法

物体の検出が難しいことがわかる場合があります。 次に例を示します。

- 物体が壁際にあるため、広い表面積にアクセスできない場合
- 物体が大きすぎて、一周するのに時間がかかりすぎる場合
- デバイス センサーによって物体の表面が検出されない場合

## <a name="adjusting-object-query-values"></a>物体クエリ値の調整

Azure Object Anchors SDK には、このような状況で役立つメカニズムが用意されています。

- `ObjectQuery.MinSurfaceCoverage` プロパティ。 これは、オブジェクト インスタンスを真陽性と見なすために最低限必要なサーフェス カバレッジ比率を表します。 指定可能な値は 0 から 1.0 です (0 から 100% を表します)。 既定の設定は物体によって異なります (表面積が大きいほど、最低限必要なカバレッジは小さくなります)。 そのままでほとんどの状況に対応できます。 ただし、検出が難しい物体の場合は、このプロパティの値を小さくすることをお勧めします。これにより、物体を検出するために必要なサーフェス カバレッジが小さくなります。

- `ObjectQuery.MaxScaleChange` プロパティ。 元のモデルが、検出対象の物体に対して `1:1` のスケールでない場合は、この設定を調整できます。 指定可能な値は 0 から 1.0 です (0 から 100% を表します)。 既定の設定の 0 を使用すると、スケールの推定が無効になります。スケールの推定には、`1:1` のスケール マッピングが必要です。 たとえば、このプロパティを 10% に設定すると、スケールの推定が有効になり、モデルのスケールが物体と `1:1` で一致しない場合にある程度の柔軟性が得られます。

- `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` プロパティ。 これは、オブジェクトの上方向と重力の間の最大角度 (度単位) を表します。 範囲は 0 から 180 です。 つまり、元のモデルに対する物体の傾きを表します。 物体の傾きが元のモデルと一致しない場合は、既定の設定 (3 度) を上げることで柔軟性を高めることができます。

- `ObjectQuery.IsExpectedToBeStandingOnGroundPlane` プロパティ。 これは、物体が地面に立っていると予想されるかどうかを表すブール値です。 この既定値は false です。 これを true に切り替えることで、物体が地上にある場合の検出を高速化できます。

- `ObjectQuery.SearchAreas` プロパティ。 これは、物体を検索する領域の集まりを表します。 物体の全体または大部分をカバーしながら、狭い検索領域を指定することで、検出の速度と精度が向上します。 次のいずれかを選択できます。

  - 方向付けられた境界ボックス (`ObjectSearchArea.FromOriented` を使用)。
  - 視野 (`ObjectSearchArea.FromFieldOfView` を使用)。
  - 場所 (`ObjectSearchArea.FromLocation` を使用)。
  - 球体 (`ObjectSearchArea.FromSphere` を使用)。

詳細については、[Unity](/dotnet/api/microsoft.azure.objectanchors.objectquery) または [HoloLens C++/WinRT](/cpp/api/object-anchors/winrt/objectquery) の `ObjectQuery` クラスを参照してください。

## <a name="next-steps"></a>次のステップ

このトラブルシューティング ガイドでは、検出が難しい物体の検出をトラブルシューティングする方法を学習しました。
関連記事を次に示します。

> [!div class="nextstepaction"]
> [オブジェクト検出のトラブルシューティング](./troubleshoot/object-detection.md)
