---
title: Azure Cosmos DB の 禁止例外をトラブルシューティングする
description: 禁止例外を診断して修正する方法について説明します。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: dcef0eccc6ca314c51b436025fb10c25dbbe04fa
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658236"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Azure Cosmos DB の 禁止例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 状態コード 403 は、要求の完了が禁止されていることを表します。

## <a name="firewall-blocking-requests"></a>要求をブロックするファイアウォール
このシナリオでは、次のようなエラーが表示されるのが一般的です。

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>解決策
現在の[ファイアウォール設定](../how-to-configure-firewall.md)が正しいことと、接続しようとしている IP またはネットワークが含まれていることを確認します。
最近更新した場合は、変更が **適用されるまでに最大 15 分** かかる可能性があることに注意してください。

## <a name="partition-key-exceeding-storage"></a>ストレージを超過するパーティション キー
このシナリオでは、次のようなエラーが表示されるのが一般的です。

```
Response status code does not indicate success: Forbidden (403); Substatus: 1014
```

```
Partition key reached maximum size of {...} GB
```

### <a name="solution"></a>解決策
このエラーは、現在の[パーティション デザイン](../partitioning-overview.md#logical-partitions)とワークロードでは、所与のパーティション キー値に対して、許可されるデータ量以上の格納が試行されることを意味します。 コンテナーの論理パーティションの数に上限はありませんが、各論理パーティションで格納できるデータのサイズには制限があります。 ご不明な点がございましたら、サポートにお問い合わせください。

## <a name="non-data-operations-are-not-allowed"></a>非データ操作が許可されません
このシナリオは、非データ[操作がアカウントで禁止されている](../how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations)場合に起こります。 このシナリオでは、次のようなエラーが表示されるのが一般的です。

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>解決策
Azure Resource Manager、Azure portal、Azure CLI、または Azure PowerShell から操作を実行します。 または、非データ操作の実行を再度許可してください。

## <a name="next-steps"></a>次のステップ
* [IP ファイアウォール](../how-to-configure-firewall.md)を構成します。
* [仮想ネットワーク](../how-to-configure-vnet-service-endpoint.md)からのアクセスを構成します。
* [プライベート エンドポイント](../how-to-configure-private-endpoints.md)からのアクセスを構成します。
