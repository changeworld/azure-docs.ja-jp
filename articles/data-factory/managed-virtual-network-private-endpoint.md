---
title: マネージド仮想ネットワークとマネージド プライベート エンドポイント
description: Azure Data Factory におけるマネージド仮想ネットワークとマネージド プライベート エンドポイントについて説明します。
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions, devx-track-azurepowershell
ms.date: 09/28/2021
ms.openlocfilehash: c4baf3ee8fdb26bd361dfafbaa29953f6d1d13f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469554"
---
# <a name="azure-data-factory-managed-virtual-network"></a>Azure Data Factory のマネージド仮想ネットワーク

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure Data Factory におけるマネージド仮想ネットワークとマネージド プライベート エンドポイントについて説明します。


## <a name="managed-virtual-network"></a>マネージド仮想ネットワーク

Azure Data Factory マネージド仮想ネットワーク (VNET) 内に Azure Integration Runtime (IR) を作成すると、マネージド仮想ネットワークを使用して統合ランタイムがプロビジョニングされ、プライベート エンドポイントを利用して、サポートされているデータ ストアに安全に接続します。 

マネージド仮想ネットワーク内に Azure IR を作成することで、データ統合プロセスの分離と安全が確保されます。 

マネージド仮想ネットワークを使用する利点は次のとおりです。

- マネージド仮想ネットワークを使用すると、仮想ネットワークを管理する負担を Azure Data Factory にオフロードできます。 Azure Integration Runtime のサブネットは最終的に Virtual Network の多くのプライベート IP を使用する可能性があり、事前のネットワーク インフラストラクチャ計画が必要になりますが、このサブネットを作成する必要がありません。 
- データ統合を安全に行うために、Azure のネットワークに関する深い知識は必要ありません。 それよりむしろ、データ エンジニアにとっては、セキュリティで保護された ETL の使用開始が非常に簡単になっています。 
- マネージド仮想ネットワークは、マネージド プライベート エンドポイントと共に、データ流出を防止します。 

> [!IMPORTANT]
>現在、マネージド仮想ネットワークは、Azure Data Factory リージョンと同じリージョン内でのみサポートされます。

> [!Note]
>既存のグローバル Azure 統合ランタイムを、Azure Data Factory マネージド仮想ネットワーク内の Azure 統合ランタイムに切り替えることはできません。また、その逆も同様です。
 

:::image type="content" source="./media/managed-vnet/managed-vnet-architecture-diagram.png" alt-text="ADF マネージド仮想ネットワークのアーキテクチャ":::

## <a name="managed-private-endpoints"></a>マネージド プライベート エンドポイント

マネージド プライベート エンドポイントは、Azure Data Factory マネージド仮想ネットワークに作成されるプライベート エンドポイントで、Azure リソースへのプライベート リンクを確立します。 これらのプライベート エンドポイントは、Azure Data Factory によって自動的に管理されます。 

:::image type="content" source="./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png" alt-text="新しいマネージド プライベート エンドポイント":::

Azure Data Factory では、プライベート リンクがサポートされます。 プライベート リンクを使用すると、Azure (PaaS) サービス (Azure Storage、Azure Cosmos DB、Azure Synapse Analytics など) にアクセスできます。

プライベート リンクを使用すると、データ ストアとマネージド仮想ネットワーク間のトラフィックは、全面的に Microsoft のバックボーンネットワークを介して転送されます。 プライベート リンクによって、データ流出のリスクから身を守ることができます。 リソースへのプライベート リンクは、プライベート エンドポイントを作成することによって確立します。

プライベート エンドポイントでは、マネージド仮想ネットワークでプライベート IP アドレスを使用して、効率的にサービスを取り込みます。 プライベート エンドポイントは、サービス全体にではなく Azure 内の特定のリソースにマップされます。 顧客は、その組織で承認されている特定のリソースに接続を制限することができます。 [プライベート リンクとプライベート エンドポイント](../private-link/index.yml)についての詳しい情報をご覧ください。

