---
title: オブジェクトの境界
description: 空間オブジェクトの境界を照会する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679361"
---
# <a name="object-bounds"></a>オブジェクトの境界

オブジェクトの境界は、[エンティティ](entities.md)とその子が占めるボリュームを表します。 Azure Remote Rendering では、オブジェクトの境界は常に*軸平行境界ボックス* (AABB) として与えられます。 オブジェクトの境界は、*ローカル空間*または*ワールド空間*内に指定できます。 どちらの場合でも、これらは常に軸と平行です。つまり、エクステントとボリュームは、ローカルとワールドの空間表現で異なる場合があります。

## <a name="querying-object-bounds"></a>オブジェクトの境界の照会

[メッシュ](meshes.md)のローカル AABB は、メッシュ リソースから直接照会できます。 これらの境界は、エンティティの変換を使用して、エンティティのローカル空間またはワールド空間に変換できます。

このようにオブジェクト階層全体の境界を計算することもできますが、その場合は階層を走査し、各メッシュの境界を照会し、それらを手動で結合する必要があります。 この操作は、煩雑で非効率的です。

より良い方法は、エンティティで `QueryLocalBoundsAsync` または `QueryWorldBoundsAsync` を呼び出すことです。 その後、計算はサーバーにオフロードされ、最小限の遅延で返されます。

``` cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

## <a name="next-steps"></a>次のステップ

* [空間クエリ](../overview/features/spatial-queries.md)
