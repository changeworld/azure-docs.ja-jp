---
title: Azure Cosmos DB のサービス要求タイムアウト例外をトラブルシューティングする
description: Azure Cosmos DB のサービス要求タイムアウト例外を診断して修正する方法
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293855"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Azure Cosmos DB の要求タイムアウトの問題を診断してトラブルシューティングする
Azure Cosmos DB で HTTP 408 要求タイムアウトが返された

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、要求タイムアウト例外の既知の原因と解決方法が含まれています。

### <a name="1-check-the-sla"></a>1.SLA を確認する
お客様は [Azure Cosmos DB の監視](monitor-cosmos-db.md)を確認して、番号 408 の例外が Cosmos DB の SLA に違反しているかどうかを確認する必要があります。

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>解決策 1: Cosmos DB の SLA に違反していなかった
このような一時的なエラーが発生した場合、このシナリオはアプリケーションによって処理され、再試行されます。

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>解決策 2: Cosmos DB の SLA に違反していた
Azure サポートにお問い合わせください: https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2.ホット パーティション キー
Azure Cosmos DB は、プロビジョニングされたスループット全体を物理パーティション間で均等に分散します。 ホット パーティションが存在すると、ある物理パーティション上の 1 つ以上の論理パーティション キーによってすべての物理パーティションの RU/秒が消費され、その一方で他の物理パーティション上の RU/秒は未使用のままになります。 症状としては、消費済み RU/秒の合計は、データベースまたはコンテナー上にプロビジョニングされている全体的な RU/秒よりも少なくなりますが、ホット論理パーティション キーに対する要求では、帯域幅調整 (429) が見られます。 [正規化された RU 消費量メトリック](monitor-normalized-request-units.md) を使用して、ワークロードでホット パーティションが発生しているかどうかを確認します。 

#### <a name="solution"></a>解決方法:
要求のボリュームと記憶域を均等に分散する適切なパーティション キーを選択します。 [パーティション キーの変更](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)方法を参照してください。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK の使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)
* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET V2](performance-tips.md) のパフォーマンス ガイドラインについて学習する