---
title: Azure Cosmos DB のリージョン内フェールオーバー | Microsoft Docs
description: Azure Cosmos DB での手動および自動フェールオーバーの動作について説明します。
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: govindk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 697be3a1eb07b2f2650f3dd94fd835b9431aec6b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038581"
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Azure Cosmos DB でのビジネス継続性のためのリージョン内自動フェールオーバー
Azure Cosmos DB はデータのグローバル分散を容易にします。そのために、対応する保証と共に一貫性、可用性、パフォーマンスの間の明確なトレードオフを提供する、フル マネージドの[複数リージョンのデータベース アカウント](distribute-data-globally.md)が用意されています。 Cosmos DB アカウントには、高可用性、10 ミリ秒未満の遅延、[明確に定義された整合性レベル](consistency-levels.md)、マルチホーム API による透過的なリージョン内フェールオーバー、世界規模でスループットとストレージを柔軟にスケーリングする機能が備わっています。 

Cosmos DB では、明示的なフェールオーバーおよびポリシーに基づくフェールオーバーの両方がサポートされており、障害発生の際にエンド ツー エンド システムの動作を制御することができます。 この記事では、次のことについて説明します。

* 手動フェールオーバーが Cosmos DB で動作する仕組み
* 自動フェールオーバーが Cosmos DB で動作する仕組みと、データセンターがダウンした場合に発生すること
* 手動フェールオーバーをアプリケーション アーキテクチャで使用する方法

Azure Cosmos DB プログラム マネージャー、Andrew Liu が作成したこの動画では、リージョンのフェールオーバーについても学習できます。リージョンのフェールオーバーを含む、グローバル分散機能について紹介しています。

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>複数リージョンのアプリケーションの構成
フェールオーバー モードを詳しく説明する前に、複数リージョンの可用性を活用し、リージョン内フェールオーバーの際も耐障害性を持つようなアプリケーションを構成する方法について説明します。

* 最初に、複数のリージョンでアプリケーションをデプロイします。
* アプリケーションがデプロイされているすべてのリージョンからのアクセスを確実に低待機時間にするために、サポートされているいずれかの SDK によってリージョンごとに、対応する[優先リージョンの一覧](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations)を構成します。

次のスニペットは、複数リージョンのアプリケーションを初期化する方法を示しています。 ここでは、Azure Cosmos DB アカウント `contoso.documents.azure.com` は 2 つのリージョン、米国西部と北ヨーロッパで構成されています。 

* アプリケーションは米国西部リージョンにデプロイされている (たとえば Azure App Services を使用) 
* 低待機時間読み取りの最初の優先リージョンとして `West US` を指定している
* 2 番目の優先リージョン (リージョン内障害時の高可用性のため) として `North Europe` を指定している

SQL API では、この構成は次のスニペットのようになります。

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "<Fill your Cosmos DB account's AuthorizationKey>",
    usConnectionPolicy);
