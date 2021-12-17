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
ms.openlocfilehash: 9a5f1590e73388429f613a1c6b078f5c315397d6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238946"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Azure Cosmos DB の 禁止例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 状態コード 403 は、要求の完了が禁止されていることを表します。

## <a name="firewall-blocking-requests"></a>要求をブロックするファイアウォール

データ プレーン要求は、次の 3 つのパスを使用して Cosmos DB に送信できます。

- パブリック インターネット (IPv4)
- サービス エンドポイント
- プライベート エンドポイント

データ プレーン要求が 403 Forbidden でブロックされた場合、エラー メッセージに、要求が上記の 3 つのパスのどれを経由して Cosmos DB に送信されたかが示されます。

- `Request originated from client IP {...} through public internet.`
- `Request originated from client VNET through service endpoint.`
- `Request originated from client VNET through private endpoint.`

### <a name="solution"></a>ソリューション

Cosmos DB に送信される要求がどのパスを経由すると **予期** されるかを理解します。
   - 予期されるパスを介して要求が Cosmos DB に送信されなかったことを示すエラー メッセージが表示された場合は、クライアント側の設定に問題があると考えられます。 次のドキュメントに従って、クライアント側の設定を再確認してください。
      - パブリック インターネット: [Azure Cosmos DB で IP ファイアウォールを構成します](../how-to-configure-firewall.md)。
      - サービス エンドポイント: [仮想ネットワーク (VNet) から Azure Cosmos DB へのアクセスを構成します](../how-to-configure-vnet-service-endpoint.md)。 たとえば、要求がサービス エンドポイントを使用することを予期しますが、要求はパブリック インターネット経由で Cosmos DB に送信された場合、クライアントが実行していたサブネットは、Cosmos DB に対してサービス エンドポイントを有効にしていない可能性があります。
      - ライベート エンドポイント: [Azure Cosmos アカウントの Azure Private Link を構成する](../how-to-configure-private-endpoints.md)。 たとえば、要求がプライベート エンドポイントを使用することを予期しますが、要求はパブリック インターネット経由で Cosmos DB に送信された場合、VM 上の DNS が、プライベート IP にアカウント エンドポイントを解決するように構成されていない可能性があります。そのため、要求は代わりにアカウントのパブリック IP を経由しました。
   - 要求が予期されるパスを経由して Cosmos DB に送信された場合、ソース ネットワーク ID がアカウントに対して許可するように構成されていないため、要求はブロックされました。 要求が Cosmos DB に送信されたパスに応じて、アカウントの設定を確認します。
      - パブリック インターネット: アカウントの[パブリック ネットワーク アクセス](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation)と IP 範囲フィルターの構成を確認します。
      - サービス エンドポイント: アカウントの[パブリック ネットワーク アクセス](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation)と VNET フィルター構成を確認します。
      - プライベート エンドポイント: アカウントのプライベート エンドポイント構成とクライアントのプライベート DNS 構成を確認します。 これは、別のアカウントに対して設定されているプライベート エンドポイントからアカウントにアクセスした場合に発生する可能性があります。

最近アカウントのファイアウォール構成を更新した場合は、変更が **適用されるまでに最大 15 分** かかる可能性があることに注意してください。

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
