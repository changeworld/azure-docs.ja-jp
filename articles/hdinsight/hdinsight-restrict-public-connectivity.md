---
title: Azure HDInsight でパブリック接続を制限する
description: すべてのアウトバウンド パブリック IP アドレスへのアクセスを削除する方法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 457e72f50b24b5850311b2869ab6daa956512ae6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215449"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight"></a>Azure HDInsight でパブリック接続を制限する

Azure HDInsight の[既定の仮想ネットワーク アーキテクチャ](./hdinsight-virtual-network-architecture.md)では、HDInsight リソース プロバイダーはパブリック ネットワーク経由でクラスターと通信します。 この記事では、インバウンド接続がプライベート ネットワークに限定される制限された HDInsight クラスターを作成するために使用できる、高度な制御について説明します。 

HDInsight クラスターと依存リソースの間にパブリック接続が必要な場合は、「[Azure HDInsight のネットワーク トラフィックを制御する](./control-network-traffic.md)」のガイドラインに従って、クラスターの接続を制限することを検討します。 パブリック接続を制限するだけでなく、HDInsight クラスターで使用する Azure Private Link 対応の依存関係リソースを構成することもできます。

次の図は、`resourceProviderConnection` が "*アウトバウンド*" に設定されている場合に考えられる HDInsight 仮想ネットワーク アーキテクチャです。

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="アウトバウンド リソース プロバイダー接続を使用する HDInsight アーキテクチャの図。":::

> [!NOTE]
> パブリック接続を制限することは、Private Link を有効にするための前提条件であり、同じ機能とは見なさないでください。

## <a name="initialize-a-restricted-cluster"></a>制限するクラスターを初期化する

既定では、HDInsight リソース プロバイダーにより、パブリック IP アドレスを使用することによってクラスターへの "*インバウンド*" 接続が使用されます。 `resourceProviderConnection` ネットワーク プロパティを "*アウトバウンド*" に設定すると、接続が HDInsight リソース プロバイダーに反転されて、接続は常にクラスター内部から開始されてリソース プロバイダーに向かいます。 

この構成では、インバウンド接続がないときは、ネットワーク セキュリティ グループでインバウンド サービス タグを構成する必要はありません。 また、ユーザー定義のルートを使用して、ファイアウォールまたはネットワーク仮想アプライアンスをバイパスする必要もありません。

クラスターを作成した後は、制限される HDInsight クラスターに必要な DNS レコードを追加して、適切な DNS 解決を設定します。 Azure で管理されるパブリック DNS ゾーンに、`azurehdinsight.net` という正規名の DNS レコード (CNAME) が作成されます。

```dns
<clustername>    CNAME    <clustername>-int
```

完全修飾ドメイン名 (FQDN) を使用してクラスターにアクセスするには、次の方法からニーズに合ったものを使用できます。

- 内部ロード バランサーのプライベート IP アドレスを直接使用します。
- 独自のプライベート DNS ゾーンを使用して、クラスターのエンドポイントをオーバーライドします。 この場合、ゾーンの名前は `azurehdinsight.net` でなければなりません。

たとえば、プライベート DNS ゾーン `azurehdinsight.net` の場合は、必要に応じてプライベート IP アドレスを追加できます。

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> パブリック接続が有効になっている他のクラスターと同じ (`azurehdinsight.net` のプライベート DNS ゾーンが含まれる) 仮想ネットワークに、制限されるクラスターを配置することはお勧めしません。 意図しない DNS 解決動作または競合が発生するおそれがあります。

DNS のセットアップを簡単にするため、FQDN とそれに対応するプライベート IP アドレスを、クラスターの `GET` 応答の一部として返します。 この PowerShell スニペットを使用して始めることができます。

```powershell
<#
    This script is an example to help you get started with automation and can be adjusted based on your needs.
    This script assumes:
    - The HDInsight cluster has already been created, and the context where this script is run has permissions to read/write resources in the same resource group.
    - The private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of the Az.HDInsight module.

#>
$subscriptionId = "<Replace with subscription for deploying HDInsight clusters, and containing private DNS zone resource>"

$clusterName = "<Replace with cluster name>"
$clusterResourceGroupName = "<Replace with resource group name>"

# For example, azurehdinsight.net for public cloud.
$dnsZoneName = "<Replace with private DNS zone name>"
$dnsZoneResourceGroupName = "<Replace with private DNS zone resource group name>"

Connect-AzAccount -SubscriptionId $subscriptionId

# 1. Get cluster endpoints
$clusterEndpoints = $(Get-AzHDInsightCluster -ClusterName $clusterName ` -ResourceGroupName $clusterResourceGroupName).ConnectivityEndpoints

