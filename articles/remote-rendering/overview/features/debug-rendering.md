---
title: デバッグ レンダリング
description: サーバー側デバッグ レンダリング効果の概要
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868151"
---
# <a name="debug-rendering"></a>デバッグ レンダリング

デバッグ レンダリング API は、さまざまなデバッグ効果を使用してサーバー側のレンダリングを変更するための広範なグローバル オプションを提供します。

## <a name="available-debug-rendering-effects"></a>利用可能なデバッグ レンダリング効果

|設定                          | 結果                               |
|---------------------------------|:-------------------------------------|
|フレーム カウンター                    | テキスト オーバーレイをフレームの左上コーナーにレンダリングします。 テキストは、現在のサーバー側のフレーム ID を示し、レンダリングの進行に伴って継続的に増分されます。 |
|多角形の数                    | テキスト オーバーレイをフレームの左上コーナーにレンダリングします。 テキストは、現在レンダリングされている多角形の量、[サーバー側のパフォーマンス クエリ](performance-queries.md)で照会された値と同じ値を示します| 
|ワイヤーフレーム                        | これを有効にすると、サーバーに読み込まれたすべてのオブジェクト ジオメトリがワイヤーフレーム モードでレンダリングされます。 このモードでは、多角形の端だけがラスタライズされます。 |

次のコードを使用すると、デバッグ効果が有効になります。

``` cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

![デバッグ レンダリング](./media/debug-rendering.png)

> [!NOTE]
> デバッグ レンダリング効果はすべて、フレーム全体に影響を与えるグローバル設定です。

## <a name="use-cases"></a>ユース ケース

デバッグ レンダリング API は、サービス接続が実際に正常に稼働していることを確認するなど、単純なデバッグ タスクを対象としています。 テキスト レンダリング オプションは、ダウンストリーム配信されたビデオ フレームに直接影響します。 これを有効にすると、新しいフレームを受信し、適切にデコードされたかどうかを確認できます。

ただし、提供された効果では、サービスの正常性に関する詳細なイントロスペクションは提供されません。 このユースケースでは、[サーバー側のパフォーマンス クエリ](performance-queries.md)をお勧めします。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

* テキスト オーバーレイを有効にした場合、パフォーマンスのオーバーヘッドはほとんど発生しません。
* ワイヤーフレーム モードを有効にすると、かなりのパフォーマンスのオーバーヘッドが発生します。ただし、これはシーンによって異なる場合があります。 複雑なシーンでは、このモードによってフレーム レートが 60 Hz のターゲットを下回ることがあります。

## <a name="next-steps"></a>次のステップ

* [レンダリング モード](../../concepts/rendering-modes.md)
* [サーバー側のパフォーマンス クエリ](performance-queries.md)
