---
title: 難しい物体を検出する方法
description: 難しい物体を検出するために構成できるメカニズムについて説明します。
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 05/28/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: f7ebff2a9fb45831842b9390e3e72cdbfe58e794
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987888"
---
# <a name="how-to-detect-a-difficult-object"></a>難しい物体を検出する方法

物体の検出が難しいことがわかる場合があります。 次に例を示します。

- 物体が壁際にあるため、広い表面積にアクセスできない場合
- 物体が大きすぎて、一周するのに時間がかかりすぎる場合
- デバイス センサーによって物体の表面が検出されない場合

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
