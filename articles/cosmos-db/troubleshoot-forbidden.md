---
title: Azure Cosmos DB の 禁止例外をトラブルシューティングする
description: 禁止例外を診断して修正する方法について説明します。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: d1ea89443f1d4eaab359e23dfc201dc732ced75e
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108875"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Azure Cosmos DB の 禁止例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
現在の[ファイアウォール設定](how-to-configure-firewall.md)が正しいことと、接続しようとしている IP またはネットワークが含まれていることを確認します。
最近更新した場合は、変更が **適用されるまでに最大 15 分** かかる可能性があることに注意してください。

## <a name="non-data-operations-are-not-allowed"></a>非データ操作が許可されません
このシナリオは、非データ[操作がアカウントで禁止されている](how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations)場合に起こります。 このシナリオでは、次のようなエラーが表示されるのが一般的です。

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>解決策
Azure Resource Manager、Azure portal、Azure CLI、または Azure PowerShell から操作を実行します。 または、非データ操作の実行を再度許可してください。

## <a name="next-steps"></a>次のステップ
* [IP ファイアウォール](how-to-configure-firewall.md)を構成します。
* [仮想ネットワーク](how-to-configure-vnet-service-endpoint.md)からのアクセスを構成します。
* [プライベート エンドポイント](how-to-configure-private-endpoints.md)からのアクセスを構成します。
