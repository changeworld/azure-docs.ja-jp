---
title: VM サイズ
description: 割り当てることができる個別の VM サイズについて説明します
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: aab914caa2647146639aa366f558c80bebcfde54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809939"
---
# <a name="vm-sizes"></a>VM サイズ

レンダリング サービスは、`Standard` と `Premium` と呼ばれる Azure の 2 種類の異なるマシンで動作できます。

## <a name="polygon-limits"></a>多角形の制限

`Standard` VM サイズには、**2,000 万多角形**のハード制限があります。 `Premium` サイズにはそのような制限はありません。

Standard VM のレンダラーがこの制限に達すると、レンダリングの背景がチェッカーボードに切り替わります。

![チェッカーボード](media/checkerboard.png)

## <a name="allocate-the-vm"></a>VM を割り当てる

レンダリング セッションの初期化時に、望ましい VM の種類を指定する必要があります。 実行中のセッション内では変更できません。 次のコード例では、VM サイズを指定する必要がある場所を示します。

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

[PowerShell スクリプトの例](../samples/powershell-example-scripts.md)では、`arrconfig.json` ファイル内で VM のサイズを指定する必要があります。

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>レンダラーによる多角形の数の評価方法

制限テストで考慮される多角形の数は、実際にレンダラーに渡される多角形の数です。 このジオメトリは、通常、インスタンス化されたすべてのモデルの合計ですが、例外もあります。 次のジオメトリは**含まれません**。
* ビューの視錐台の完全に外側にある読み込まれたモデル インスタンス。
* [階層状態のオーバーライド コンポーネント](../overview/features/override-hierarchical-state.md)を使用して非表示に切り替えられたモデルまたはモデル パーツ。

したがって、すべての単一モデルに上限に近い数の多角形が含まれる複数のモデルが読み込まれる、`standard` サイズを対象とするアプリケーションを作成することができます。 アプリケーションで一度に 1 つのモデルのみが表示される場合、チェッカーボードはトリガーされません。

### <a name="how-to-determine-the-number-of-polygons"></a>多角形の数を確認する方法

`standard` サイズの VM の容量制限に含まれるモデルまたはシーンの多角形の数を確認するには、次の 2 つの方法があります。
* モデル変換側で、[変換出力 json ファイル](../how-tos/conversion/get-information.md)を取得し、[*inputStatistics* セクション](../how-tos/conversion/get-information.md#the-inputstatistics-section)の `numFaces` エントリを調べます
* アプリケーションで動的なコンテンツが処理されている場合、レンダリングされるポリゴンの数は実行時に動的に照会できます。 [パフォーマンス評価クエリ](../overview/features/performance-queries.md#performance-assessment-queries)を使用し、`FrameStatistics` 構造体の `polygonsRendered` メンバーを調べます。 この非同期クエリの後でユーザーが操作を実行できるようにするため、チェッカーボードの背景のフェードインでは常に遅延が発生します。 たとえば、ユーザーは、モデルのインスタンスを非表示にしたり削除したりすることができます。

## <a name="pricing"></a>価格

VM の種類ごとの価格の詳細については、「[Remote Rendering の価格](https://azure.microsoft.com/pricing/details/remote-rendering)」ページを参照してください。

## <a name="next-steps"></a>次のステップ
* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)
* [モデルの変換](../how-tos/conversion/model-conversion.md)