> [!NOTE]
> Azure のデータ ソースとの接続にはすべて、マネージド プライベート エンドポイントを作成することをお勧めします。 
 
> [!WARNING]
> PaaS データ ストア (BLOB、ADLS Gen2、Azure Synapse Analytics) に対してプライベート エンドポイントが既に作成されており、すべてのネットワークからのアクセスが許可されている場合でも、ADF からはマネージド プライベート エンドポイントを使用してのみアクセスできます。 プライベート エンドポイントが存在しない場合は、このようなシナリオで作成する必要があります。 

Azure Data Factory にマネージド プライベート エンドポイントを作成すると、プライベート エンドポイント接続は "保留" 状態で作成されます。 承認ワークフローが開始されます。 接続を承認または拒否する役割は、プライベート リンク リソースの所有者が担います。

:::image type="content" source="./media/tutorial-copy-data-portal-private/manage-private-endpoint.png" alt-text="マネージド プライベート エンドポイント":::

所有者が接続を承認した場合、プライベート リンクが確立されます。 それ以外の場合、プライベート リンクは確立されません。 どちらの場合も、マネージド プライベート エンドポイントは接続の状態で更新されます。

:::image type="content" source="./media/tutorial-copy-data-portal-private/approve-private-endpoint.png" alt-text="マネージド プライベート エンドポイントを承認する":::

承認済み状態のマネージド プライベート エンドポイントのみが、指定されたプライベート リンク リソースにトラフィックを送信できます。

## <a name="interactive-authoring"></a>インタラクティブな作成
インタラクティブな作成機能は、テスト接続、フォルダー一覧とテーブル リストの参照、スキーマの取得、データのプレビューなどの機能に使用されます。 ADF で管理されている仮想ネットワーク内の Azure Integration Runtime を作成または編集する際に、インタラクティブな作成を有効にすることができます。 バックエンド サービスでは、インタラクティブな作成機能のためにコンピューティングが事前に割り当てられます。 そうしないと、インタラクティブな操作が実行されるたびにコンピューティングが割り当てられ、時間がかかります。 インタラクティブな作成の Time To Live (TTL) は 60 分です。つまり、前回のインタラクティブな作成操作から 60 分が経過すると、この機能が自動的に無効になります。

:::image type="content" source="./media/managed-vnet/interactive-authoring.png" alt-text="インタラクティブな作成":::

## <a name="activity-execution-time-using-managed-virtual-network"></a>マネージド仮想ネットワークを使用したアクティビティの実行時間
設計上、データ ファクトリごとに 1 つの計算ノードを予約していないため、マネージド仮想ネットワーク内の Azure 統合ランタイムは、グローバル Azure 統合ランタイムよりもキュー時間が長く、各アクティビティが開始されるまでにウォームアップがあります。これは、Azure 統合ランタイムではなく、主に仮想ネットワーク参加で発生します。 パイプライン アクティビティや外部アクティビティを含む非 Copy アクティビティの場合、初めてトリガーするときに、60 分の Time To Live (TTL) があります。 TTL 内では、ノードが既にウォームアップされているため、キュー時間が短くなります。 
> [!NOTE]
> Copy アクティビティでは、まだ TTL がサポートされていません。

> [!NOTE]
> 2 コピー アクティビティ用の DIU は、マネージド仮想ネットワークではサポートされていません。

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Azure PowerShell を使用したマネージド仮想ネットワークの作成
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}" -Properties @{}

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

