---
title: Azure Cosmos DB の要求率が大きすぎる例外をトラブルシューティングする
description: 要求率が大きすぎる例外を診断して修正する方法
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293854"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Cosmos DB の要求が多すぎる問題を診断してトラブルシューティングする
"Request rate too large (要求率が大きすぎる)" またはエラー コード 429 は、要求が調整中であることを示しています。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、要求が多すぎる問題の原因と解決方法が含まれています。

### <a name="1-check-the-metrics"></a>1.メトリックを確認する
お客様は [Azure Cosmos DB の監視](monitor-cosmos-db.md)を確認して、番号 429 の例外があるかどうかを確認する必要があります。

## <a name="cause"></a>原因:
使用されたスループット (RU/秒) が、[プロビジョニングされたスループット](set-throughput.md)を超えました。 SDK は、指定された再試行ポリシーに基づいて要求を自動的に再試行します。 このエラーが頻繁に発生する場合、コレクションでスループットを増やすことを検討してください。 ポータルのメトリックを調べて、429 エラーが発生しているかどうかを確認します。 パーティション キーを調べて、[ストレージおよび要求ボリュームが均等に分散されていること](partition-data.md)を確認します。

## <a name="solution"></a>解決方法:
1. [ポータルまたは SDK](set-throughput.md) を使用して、プロビジョニングされているスループットを大きくします。
2. データベースまたはコンテナーを[自動スケーリング](provision-throughput-autoscale.md)に切り替えます。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK の使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)
* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET V2](performance-tips.md) のパフォーマンス ガイドラインについて学習する