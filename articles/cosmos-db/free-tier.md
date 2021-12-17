---
title: Azure Cosmos DB の Free レベル
description: Azure Cosmos DB の Free レベルを使用して、アプリケーションを開始、開発およびテストします。 Free レベルの場合、そのアカウントでの最初の 1000 RU/秒と 25 GB のストレージが無料で利用できます。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/25/2021
ms.author: sngun
ms.openlocfilehash: ddc09c428a7779f5e059ead02dbd869a592813e5
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554776"
---
# <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB の Free レベル 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB の Free レベルを使用すると、アプリケーションの利用開始、開発、およびテストを簡単に行えるようになります。また、小規模な実稼働ワークロードを無料で実行することもできます。 アカウント上で Free レベルを有効にすると、そのアカウントでの最初の 1000 RU/秒と 25 GB のストレージが無料で利用できるようになります。 これらの制限を超えて消費されるスループットとストレージは、通常の価格で請求されます。 Free レベルは、プロビジョニングされたスループット、自動スケーリング スループット、単一または複数の書き込みリージョンを持つすべての API アカウントで使用できます。

Free レベルは、アカウント全有効期間にわたって持続し、通常の Azure Cosmos DB アカウントのすべての[利点と機能](introduction.md#key-benefits)を使用できます。 これらの利点とは、無制限のストレージとスループット (RU/秒)、SLA、高可用性、すべての Azure リージョンなどでのターンキー グローバル分散などです。

Azure サブスクリプションにつき所有できる Free レベルの Azure Cosmos DB アカウントは 1 つまでです。また、アカウントの作成時にオプトインする必要があります。 Free レベルの割引を適用するオプションが表示されない場合は、サブスクリプション内の別のアカウントが Free レベルで既に有効になっていることを意味します。 Free レベルのアカウントを作成し、その後に削除すると、新しいアカウントに Free レベルを適用できます。 新しいアカウントを作成する場合は、利用可能であれば Free レベルの割引を有効にすることをお勧めします。

> [!NOTE]
> Free レベルは現在、サーバーレス アカウントでは使用できません。

## <a name="free-tier-with-shared-throughput-database"></a>共有スループット データベースを使用する Free レベル

共有スループット モデルでは、データベースでスループットをプロビジョニングすると、データベースのすべてのコンテナーでスループットが共有されます。 Free レベルを使用する場合は、最大 1,000 RU/秒 の共有データベースを無料でプロビジョニングできます。 データベース内のすべてのコンテナーでスループットが共有されます。 

通常のアカウントと同様に、Free レベルのアカウントでは、共有スループット データベースに最大 25 個のコンテナーを含めることができます。 共有スループットを持つ追加のデータベースや 1000 RU/秒を超える専用スループットを持つコンテナーは、通常の価格で課金されます。

## <a name="free-tier-with-azure-discount"></a>Azure 割引付きの Free レベル

Azure Cosmos DB Free レベルは、[Azure 無料アカウント](optimize-dev-test.md#azure-free-account)とは異なります。 オプトインするには、Azure 無料アカウント サブスクリプションで Azure Cosmos DB の Free レベル アカウントを作成します。 最初の 12 か月間は、1400 RU/秒 (Azure Cosmos DB Free レベルから 1000 RU/s、Azure 無料アカウントから 400 RU/秒) と 50 GB のストレージ (Azure Cosmos DB Free レベルから 25 GB、Azure 無料アカウントから 25 GB) の結合割引を受けることができます。 12 か月の有効期限が切れた後も、Azure Cosmos DB アカウントの有効期間中、Azure Cosmos DB Free レベルから 1000 RU/秒 と 25 GB を引き続き得られます。 料金の積み上げ方法の例については、「[Billing examples with free tier accounts](understand-your-bill.md#azure-free-tier)」\(Free レベルアカウントを使用した課金例\) を参照してください。

> [!NOTE]
> Azure Cosmos DB Free レベルは、Azure 無料アカウントとは異なります。 Azure 無料アカウントでは、Azure クレジットおよびリソースが一定の期間無料で提供されます。 この無料アカウントの一部として Azure Cosmos DB を使用する場合、25 GB のストレージと 400 RU/秒のプロビジョニング済みスループットが 12 か月間利用できます。

## <a name="best-practices-to-keep-your-account-free"></a>アカウントを無料に保つベスト プラクティス

Azure Cosmos DB Free レベルを使用する場合、アカウントを完全無料で維持するために、無料レベルで提供される RU/秒 またはストレージ使用量以外の追加の RU/秒 またはストレージ消費量をアカウントに含めないでください。

たとえば、月額料金が発生しないオプションを次に示します。

* プロビジョニングされたスループットが最大 1,000 RU/秒 の 1 つのデータベース。
* 最大 400 RU/秒、および最大 600 RU/秒のスループットがプロビジョニングされている 2 つのコンテナー。
* 1 つのリージョンに 2 つのリージョンがあり、最大 500 RU/秒のスループットがプロビジョニングされた 1 つのコンテナーがあるアカウント。

## <a name="create-an-account-with-free-tier"></a>Free レベルのアカウントを作成する

Free レベルアカウントは、Azure portal、PowerShell、CLI、または Azure Resource Manager (ARM) テンプレートから作成できます。 アカウントの作成中に Free レベルを選択することはできますが、アカウントの作成後に設定することはできません。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用してアカウントを作成する場合は、 **[Apply Free Tier Discount]** \(Free レベルの割引を適用\) オプションを **[Apply]** \(適用\) に設定します。 詳細な手順については、「[create a new account with free tier](create-cosmosdb-resources-portal.md)」\(free レベルを使用して新しいアカウントを作成する\) を参照してください。

### <a name="arm-template"></a>ARM テンプレート

ARM テンプレートを使用して Free レベルアカウントを作成するには、`"enableFreeTier": true` プロパティを設定します。 完全なテンプレートについては、[Free レベルを使用して ARM テンプレート](manage-with-templates.md#free-tier) をデプロイする例を参照してください。

### <a name="cli"></a>CLI

CLI を使用して Free レベルのアカウントを作成するには、 `--enable-free-tier` パラメーターを True に設定します。

```azurecli-interactive
# Create a free tier account for SQL API
az cosmosdb create \
    -n "Myaccount" \
    -g "MyResourcegroup" \
    --enable-free-tier true \
    --default-consistency-level "Session"
    
```

### <a name="powershell"></a>PowerShell

Azure PowerShell を使用して Free レベルのアカウントを作成するには、 `-EnableFreeTier` パラメーターを True に設定します。

```powershell-interactive
# Create a free tier account for SQL API. 
New-AzCosmosDBAccount -ResourceGroupName "MyResourcegroup" `
    -Name "Myaccount" `
    -ApiKind "sql" `
    -EnableFreeTier true `
    -DefaultConsistencyLevel "Session" `
```

## <a name="next-steps"></a>次の手順

Free レベルのアカウントを作成したら、次の記事を使用して Azure Cosmos DB でアプリの構築を開始できます。

* Azure Cosmos DB リソースを管理するための [.NET V4 SDK を使用したコンソール アプリを構築します](create-sql-api-dotnet-v4.md)。
* [Azure Cosmos DB の MongoDB 用 API を使用して .NET Web アプリを構築する](mongodb/create-mongodb-dotnet.md)
* [ギャラリーから notebook をダウンロード](publish-notebook-gallery.md#download-a-notebook-from-the-gallery) し、データを解析します。
* [Azure Cosmos DB の課金内容の確認](understand-your-bill.md)の詳細について学習します
