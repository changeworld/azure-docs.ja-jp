---
title: メッシュ
description: Azure Remote Rendering のスコープ内のメッシュの定義
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679377"
---
# <a name="meshes"></a>メッシュ

## <a name="mesh-resource"></a>メッシュ リソース

メッシュは、変更できない[共有リソース](../concepts/lifetime.md)であり、[モデル変換](../how-tos/conversion/model-conversion.md)でのみ作成できます。 メッシュには、1 つまたは複数の*サブメッシュ*が含まれています。 各サブメッシュは、既定でレンダリングに使用する必要がある[素材](materials.md)を参照します。 3D 空間にメッシュを配置するには、[エンティティ](entities.md)に [MeshComponent](#meshcomponent) を追加します。

### <a name="mesh-resource-properties"></a>メッシュ リソースのプロパティ

`Mesh` クラスのプロパティは次のとおりです。

* **素材:** 素材の配列。 各素材は、異なるサブメッシュによって使用されます。 配列内の複数のエントリが、同じ[素材](materials.md)を参照している場合があります。 このデータは実行時に変更できません。

* **境界:** メッシュの頂点のローカル空間の軸平行境界ボックス (AABB)。

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent` クラスは、メッシュ リソースのインスタンスを配置するために使用されます。 各 MeshComponent は単一のメッシュを参照します。 これは、各サブメッシュのレンダリングに使用される素材をオーバーライドできます。

### <a name="meshcomponent-properties"></a>MeshComponent のプロパティ

* **メッシュ:** このコンポーネントによって使用されるメッシュ リソース。

* **素材:** メッシュ コンポーネント自体に指定されている素材の配列。 配列は、メッシュ リソースの*素材*配列と常に同じ長さになります。 メッシュの既定値からオーバーライドされない素材は、この配列で *null 値*に設定されます。

* **UsedMaterials:** 各サブメッシュで実際に使用される素材の配列。 null 以外の値の場合、*素材*配列のデータと同じになります。 それ以外の場合は、メッシュ インスタンス内の*素材*配列の値を格納します。

## <a name="next-steps"></a>次のステップ

* [素材](materials.md)
