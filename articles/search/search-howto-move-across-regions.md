---
title: サービス リソースをリージョン間で移動する方法
titleSuffix: Azure Cognitive Search
description: この記事では、Azure クラウドで Azure Cognitive Search リソースをあるリージョンから別のリージョンに移動する方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/06/2020
ms.openlocfilehash: 183a937a232dbd28962bb7d6ef42b0d78b8a81fd
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78850688"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Azure Cognitive Search サービスを別の Azure リージョンに移動する

場合によっては、既存の検索サービスを別のリージョンに移動することについてお客様が問い合わせることがあります。 現時点では、このタスクを支援する組み込みのメカニズムやツールはありません。 この記事で後述する手動のプロセスがあります。

> [!NOTE]
> Azure portal では、すべてのサービスに **Export template** コマンドがあります。 Azure Cognitive Search の場合、このコマンドを実行すると、サービスの基本定義 (名前、場所、レベル、レプリカ、およびパーティション数) が生成されますが、サービスのコンテンツは認識されず、キー、ロール、またはログにも引き継がれません。 コマンドは存在しますが、検索サービスの移動には使用しないことをお勧めします。

## <a name="steps-for-moving-a-service"></a>サービスを移動する手順

検索サービスを別のリージョンに移動する必要がある場合、アプローチは次の手順のようになります。

1. サービスを再配置した場合の影響を理解するために、関連するサービスを特定します。 ログ、ナレッジ ストア、または外部データソースとして Azure Storage を使用している可能性があります。 AI エンリッチメントに Cognitive Services を使用している可能性があります。 他のリージョンのサービスへのアクセスは一般的に行われていることですが、追加の帯域幅料金も伴います。 AI エンリッチメントを使用している場合は、Cognitive Services と Azure Cognitive Search を同じリージョンに配置する必要があります。

1. サービスのオブジェクトの完全な一覧を表示するには、既存のサービスをインベントリします。 ログを有効にした場合は、履歴レコードに必要な可能性のあるレポートを作成してアーカイブします。

1. 新しいリージョンの価格と可用性を確認して、Azure Cognitive Search の可用性と、同じリージョンに作成する関連サービスを確認します。 機能パリティをチェックします。 一部のプレビュー機能では、可用性が制限されています。

1. 新しいリージョンにサービスを作成し、既存のインデックス、インデクサー、データ ソース、スキルセット、ナレッジ ストア、およびシノニム マップをソース コードから再発行します。 サービス名は一意である必要があるため、既存の名前を再利用することはできません。

1. 該当する場合は、インデックスとナレッジ ストアを再度読み込みます。 アプリケーション コードを使用して JSON データをインデックスにプッシュするか、インデクサーを再度実行して外部ソースからドキュメントをプルします。 

1. ログ記録を有効にし、それらを使用している場合はセキュリティ ロールを再度作成します。

1. クライアント アプリケーションとテスト スイートを更新し、新しいサービス名と API キーを使用してから、すべてのアプリケーションをテストします。

1. 新しいサービスが完全にテストされ、運用可能になったら、古いサービスを削除します。

## <a name="next-steps"></a>次のステップ

+ [レベルの選択](search-sku-tier.md)
+ [Search Service の作成](search-create-service-portal.md)
+ [検索ドキュメントの読み込み](search-what-is-data-import.md)
+ [ログ記録を有効化する](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->