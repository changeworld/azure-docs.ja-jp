---
title: Azure HDInsight でパブリック接続を制限する (プレビュー)
description: すべてのアウトバウンド パブリック IP アドレスへのアクセスを削除する方法について説明します
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 8d4fc269137b9d11ab0db046288f1d548b911d7a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716273"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight-preview"></a>Azure HDInsight でパブリック接続を制限する (プレビュー)

## <a name="overview"></a>概要
Azure HDInsight の[既定の仮想ネットワーク アーキテクチャ](./hdinsight-virtual-network-architecture.md)では、HDInsight リソース プロバイダー (RP) はパブリック ネットワーク経由でクラスターと通信します。 この記事では、インバウンド接続がプライベート ネットワークに限定される制限された HDInsight クラスターを作成するために使用できる、高度な制御について説明します。 HDInsight クラスターとそれに依存するリソースに対して、またはその内部で、パブリック接続を使用することがある場合は、「[Azure HDInsight のネットワーク トラフィックを制御する](./control-network-traffic.md)」のガイドラインに従って、クラスターの接続を制限することを検討してください。 パブリック接続を制限するだけでなく、これらのクラスターで使用するように構成できる、プライベート リンクが有効な依存関係リソースのサポートを追加しています。

次の図は、`resourceProviderConnection` が "*アウトバウンド*" に設定されている場合に考えられる HDInsight 仮想ネットワーク アーキテクチャです。

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="送信リソース プロバイダー接続を使用した HDInsight アーキテクチャの図":::

> [!NOTE]
> パブリック接続を制限することは、Private Link を有効にするための前提条件であり、同じ機能とは見なさないでください。

## <a name="initialize-a-restricted-cluster"></a>制限付きクラスターを初期化する

HDInsight リソース プロバイダーでは、パブリック IP を使用するクラスターへの "*インバウンド*" 接続が既定で使用されます。 `resourceProviderConnection` ネットワーク プロパティが "*アウトバウンド*" に設定されていると、常にクラスター内部からリソース プロバイダーへと接続が開始されるように、HDInsight リソース プロバイダーへの接続が逆にされます。 この構成では、インバウンド接続がないので、ネットワーク セキュリティ グループ (NSG) でインバウンド サービス タグを構成したり、ユーザー定義ルート (UDR) を介してファイアウォールやネットワーク仮想アプライアンス (NVA) をバイパスしたりする必要はありません。

クラスターを作成した後は、制限付き HDInsight クラスターに必要な DNS レコードを追加して、適切な DNS 解決を設定する必要があります。 Azure で管理されたるパブリック DNS ゾーンで、`azurehdinsight.net` という正規名の DNS レコード (CNAME) が作成されます。

```dns
<clustername>    CNAME    <clustername>-int
```

クラスターの FQDN を使用してクラスターにアクセスするには、内部ロード バランサーのプライベート IP を直接使用するか、独自のプライベート DNS ゾーン (この場合、ゾーン名は `azurehdinsight.net` でなければなりません) を使用して、必要に応じてクラスター エンドポイントを上書きします。 たとえば、プライベート DNS ゾーン `azurehdinsight.net` の場合は、必要に応じてプライベート IP を追加できます。

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> パブリック接続が有効になっている他のクラスターと同じ VNet に (`azurehdinsight.net` のプライベート DNS ゾーンで) 制限付きクラスターを配置することは、意図しない DNS 解決の動作や競合が発生する可能性があるため、推奨されません。

DNS のセットアップを簡単にするため、FQDN とそれに対応するプライベート IP アドレスを、クラスターの `GET` 応答の一部として返します。 この PowerShell スニペットを使用して始めることができます。

```powershell
<#
    This script is offered as an example to help get started with automation and can be adjusted based on your needs.
    This script assumes:
    - HDInsight cluster has already been created and the context where this script is run has permissions to read/write resources in the same resource group.
    - Private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of Az.HDInsight module

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

# 2. Confirm DNS zone exists
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone
#    - If the entries already exist, update to the new IP
#    - If the entries do not exist, create them
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

## <a name="adding-private-link-connectivity-private-endpoints-to-cluster-dependent-resources-optional"></a>クラスターに依存するリソースへのプライベート リンク接続 (プライベート エンドポイント) の追加 (省略可能)

`resourceProviderConnection` を "*アウトバウンド*" に構成すると、ストレージ (Azure Data Lake Storage Gen2 と Windows Azure Storage Blob)、SQL メタ ストア (Apache Ranger、Ambari、Oozie、Hive)、Azure Key Vault などのクラスター固有のリソースに、プライベート エンドポイントを使用してアクセスすることもできます。 これらのリソースにプライベート エンドポイントを使用することは必須ではありませんが、これらのリソースでプライベート エンドポイントを使用する予定がある場合は、HDInsight クラスターを作成する前に、これらのリソースを作成し、プライベート エンドポイントと DNS エントリを構成する必要があります。 これらのすべてのリソースに、プライベート エンドポイントまたはそれ以外の方法で、クラスター サブネット内からアクセスできる必要があります。
プライベート エンドポイント経由で Azure Data Lake Storage Gen2 に接続するときは、Gen2 ストレージ アカウントに "blob" と "dfs" の両方のエンドポイントが設定されていることを確認してください。 詳細については、[プライベート エンドポイントの作成](../private-link/create-private-endpoint-portal.md)に関する記事を参照してください。

## <a name="using-firewall-optional"></a>ファイアウォールの使用 (省略可能)
HDInsight クラスターでは、まだ、パブリック インターネットに接続して、アウトバウンドの依存関係を取得することができます。 さらに厳しく制限する必要がある場合は、[ファイアウォールを構成する](./hdinsight-restrict-outbound-traffic.md)ことができますが、必須ではありません。

## <a name="how-to-create-clusters"></a>クラスターを作成する方法
### <a name="use-arm-template-properties"></a>ARM テンプレートのプロパティの使用

次の JSON コード スニペットには、プライベート HDInsight クラスターを作成するために ARM テンプレートで構成する必要がある 2 つのネットワーク プロパティが含まれています。

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

数多くの HDInsight エンタープライズ セキュリティ機能 (Private Link など) を備えた完全なテンプレートについては、[HDInsight エンタープライズ セキュリティのテンプレート](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)を参照してください。

### <a name="use-azure-powershell"></a>Azure PowerShell の使用

PowerShell を使用する場合は、[こちら](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)の例を参照してください。

### <a name="use-azure-cli"></a>Azure CLI の使用
Azure CLI を使用する場合は、[こちら](/cli/azure/hdinsight#az_hdinsight_create-examples)の例を参照してください。

## <a name="next-steps"></a>次の手順

* [クラスターで Private Link を有効にする](./hdinsight-private-link.md)
* [Azure HDInsight 用の Enterprise セキュリティ パッケージ](enterprise-security-package.md)
* [Azure HDInsight でのエンタープライズ セキュリティの一般情報とガイドライン](./domain-joined/general-guidelines.md)
