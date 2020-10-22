---
title: Azure Cosmos DB サービス利用不可の例外をトラブルシューティングする
description: Azure Cosmos DB サービス利用不可の例外を診断して修正する方法について説明します。
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b40787e1b7c40e5c238b2e400f6b449ad8963dd1
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277128"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Azure Cosmos DB サービス利用不可の例外を診断してトラブルシューティングする
SDK は、Azure Cosmos DB に接続することができませんでした。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、サービス利用不可の例外の既知の原因と解決策が含まれています。

### <a name="the-required-ports-are-being-blocked"></a>必要なポートがブロックされている
[必要なすべてのポート](sql-sdk-connection-modes.md#service-port-ranges)が有効になっていることを確認します。

### <a name="client-side-transient-connectivity-issues"></a>クライアント側の一時的な接続に関する問題
サービス利用不可の例外は、タイムアウトの原因となっている一時的な接続の問題がある場合に発生する可能性があります。 一般的に、このシナリオに関連するスタック トレースには、`TransportException` エラーが含まれます。 次に例を示します。

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

[要求のタイムアウトのトラブルシューティングの手順](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps)に関するページに従って、問題を解決します。

### <a name="service-outage"></a>サービス停止
[[Azure の状態]](https://status.azure.com/status) を確認して、進行中の問題があるかどうかを確認します。


## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。