$endpointMapping = @{}

foreach($endpoint in $clusterEndpoints)
{
    $label = $endpoint.Location.ToLower().Replace(".$dnsZoneName".ToLower(), "")
    $ip = $endpoint.PrivateIPAddress

    $endpointMapping.Add($label, $ip)
}

# 2. Confirm that the DNS zone exists.
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone:
#    - If the entries already exist, update to the new IP.
#    - If the entries don't exist, create them.
$recordSets = Get-AzPrivateDnsRecordSet -ZoneName $dnsZoneName -ResourceGroupName $dnsZoneResourceGroupName -RecordType A

foreach($label in $endpointMapping.Keys)
{
    $updateRecord = $null

    foreach($record in $recordSets)
    {
        if($record.Name -eq $label)
        {
            $updateRecord = $record
            break;
        }
        
    }

    if($null -ne $updateRecord)
    {
        $updateRecord.Records[0].Ipv4Address = $endpointMapping[$label]
        Set-AzPrivateDnsRecordSet -RecordSet $updateRecord | Out-Null
    }
    else
    {
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $dnsZoneResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name $label `
            -RecordType A `
            -Ttl 3600 `
            -PrivateDnsRecord (New-AzPrivateDnsRecordConfig -Ipv4Address $endpointMapping[$label]) | Out-Null
    }
}

```

## <a name="add-private-link-connectivity-to-cluster-dependent-resources-optional"></a>クラスターに依存するリソースへの Private Link 接続を追加する (省略可能)

`resourceProviderConnection` を "*アウトバウンド*" に構成すると、プライベート エンドポイントを使用してクラスター固有のリソースにアクセスすることもできます。 これらのリソースには次が含まれます。

- ストレージ: Azure Data Lake Storage Gen2、Azure Blob Storage
- SQL メタストア: Apache Ranger、Ambari、Oozie、Hive
- Azure Key Vault 

これらのリソースにプライベート エンドポイントを使用することは必須ではありません。 ただし、これらのリソースにプライベート エンドポイントを使用する予定がある場合は、HDInsight クラスターを作成する前に、リソースを作成し、プライベート エンドポイントと DNS エントリを構成する必要があります。 これらのすべてのリソースに、プライベート エンドポイントまたはそれ以外の方法で、クラスター サブネット内からアクセスできる必要があります。

プライベート エンドポイント経由で Azure Data Lake Storage Gen2 に接続するときは、Gen2 ストレージ アカウントに `blob` と `dfs` の両方のエンドポイントが設定されていることを確認します。 詳細については、[プライベート エンドポイントの作成](../private-link/create-private-endpoint-portal.md)に関する記事を参照してください。

## <a name="use-a-firewall-optional"></a>ファイアウォールを使用する (省略可能)
HDInsight クラスターでは、引き続き、パブリック インターネットに接続して、アウトバウンドの依存関係を取得することができます。 アクセスを制限する必要がある場合は、[ファイアウォールを構成する](./hdinsight-restrict-outbound-traffic.md)ことができますが、必須ではありません。

## <a name="create-clusters"></a>クラスターの作成

次の JSON コード スニペットには、プライベート HDInsight クラスターを作成するために Azure Resource Manager テンプレートで構成する必要がある 2 つのネットワーク プロパティが含まれています。

```json
networkProperties: {
    "resourceProviderConnection": "Outbound"
}
```

数多くの HDInsight エンタープライズ セキュリティ機能 (Private Link など) を備えた完全なテンプレートについては、[HDInsight エンタープライズ セキュリティのテンプレート](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)を参照してください。

PowerShell を使用してクラスターを作成するには、[例](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)を参照してください。

Azure CLI を使用してクラスターを作成するには、[例](/cli/azure/hdinsight#az_hdinsight_create-examples)を参照してください。

## <a name="next-steps"></a>次の手順

* [クラスターで Private Link を有効にする](./hdinsight-private-link.md)
* [Azure HDInsight 用の Enterprise セキュリティ パッケージ](enterprise-security-package.md)
* [Azure HDInsight でのエンタープライズ セキュリティに関する一般情報とガイドライン](./domain-joined/general-guidelines.md)
