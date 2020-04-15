---
title: 色素材
description: 色素材の種類について説明します。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679165"
---
# <a name="color-materials"></a>色素材

*色素材*は、Azure Remote Rendering でサポートされている[素材の種類](../../concepts/materials.md)の 1 つです。 これらは、いかなる照明も当てず、常に明るさが最大である[メッシュ](../../concepts/meshes.md)に使用されます。 車のダッシュボード、電球、静的な光源が既に組み込まれているデータ ([フォトグラメトリ](https://en.wikipedia.org/wiki/Photogrammetry)で取得したモデルなど) については、これが当てはまります。

色素材のシェーディング モデルは [PBR 素材](pbr-materials.md)と比べて単純であるため、より効率的にレンダリングできます。 また、さまざまな透明度モードもサポートしています。

## <a name="common-material-properties"></a>共通の素材のプロパティ

これらのプロパティは、すべての素材に共通です。

* **albedoColor:** この色には、*albedoMap* や*頂点カラー*など、その他の色が乗算されます。 素材に対して*透明度*が有効な場合、アルファ チャネルを使用して不透明度が調整されます。ここで、`1` は完全に不透明、`0` は完全に透明を意味します。 既定値は白です。

  > [!NOTE]
  > 色素材には環境が反映されないため、完全に透明な色の素材は非表示になります。 これは、[PBR 素材](pbr-materials.md)とは異なります。

* **albedoMap:** ピクセル単位の albedo 値の [2D テクスチャ](../../concepts/textures.md)。

* **alphaClipEnabled** と **alphaClipThreshold:** *alphaClipEnabled* が true の場合、albedo アルファ値が *alphaClipThreshold* よりも小さいすべてのピクセルは描画されません。 アルファ クリッピングは、透明度を有効にしなくても使用でき、レンダリングがはるかに高速になります。 ただし、アルファ クリッピングされた素材は、完全に不透明な素材と比べると、レンダリングは遅くなります。 既定では、アルファ クリッピングは無効になっています。

* **textureCoordinateScale** と **textureCoordinateOffset:** スケールが UV テクスチャ座標に乗算され、オフセットが追加されます。 テクスチャの伸縮とシフトに使用できます。 既定のスケールは (1, 1) で、オフセットは (0, 0) です。

* **useVertexColor:** メッシュに頂点カラーが含まれていて、このオプションが有効になっている場合は、メッシュの頂点カラーが *albedoColor* と *Albedocolor* に乗算されます。 既定では、頂点カラーは無効になっています。

* **isDoubleSided:** 両面に対する表示が true に設定されている場合、この素材が表示されている三角形は、カメラが背面を見ている場合でもレンダリングされます。 既定では、このオプションは無効になっています。 [片面レンダリング](single-sided-rendering.md)に関する記事も参照してください。

## <a name="color-material-properties"></a>色素材のプロパティ

次のプロパティは、色素材に固有のものです。

* **vertexMix:** `0` と `1` の間のこの値は、[メッシュ](../../concepts/meshes.md)の頂点カラーが最終的な色にどれだけ強く影響するかを指定します。 既定値の 1 の場合、頂点カラーは albedo の色に完全に乗算されます。 値が 0 の場合、頂点カラーは完全に無視されます。

* **transparencyMode:** [PBR 素材](pbr-materials.md)とは対照的に、色素材は異なる透明度モードを区別します。

  1. **Opaque:** 既定のモードでは、透明度が無効になります。 この場合でもアルファ クリッピングは引き続き可能で、これが十分な場合は推奨されます。
  
  1. **AlphaBlended:** このモードは、PBR 素材の透明度モードに似ています。 ガラスなどの透明な素材の表示に使用されます。

  1. **Additive:** このモードは、最もシンプルかつ効率的な透明度モードです。 素材の影響は、レンダリングされるイメージに追加されます。 このモードを使用すると、重要なオブジェクトを強調表示するために使用されるマーカーなど、光彩 (ただし透明) のオブジェクトをシミュレートできます。

## <a name="next-steps"></a>次のステップ

* [PBR 素材](pbr-materials.md)
* [テクスチャ](../../concepts/textures.md)
* [メッシュ](../../concepts/meshes.md)
