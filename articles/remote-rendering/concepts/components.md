---
title: Components
description: Azure Remote Rendering のスコープ内のコンポーネントの定義
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679417"
---
# <a name="components"></a>Components

Azure Remote Rendering では、[エンティティ コンポーネント システム](https://en.wikipedia.org/wiki/Entity_component_system)のパターンが使用されます。 [エンティティ](entities.md)は、オブジェクトの位置と階層構造を表し、コンポーネントで動作が実装されます。

最も頻繁に使用されるコンポーネントの種類は、[メッシュ コンポーネント](meshes.md)であり、これはメッシュをレンダリング パイプラインに追加します。 同様に、[ライト コンポーネント](../overview/features/lights.md)を使用してライティングを追加し、[カット プレーン コンポーネント](../overview/features/cut-planes.md)を使用してオープン メッシュを切り取ります。

これらのコンポーネントはすべて、関連付けられているエンティティの変換 (位置、回転、スケール) を参照ポイントとして使用します。

## <a name="working-with-components"></a>コンポーネントの操作

プログラムを使用して、コンポーネントの追加、削除、および操作を簡単に行うことができます。

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

コンポーネントは、作成時にエンティティに関連付けられます。 後で別のエンティティに移動することはできません。 コンポーネントは `Component.Destroy()` を使用して明示的に削除されるか、またはコンポーネントの所有者エンティティが破棄されたときに自動的に削除されます。

各コンポーネントの種類のインスタンスは、一度に 1 つのみエンティティに追加できます。

## <a name="unity-specific"></a>Unity 固有

Unity 統合には、コンポーネントと対話するための拡張機能が追加されています。 「[Unity のゲーム オブジェクトとコンポーネント](../how-tos/unity/objects-components.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [オブジェクトの境界](object-bounds.md)
* [メッシュ](meshes.md)
