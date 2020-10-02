---
title: 複数リージョン環境での Azure Cosmos SDK の可用性の診断とトラブルシューティング
description: 複数リージョン環境で操作する場合の Azure Cosmos SDK の可用性の動作について、詳しく説明します。
author: ealsur
ms.service: cosmos-db
ms.date: 09/16/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0c717aca88095df05fc7927f3c3d6e2d481925d2
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708470"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>複数リージョン環境での Azure Cosmos SDK の可用性の診断とトラブルシューティング

この記事では、特定のリージョンに対する接続の問題が発生した場合、またはリージョンのフェールオーバーが発生した場合の最新バージョンの Azure Cosmos SDK の動作について説明します。

すべての Azure Cosmos SDK には、リージョンの優先設定をカスタマイズするオプションがあります。 次のプロパティは、さまざまな SDK で使用されます。

* .NET V2 SDK の [ConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) プロパティ。
* .NET V3 SDK の [CosmosClientOptions.ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) または [CosmosClientOptions.ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) プロパティ。
* Java V4 SDK の [CosmosClientBuilder.preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) メソッド。
* Python SDK の [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) パラメーター。

単一書き込みリージョン アカウントの場合、すべての書き込み操作は常にその書き込みリージョンに送られるため、優先リージョンの一覧は読み取り操作にのみ適用されます。 複数書き込みリージョン アカウントの場合、優先設定一覧は読み取りおよび書き込み操作に影響します。

優先リージョンを設定しない場合、リージョンの優先順位は [Azure Cosmos DB リージョン一覧の順序](distribute-data-globally.md)によって定義されます。

次のシナリオのいずれかが発生すると、Azure Cosmos SDK を使用するクライアントではログが公開され、**操作の診断情報**の一部として再試行情報が含まれます。

## <a name="removing-a-region-from-the-account"></a><a id="remove region"></a>アカウントからのリージョンの削除

Azure Cosmos アカウントからリージョンを削除すると、アカウントをアクティブに使用している SDK クライアントでは、バックエンドの応答コードを介してリージョンの削除が検出されます。 次に、クライアントによってリージョンのエンドポイントは使用不可とマークされます。 クライアントによって現在の操作が再試行され、以降のすべての操作は優先順に次のリージョンに永続的にルーティングされます。

## <a name="adding-a-region-to-an-account"></a>アカウントへのリージョンの追加

Azure Cosmos SDK クライアントでは、5 分ごとにアカウント構成が読み取られ、認識しているリージョンが更新されます。

リージョンを削除して後でアカウントに追加し直した場合、追加したリージョンの優先度が高くなると、SDK はそのリージョンを永続的に使用するように切り替わります。 追加されたリージョンが検出された後、それ以降のすべての要求はそのリージョンに送信されます。

Azure Cosmos アカウントに割り当てられていないリージョンに接続するようにクライアントを構成した場合、優先リージョンは無視されます。 後でそのリージョンを追加すると、クライアントはそれを検出し、そのリージョンに永続的に切り替わります。

## <a name="failover-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>単一書き込みリージョン アカウントで書き込みリージョンをフェールオーバーする

現在の書き込みリージョンのフェールオーバーを開始すると、次の書き込み要求は既知のバックエンド応答で失敗します。 この応答が検出されると、クライアントでは、アカウントに対してクエリが実行され、新しい書き込みリージョンが学習され、現在の操作が再試行され、以降のすべての書き込み操作は永続的に新しいリージョンにルーティングされます。

## <a name="regional-outage"></a>リージョンの停止

アカウントが単一書き込みリージョンであり、書き込み操作中にリージョンの停止が発生した場合、動作は[手動フェールオーバー](#manual-failover-single-region)と同様です。 読み取り要求または複数書き込みリージョン アカウントの場合、動作は[リージョンの削除](#remove region)と同様です。

## <a name="session-consistency-guarantees"></a>セッションの整合性の保証

[セッションの整合性](consistency-levels.md#guarantees-associated-with-consistency-levels)を使用する場合、クライアントでは、自身の書き込みを読み取ることができるように保証する必要があります。 読み取りリージョンの優先設定が書き込みリージョンと異なる単一書き込みリージョン アカウントでは、ユーザーが書き込みを発行し、ローカル リージョンから読み取りが実行されているときに、ローカル リージョンでまだデータのレプリケーションを受け取っていない場合があります (軽い制約の速度)。 このような場合、SDK によって読み取り操作に対する特定の障害が検出され、セッションの整合性を確保するためにハブ リージョンに対する読み取りが再試行されます。

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP プロトコルでの一時的な接続の問題

Azure Cosmos SDK クライアントが TCP プロトコルを使用するように構成されているシナリオでは、特定の要求に対して、ネットワークの状態が特定のエンドポイントとの通信に一時的に影響を与える場合があります。 このような一時的なネットワーク状態は、TCP タイムアウトとして表面化する可能性があります。 クライアントでは、同じエンドポイントに対して数秒間要求がローカルで再試行されます。

ユーザーが複数のリージョンを含む優先リージョン一覧を構成し、Azure Cosmos アカウントが複数書き込みリージョンまたは単一書き込みリージョンであり、操作が読み取り要求である場合、クライアントでは優先設定一覧の次のリージョンでその 1 つの操作が再試行されます。

## <a name="next-steps"></a>次の手順

* 最新の [.NET SDK](sql-api-sdk-dotnet-standard.md) を使用する
* 最新の [Java SDK](sql-api-sdk-java-v4.md) を使用する
* 最新の [Python SDK](sql-api-sdk-python.md) を使用する
* 最新の [Node SDK](sql-api-sdk-node.md) を使用する