### <a name="supported-data-sources"></a>サポートされるデータ ソース
次のデータ ソースはネイティブ プライベート エンドポイントをサポートしており、ADF マネージド仮想ネットワークからプライベート リンクを介して接続できます。
- Azure Blob Storage (ストレージ アカウント V1 は含まれません)
- Azure Cognitive Search
- Azure Cosmos DB SQL API
- Azure Data Lake Storage Gen2
- Azure Database for MariaDB
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Files (ストレージ アカウント V1 は含まれません)
- Azure Key Vault
- Azure Machine Learning
- Azure Private Link サービス
- Azure Purview
- Azure SQL Database (Azure SQL Managed Instance を含まない)
- Azure Synapse Analytics
- Azure Table Storage (ストレージ アカウント V1 は含まれません)

> [!Note]
> 引き続き、パブリック ネットワークを介して、Data Factory でサポートされているすべてのデータ ソースにアクセスできます。

> [!NOTE]
> 現在、Azure SQL Managed Instance はネイティブ プライベート エンドポイントをサポートしていないため、Private Link サービスと Load Balancer を使用することで、マネージド仮想ネットワークからアクセスできます。 [プライベート エンドポイントを使用して、Data Factory マネージド VNET から SQL Managed Instance にアクセスする方法](tutorial-managed-virtual-network-sql-managed-instance.md)に関するページを参照してください。

### <a name="on-premises-data-sources"></a>オンプレミス データ ソース
プライベート エンドポイントを使用して、マネージド仮想ネットワークからオンプレミスのデータ ソースにアクセスするには、[プライベート エンドポイントを使用して Data Factory マネージド VNET からオンプレミスの SQL Server にアクセスする方法](tutorial-managed-virtual-network-on-premise-sql-server.md)に関するチュートリアルを参照してください。

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory マネージド仮想ネットワークは次の Azure リージョンでご利用いただけます。
一般に、マネージド仮想ネットワークは、次の点を除いて、すべての Azure Data Factory リージョンで利用できます。
- インド南部


### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>ADF マネージド仮想ネットワークからのパブリック エンドポイントを介した送信方向の通信
- 送信方向の通信用には、すべてのポートが開かれています。
- Azure Storage および Azure Data Lake Gen2 では、ADF マネージド仮想ネットワークからパブリック エンドポイント経由で接続することはサポートされていません。

### <a name="linked-service-creation-of-azure-key-vault"></a>Azure Key Vault のリンクされたサービスの作成 
- Azure Key Vault のリンクされたサービスを作成する場合、Azure Integration Runtime の参照はありません。 そのため、Azure Key Vault のリンクされたサービスの作成中にプライベート エンドポイントを作成することはできません。 ただし、Azure Key Vault のリンクされたサービスを参照するデータ ストアに対してリンクされたサービスを作成し、そのリンクされたサービスが、マネージド仮想ネットワークが有効になっている Azure Integration Runtime を参照する場合は、作成中に Azure Key Vault のリンクされたサービスのプライベート エンドポイントを作成できます。 
- Azure Key Vault のリンクされたサービスの **テスト接続** 操作では、単に URL 形式が検証され、ネットワーク操作は実行されません。
- **[プライベート エンド ポイントの使用]** 列は、Azure Key Vault 用のプライベート エンドポイントを作成した場合でも、常に空白として表示されます。

### <a name="linked-service-creation-of-azure-hdi"></a>Azure HDI のリンク サービスの作成
- プライベート リンク サービスおよびロード バランサーとポート転送を使用して HDI 用のプライベート エンドポイントを作成した場合でも、 **[Using private endpoint]\(プライベート エンドポイントの使用\)** 列は常に空白として表示されます。

:::image type="content" source="./media/managed-vnet/akv-pe.png" alt-text="AKV 用のプライベート エンドポイント":::

## <a name="next-steps"></a>次のステップ

- チュートリアル:[マネージド仮想ネットワークとプライベート エンドポイントを使用してコピー パイプラインを構築する](tutorial-copy-data-portal-private.md) 
- チュートリアル:[マネージド仮想ネットワークとプライベート エンドポイントを使用してマッピング データフロー パイプラインを構築する](tutorial-data-flow-private.md)