```

アプリケーションは北ヨーロッパでもデプロイされていますが、逆の優先リージョン順位です。 つまり、北ヨーロッパが低待機時間読み取りの最初に指定されています。 そして米国西部が、リージョン内障害時の高可用性のため 2 番目の優先リージョンとして指定されています。

下記のアーキテクチャの図は、複数リージョンのアプリケーションのデプロイを示しており、Cosmos DB とアプリケーションが Azure の 4 つの地理的リージョンで利用できるように構成されています。  

![Azure Cosmos DB を使用して世界各地に分散してデプロイされたアプリケーション](./media/regional-failover/app-deployment.png)

では、Cosmos DB サービスによる自動フェールオーバーを使用したリージョン内障害の処理方法を見てみましょう。 

## <a id="AutomaticFailovers"></a>自動フェールオーバー
Azure のリージョン内障害やデータセンターの停止はめったに発生しませんが、発生した場合、Cosmos DB は、影響を受けるリージョンに存在するすべての Cosmos DB アカウントのフェールオーバーを自動的にトリガーします。 

**読み取りリージョンで障害が起きた場合**

影響を受けるリージョンのいずれかに読み取りリージョンを指定している Cosmos DB アカウントは、自動的にそれらの書き込みリージョンから切断され、オフラインとしてマークされます。 Cosmos DB SDK ではリージョン内探索プロトコルが実装されており、リージョンが使用可能になったことを自動的に検出し、優先リージョンの一覧に従って次の使用可能なリージョンに読み込み呼び出しをリダイレクトすることができます。 優先リージョンの一覧にあるいずれのリージョンも使用可能でない場合、呼び出しは自動的に現在の書き込みリージョンに戻ります。 リージョン内フェールオーバーを処理するアプリケーション コードは、変更する必要はありません。 この処理全体において、Cosmos DB によって一貫性の保証が継続的に実現されています。

![Azure Cosmos DB での読み取りリージョン障害](./media/regional-failover/read-region-failures.png)

影響を受けたリージョンが障害から回復したら、そのリージョン内で影響を受けたすべての Cosmos DB アカウントは、サービスにより自動的に復旧されます。 そして、影響を受けたリージョンに読み取りリージョンがあった Cosmos DB アカウントは、自動的に現在の書き込みリージョンと同期してオンラインになります。 Cosmos DB SDK では、新しいリージョンが使用可能かを検出し、そのリージョンを現在の読み取りリージョンとして選択すべきかどうか、アプリケーションで構成された優先リージョンの一覧に基づいて判断します。 それ以降の読み取りは、アプリケーション コードを変更しなくても、回復したリージョンにリダイレクトされます。

**書き込みリージョンで障害が起きた場合**

影響を受けたリージョンが現在の書き込みリージョンであり、Azure Cosmos DB アカウントの自動フェールオーバーが有効になっている場合、リージョンは自動的にオフラインとしてマークされます。 そして、影響を受けた Azure Cosmos DB アカウントでは、代替リージョンが書き込みリージョンとして昇格されます。 Azure Portal を使用して、または[プログラムを使用して](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange)、自動フェールオーバーを有効にし、Azure Cosmos DB アカウントのリージョンの選択順序を完全に制御することができます。 

![Azure Cosmos DB のフェールオーバーの優先順位](./media/regional-failover/failover-priorities.png)

自動フェールオーバー中、Azure Cosmos DB は、指定された優先順位に基づいて、指定の Azure Cosmos DB アカウント用の次の書き込みリージョンを自動的に選択します。 アプリケーションは、DocumentClient クラスの WriteEndpoint プロパティを使用して、書き込みリージョン内の変更を検出できます。

![Azure Cosmos DB での書き込みリージョン障害](./media/regional-failover/write-region-failures.png)

影響を受けたリージョンが障害から回復したら、そのリージョン内で影響を受けたすべての Cosmos DB アカウントは、サービスにより自動的に復旧されます。 

* 障害発生中に読み取りリージョンにレプリケートされなかった以前の書き込みリージョン内のデータは、競合フィードとして発行されます。 アプリケーションは競合フィードを読み取って、アプリケーションに基づくロジック固有の競合を解決し、必要に応じて Azure Cosmos DB アカウントに更新したデータを書き戻すことができます。 
* 以前の書き込みリージョンは読み取りリージョンとして再作成され、自動的にオンラインに戻されます。 
* 自動的にオンラインに戻った読み取りリージョンは、Azure Portal 経由または[プログラムを使用して](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate)手動フェールオーバーを実行することで、書き込みリージョンとして再構成できます。

次のコード スニペットは、影響を受けたリージョンが障害から復旧した後の競合の処理方法を示しています。

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a>手動フェールオーバー

自動フェールオーバーに加えて、指定の Cosmos DB アカウントの現在の書込みリージョンは、手動で動的に、既存の書込みリージョンのいずれかに変更することができます。 手動フェールオーバーは、Azure Portal から、または[プログラムを使用して](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate)開始することができます。 

手動フェールオーバーは**データ損失なし**および**可用性の損失なし**を保証するもので、指定した Cosmos DB アカウントの書き込みリージョンを元のリージョンから新しいリージョンに適切に移行します。 自動フェールオーバーの場合と同様に、Cosmos DB SDK は手動フェールオーバー時に自動的に書き込みリージョンの変更処理を行い、呼び出しが自動的に新しい書き込みリージョンにリダイレクトされるようにします。 フェールオーバー管理のためにお使いのアプリケーションのコードや構成を変更する必要はありません。 

![Azure Cosmos DB の手動フェールオーバー](./media/regional-failover/manual-failovers.png)

手動フェールオーバーが役立つ一般的なシナリオには、次のようなものがあります。

**時系列のパターンに合わせる**: お使いのアプリケーションにおいて、1 日の時刻に基づいたトラフィックのパターンが予測可能な場合、書き込み状態を定期的に 1 日の時刻に基づいて最もアクティブな地域リージョンに変更することができます。

**サービスの更新**: グローバルに分散デプロイされているアプリケーションでは、計画的なサービスの更新の実行中に、トラフィック マネージャーを使用して別のリージョンにトラフィックを再ルーティングすることがあります。 そのようなアプリケーションのデプロイでは、手動フェールオーバーを使用して、サービスの更新中にトラフィックがアクティブになるリージョンに、書き込み状態を保持することができます。

**ビジネス継続性と障害復旧 (BCDR) および高可用性と障害復旧 (HADR) のテスト**: ほとんどのエンタープライズ アプリケーションには、開発およびリリース プロセスの一部として、ビジネス継続性のテストが含まれています。 BCDR と HADR のテストは、コンプライアンス証明と、リージョン内障害の際のサービス可用性の保証のために、しばしば重要な手順となります。 ストレージに Cosmos DB を使用しているアプリケーションで BCDR に対応できているかテストするには、Cosmos DB アカウントの手動フェールオーバーをトリガーするか、動的なリージョンの追加と削除を行います。

この記事では、Cosmos DB における手動および自動フェールオーバーの動作の仕組み、グローバルに使用する Cosmos DB アカウントとアプリケーションの構成方法を確認しました。 Cosmos DB のグローバルなレプリケーション サポートを使用すると、エンド ツー エンドの待機時間を向上させ、リージョンで障害が発生しても高可用性を保証することができます。 

## <a id="NextSteps"></a>次のステップ
* Cosmos DB の[グローバル配布](distribute-data-globally.md)サポートについて確認する
* [Azure Cosmos DB とのグローバルな整合性](consistency-levels.md)について確認する
* Azure Cosmos DB の [SQL API](tutorial-global-distribution-sql-api.md) を使用して複数のリージョンで開発する
* Azure Cosmos DB を使用して[複数リージョン ライター アーキテクチャ](multi-region-writers.md)を作成する方法を確認する

