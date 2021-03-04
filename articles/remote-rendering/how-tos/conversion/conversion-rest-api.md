---
title: 資産変換 REST API
description: REST API を介して資産を変換する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: f33e5717cd5556e72d996e7e943867c16805e71b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705179"
---
# <a name="use-the-model-conversion-rest-api"></a>モデル変換 REST API を使用する

[モデル変換](model-conversion.md)サービスでは、[REST API](https://en.wikipedia.org/wiki/Representational_state_transfer) を使用して制御を行います。 この API を使用すると、変換の作成、変換プロパティの取得、既存の変換の一覧表示を行うことができます。

## <a name="rest-api-reference"></a>REST API リファレンス

Remote Rendering REST API のリファレンス ドキュメントは[こちら](/rest/api/mixedreality/2021-01-01preview/remoterendering)で参照できます。Swagger の定義は[こちら](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)で参照できます。

*Scripts* フォルダー内の [ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)に、サービスの使用方法を示す *Conversion.ps1* という名前の PowerShell スクリプトが提供されています。 スクリプトとその構成については、次を参照してください: [PowerShell スクリプトの例](../../samples/powershell-example-scripts.md)。 [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering)、Java、Python 用の SDK も用意されています。

## <a name="next-steps"></a>次のステップ

- [モデル変換に Azure Blob Storage を使用する](blob-storage.md)
- [モデル変換](model-conversion.md)