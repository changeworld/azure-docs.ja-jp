---
title: 制限事項
description: SDK 機能のコードに関する制限事項
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 06e266460e12218f531c85c2c6ca48c1e9658053
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003025"
---
# <a name="limitations"></a>制限事項

多くの機能には、サイズや数などの制限があります。

## <a name="azure-frontend"></a>Azure フロントエンド

フロントエンド API (C++ および C#) には次の制限事項が適用されます。
* プロセスあたりの [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) インスタンスの合計数:16。
* [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) あたりの [RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession) インスタンスの合計数:16。

## <a name="objects"></a>オブジェクト

* オブジェクトの 1 つの型 ([Entity](../concepts/entities.md)、[CutPlaneComponent](../overview/features/cut-planes.md) など) の許容される合計数: 16,777,215。
* 許容されるアクティブな切断面の合計数: 8.

## <a name="geometry"></a>ジオメトリ

* **アニメーション:** アニメーションは、[ゲーム オブジェクト](../concepts/entities.md)の個々の変換をアニメーション化することに限定されます。 スキニングや頂点のアニメーションを使用したスケルトン アニメーションはサポートされていません。 ソース資産ファイルからのアニメーション トラックは保持されません。 代わりに、クライアント コードからオブジェクト変換アニメーションを駆動する必要があります。
* **カスタム シェーダー:** カスタム シェーダーの作成はサポートされていません。 使用できるのは、組み込みの[色素材](../overview/features/color-materials.md)または [PBR 素材](../overview/features/pbr-materials.md)だけです。
* 1 つの資産内の **個別の素材の最大数**:65,535。 素材数の自動的な削減の詳細については、「[素材の重複除去](../how-tos/conversion/configure-model-conversion.md#material-de-duplication)」の章をご覧ください。
* **1 つのテクスチャの最大寸法**:16,384 x 16,384。 レンダラーでは、これより大きいテクスチャは使用できません。 変換プロセスにより、大きいテクスチャのサイズを縮小できる場合がありますが、一般に、この制限よりも大きいテクスチャは処理できません。

### <a name="overall-number-of-polygons"></a>多角形の全体数

読み込まれたすべてのモデルに対して許容される多角形の数は、[セッション管理 REST API](../how-tos/session-rest-api.md) に渡される VM のサイズによって異なります。

| サーバーのサイズ | 多角形の最大数 |
|:--------|:------------------|
|standard| 2,000 万 |
|Premium| 制限なし |

この制限の詳細については、「[サーバーのサイズ](../reference/vm-sizes.md)」の章をご覧ください。

## <a name="platform-limitations"></a>プラットフォームの制限事項

**Windows 10 デスクトップ**

* Win32/x64 は、サポートされている唯一の Win32 プラットフォームです。 Win32/x86 はサポートされていません。

**HoloLens 2**

* [PV カメラからのレンダリング](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)機能はサポートされていません。