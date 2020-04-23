---
title: 制限事項
description: SDK 機能のコードに関する制限事項
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417693"
---
# <a name="limitations"></a>制限事項

多くの機能には、サイズや数などの制限があります。

## <a name="azure-frontend"></a>Azure フロントエンド

* プロセスあたりの AzureFrontend インスタンスの合計数: 16.
* AzureFrontend あたりの AzureSession インスタンスの合計数: 16.

## <a name="objects"></a>Objects

* オブジェクトの 1 つの型 (Entity、CutPlaneComponent など) の許容される合計数: 16,777,215。
* 許容されるアクティブな切断面の合計数: 8.

## <a name="materials"></a>素材

* 1 つの資産内で許容される素材の合計数: 65,535。

## <a name="overall-number-of-polygons"></a>多角形の全体数

読み込まれたすべてのモデルに対して許容される多角形の数は、[セッション管理 REST API](../how-tos/session-rest-api.md#create-a-session) に渡される VM のサイズによって異なります。

| VM サイズ | 多角形の最大数 |
|:--------|:------------------|
|standard| 2,000 万 |
|Premium| 制限なし |


## <a name="platform-limitations"></a>プラットフォームの制限事項

**Windows 10 デスクトップ**

* Unity からの "PC スタンドアロン" 展開はサポートされていません。 代わりに UWP を使用してください。
* サポートされている UWP プラットフォームは、UWP (x86) のみです。 UWP (x64) はサポートされていません。

**Hololens 2**

* [PV カメラからのレンダリング](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)機能はサポートされていません。
