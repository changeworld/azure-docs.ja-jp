---
title: Azure Cosmos DB トリガーの接続ポリシー
description: Azure Cosmos DB トリガーで使用される接続ポリシーを構成する方法について説明します
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/05/2019
ms.author: maquaran
ms.openlocfilehash: 584d59884b70d2ee8243216e6f907fc9ec2d8ad4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755460"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-cosmos-db-trigger"></a>Azure Cosmos DB トリガーで使用される接続ポリシーを構成する方法

この記事では、Azure Cosmos DB トリガーを使用して Azure Cosmos アカウントに接続するときの接続ポリシーの構成方法について説明します。

## <a name="why-is-the-connection-policy-important"></a>接続ポリシーが重要な理由

接続モードには、直接モードとゲートウェイ モードの 2 つがあります。 これらの接続モードの詳細については、[パフォーマンスのヒント](./performance-tips.md#networking)に関する記事を参照してください。 既定では、Azure Cosmos DB トリガーにおけるすべての接続の確立に、**ゲートウェイ**が使用されます。 ただしこれは、パフォーマンス重視のシナリオでは最適な選択肢ではない可能性があります。

## <a name="changing-the-connection-mode-and-protocol"></a>接続のモードとプロトコルの変更

クライアントの接続ポリシーを構成するために使用できる 2 つの主要な構成設定として、**接続モード**と**接続プロトコル**があります。 Azure Cosmos DB トリガーとすべての [Azure Cosmos DB バインディング](../azure-functions/functions-bindings-cosmosdb-v2.md#output)で使用される既定の接続モードおよびプロトコルは、変更することができます。 既定の設定を変更するには、Azure Functions プロジェクトまたは Azure Functions アプリ内で `host.json` ファイルを見つけて、次の[追加設定](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings)を行います。

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

`connectionMode` には目的の接続モード (Direct または Gateway)、`protocol` には目的の接続プロトコル (Tcp または Https) が設定されている必要があります。 

自分の Azure Functions プロジェクトで Azure Functions V1 ランタイムを使用している場合、構成の名前はわずかに異なり、`cosmosDB` ではなく `documentDB` を使用する必要があります。

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Azure Functions の従量課金プランをホスティング プランとして使用している場合、各インスタンスで維持できるソケット接続量には制限があります。 直接および TCP モードを使用している場合は、設計上、より多くの接続が作成されて[従量課金プランの制限](../azure-functions/manage-connections.md#connection-limit)に達する可能性があります。この場合、ゲートウェイ モードを使用するか、または [App Service モード](../azure-functions/functions-scale.md#app-service-plan)で Azure 関数を実行することができます。

## <a name="next-steps"></a>次の手順

* [Azure Functions での接続の制限](../azure-functions/manage-connections.md#connection-limit)
* [Azure Cosmos DB のパフォーマンスに関するヒント](./performance-tips.md)
* [コード サンプル](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
