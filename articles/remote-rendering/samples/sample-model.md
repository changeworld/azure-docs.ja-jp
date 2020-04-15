---
title: サンプル モデル
description: サンプル モデルのソースを示します。
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678493"
---
# <a name="sample-models"></a>サンプル モデル

この記事では、Azure Remote Rendering サービスのテストに使用できるサンプル データのいくつかのリソースを示します。

## <a name="built-in-sample-model"></a>組み込みサンプル モデル

URL **builtin://Engine** を使用していつでも読み込むことができる組み込みサンプル モデルが用意されています。

![サンプル モデル](./media/sample-model.png "サンプル モデル")

モデルの統計:

| 名前 | 値 |
|-----------|:-----------|
| [必要な VM サイズ](../how-tos/session-rest-api.md#create-a-session) | standard |
| 三角形の数 | 1870 万 |
| 可動パーツの数 | 2073 |
| 素材の数 | 94 |

## <a name="third-party-data"></a>サード パーティのデータ

Khronos Group は、テスト用の一連の glTF サンプル モデルを保持しています。 ARR では、テキスト ( *.gltf*) とバイナリ ( *.glb*) の両方の形式の glTF 形式がサポートされます。 最良の視覚的結果を得るためには、PBR モデルを使用することをお勧めします。

* [glTF サンプル モデル](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Unity によるモデルのレンダリング](../quickstarts/render-model.md)
* [クイック スタート: モデルをレンダリング用に変換する](../quickstarts/convert-model.md)
