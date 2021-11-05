---
title: Azure-SSIS 統合ランタイムの標準インジェクション用に仮想ネットワークを構成する
description: Azure-SSIS 統合ランタイムの標準インジェクション用に仮想ネットワークを構成する方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ee88df19385041de660da311fe51c66099d15cc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091270"
---
# <a name="standard-virtual-network-injection-method"></a>標準仮想ネットワーク インジェクション方法

Azure Data Factory (ADF) で SQL Server Integration Services (SSIS) を使用する場合、Azure-SSIS 統合ランタイム (IR) を仮想ネットワークに参加させるにはスタンダードとエクスプレスの 2 つの方法があります。 標準の方法を使用する場合は、次の要件を満たした仮想ネットワークを構成する必要があります。

- *Microsoft.Batch* が、Azure-SSIS IR を参加させる仮想ネットワークを持つ Azure サブスクリプションに登録されたリソース プロバイダーであることを確認します。 詳細な手順については、「[リソースプロバイダーとして Azure Batch を登録する](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch)」セクションをご覧ください。

- Azure-SSIS IR を作成するユーザーに、仮想ネットワークまたはサブネットに参加するためのロールベースのアクセス制御 (RBAC) アクセス許可が付与されていることを確認します。  詳細については、後の「[仮想ネットワークのアクセス許可を選択する](#perms)」セクションをご覧ください。

- Azure-SSIS IR が参加するための仮想ネットワーク内の適切なサブネットを選択します。 詳しくは、後の「[サブネットを選択する](#subnet)」セクションをご覧ください。

特定のシナリオに応じて、必要に応じて次を構成できます。

- Azure-SSIS IR のアウトバウンド トラフィックに対して独自の静的パブリック IP (BYOIP) アドレスを使用する場合は、後の「[静的パブリック IP アドレスを構成する](#ip)」セクションをご覧ください。

- 仮想ネットワークで独自のドメイン ネーム システム (DNS) サーバーを使用する場合は、後の「[カスタム DNS サーバーを構成する](#dns)」セクションをご覧ください。

- ネットワーク セキュリティ グループ (NSG) を使用してサブネット上のインバウンドとアウトバウンドのトラフィックを制限する場合は、後の「[NSG を構成する](#nsg)」セクションをご覧ください。

- アウトバウンド トラフィックを監査および検査するためにユーザー定義ルート (UDR) を使用する場合は、後の「[UDR を構成する](#udr)」セクションをご覧ください。

- 仮想ネットワークのリソース グループ (独自のパブリック IP アドレスを使用する場合は、パブリック IP アドレスのリソース グループ) が特定の Azure ネットワーク リソースを作成および削除できることを確認します。 詳しくは、「[関連するリソース グループを構成する](#rg)」をご覧ください。 

- [Azure-SSIS IR のカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)に関する記事で説明されているように Azure-SSIS IR をカスタマイズすると、そのノードを管理する内部プロセスでは、事前に定義された *172.16.0.0* から *172.31.255.255* の範囲からプライベート IP アドレスが消費されます。 結果として、仮想またはオンプレミスのネットワークのプライベート IP アドレスの範囲が、この範囲と競合しないようにしてください。

次の図は、Azure-SSIS IR に必要な接続を示しています。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Azure-SSIS IR に必要な接続を示している図。":::

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>仮想ネットワークのアクセス許可を選択する

標準仮想ネットワークのインジェクションを有効にするには、Azure-SSIS IR を作成するユーザーに、仮想ネットワークとサブネットを参加させるために必要な RBAC アクセス許可を付与する必要があります。

- Azure-SSIS IR を Azure Resource Manager 仮想ネットワークに参加させようとしている場合は、次の 2 つのオプションがあります。

  - 組み込みの *ネットワーク共同作成者* ロールを使用します。 このロールには、必要なスコープよりずっと大きなスコープを持つ _Microsoft.Network/\*_ アクセス許可が備わっています。

  - 必要な _Microsoft.Network/virtualNetworks/\*/join/action_ アクセス許可のみを含むカスタム ロールを作成してください。 また、Azure-SSIS IR を Azure Resource Manager 仮想ネットワークに参加させるときに独自の静的パブリック IP アドレスを使用する場合は、_Microsoft.Network/publicIPAddresses/\*/join/action_ アクセス許可もロールに含めてください。

  - 詳細な手順については、「[仮想ネットワークのアクセス許可を付与する](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms)」セクションをご覧ください。

- Azure-SSIS IR を従来の仮想ネットワークに参加させる場合は、組み込みの *従来の仮想マシン共同作成者* ロールを使用することをお勧めします。 そうしない場合は、仮想ネットワークに参加するためのアクセス許可を含むカスタム ロールを作成する必要があります。 また、その組み込みまたはカスタムのロールに *MicrosoftAzureBatch* を割り当てる必要もあります。

## <a name="select-a-subnet"></a><a name="subnet"></a>サブネットを選択する

標準仮想ネットワークのインジェクションを有効にするには、Azure-SSIS IR を参加させる適切なサブネットを選ぶ必要があります。

- GatewaySubnet は仮想ネットワーク ゲートウェイ専用であるため、選ばないでください。

- 選んだサブネットに、Azure-SSIS IR ノードの数の少なくとも 2 倍の使用可能な IP アドレスがあることを確認します。 これらは、Azure-SSIS IR のパッチやアップグレードをロールアウトするときの中断を回避するために必要です。 また、Azure でもいくつかの IP アドレスが予約されており、各サブネットではそれを使用できません。 最初と最後の IP アドレスはプロトコルのコンプライアンスのために予約され、さらに 3 つのアドレスは Azure サービス用に予約されています。 詳しくは、[サブネットの IP アドレスの制限](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)に関するセクションをご覧ください。

- (Azure SQL Managed Instance、App Service など) 他の Azure サービスによって排他的に専有されているサブネットを使用しないでください。 

## <a name="configure-static-public-ip-addresses"></a><a name="ip"></a>静的パブリック IP アドレスを構成する

Azure-SSIS IR を仮想ネットワークに参加させながら、Azure-SSIS IR のアウトバウンド トラフィックに独自の静的パブリック IP アドレスを持ち込み、ファイアウォールで許可したい場合は、以下の要件を満たしていることを確認します。

- まだ他の Azure リソースに関連付けられていない 2 つの未使用のもののみを指定する必要があります。 余分なものは、定期的に Azure-SSIS IR をアップグレードするときに使用されます。 アクティブな Azure-SSIS IR 間で 1 つのパブリック IP アドレスを共有することはできません。

- これらの両方が、標準の種類の静的なものである必要があります。 詳しくは、[パブリック IP アドレスの SKU](../virtual-network/ip-services/public-ip-addresses.md#sku) に関するセクションをご覧ください。

- これらの両方に DNS 名が必要です。 作成時に DNS 名を指定していない場合は、Azure portal で行うことができます。

  :::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Azure-SSIS IR":::

- これらと仮想ネットワークは、同じサブスクリプションと同じリージョンにある必要があります。

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>カスタム DNS サーバーを構成する 

仮想ネットワーク内の独自の DNS サーバーを使用してプライベート ホスト名を解決する場合は、グローバルな Azure ホスト名 (たとえば、`<your storage account>.blob.core.windows` という名前の Azure Blob Storage) も解決できることを確認します。

未解決の DNS 要求を Azure 再帰リゾルバーの IP アドレス (*168.63.129.16*) に転送するよう、独自の DNS サーバーを構成することをお勧めします。

詳しくは、[DNS サーバーの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)に関するセクションをご覧ください。

> [!NOTE]
> プライベート ホスト名には完全修飾ドメイン名 (FQDN) を使用してください (たとえば、`<your_private_server>` ではなく `<your_private_server>.contoso.com` を使用します)。 または、Azure-SSIS IR 上で標準のカスタム セットアップを使用して、独自の DNS サフィックス (たとえば `contoso.com`) を非修飾の 1 つのラベル ドメイン名に自動的に追加することで、DNS クエリで使用する前に FQDN に変換することもできます。「[標準カスタム セットアップのサンプル](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples)」セクションをご覧ください。 

## <a name="configure-an-nsg"></a><a name="nsg"></a>NSG を構成する

Azure-SSIS IR によって参加しているサブネットで NSG を使用する場合は、次のインバウンドとアウトバウンドのトラフィックを許可します。

| Direction | トランスポート プロトコル | source | ソース ポート | 到着地 | 宛先ポート | 説明 | 
|-----------|--------------------|--------|--------------|-------------|-------------------|----------| 
| 受信 | TCP | *BatchNodeManagement* | * | *VirtualNetwork* | *29876、29877* (SSIS IR を Azure Resource Manager 仮想ネットワークに参加させる場合)<br/><br/>*10100、20100、30100* (SSIS IR をクラシック仮想ネットワークに参加させる場合)| Data Factory サービスはこれらのポートを使って、仮想ネットワークの Azure-SSIS IR のノードと通信します。<br/><br/>サブネット上に NSG を作成するかどうかに関わらず、Data Factory によって常に、Azure-SSIS IR がホストされている仮想マシンにアタッチされたネットワーク インターフェイス カード (NIC) で NSG が構成されます。<br/><br/>指定したポートでの Data Factory の IP アドレスからのインバウンド トラフィックのみが、NIC レベルの NSG によって許可されます。<br/><br/>サブネット レベルでインターネット トラフィックに対してこれらのポートを開いている場合でも、Data Factory の IP アドレスではない IP アドレスからのトラフィックは NIC レベルでまだブロックされます。 | 
| 受信 | TCP | *CorpNetSaw* | * | *VirtualNetwork* | *3389* | (省略可能) Microsoft サポート エンジニアから高度なトラブルシューティングのためにポート *3389* を開くように依頼された場合にのみ必要であり、トラブルシューティングの直後に閉じることができます。<br/><br/>*CorpNetSaw* サービス タグを使用すると、Microsoft 企業ネットワーク内のセキュリティで保護されたアクセス ワークステーション (SAW) マシンだけが、リモート デスクトップ プロトコル (RDP) 経由で Azure-SSIS IR にアクセスできます。<br/><br/>このサービス タグは Azure portal から選択することはできず、Azure PowerShell または CLI 経由でのみ使用できます。<br/><br/>NIC レベルの NSG では、ポート *3389* は既定で開かれますが、サブネット レベルの NSG で制御することができます。一方、それでのアウトバウンド トラフィックは、Windows ファイアウォール規則を使用して、Azure-SSIS IR ノードで既定で禁止されます。 | 

| Direction | トランスポート プロトコル | source | ソース ポート | 到着地 | 宛先ポート | 説明 |
|-----------|--------------------|--------|--------------|-------------|-------------------|----------|
| 送信 | TCP | *VirtualNetwork* | * | *AzureCloud* | *443* | Azure-SSIS IR で、Azure Storage や Azure Event Hubs などの Azure サービスにアクセスするために必要です。 | 
| 送信 | TCP | *VirtualNetwork* | * | *Internet* | *80* | (省略可能) Azure-SSIS IR により、このポートを使用して、インターネットから証明書失効リスト (CRL) がダウンロードされます。<br/><br/>このトラフィックをブロックすると、Azure-SSIS IR を起動するときにパフォーマンスが低下し、証明書を使用するときに CRL を確認する機能が失われる可能性があります。これはセキュリティの観点から推奨されません。<br/><br/>特定の FQDN に宛先を絞り込む場合は、後の「**UDR を構成する**」セクションをご覧ください | 
| 送信 | TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433、11000 ～ 11999* | (省略可能) Azure SQL Database サーバーまたは Managed Instance を使用して SSIS カタログ (SSISDB) をホストする場合にのみ必要です。<br/><br/>Azure SQL Database サーバーまたは Managed Instance がパブリック エンドポイントまたは仮想ネットワーク サービス エンドポイントで構成されている場合は、宛先として *Sql* サービス タグを使用します。<br/><br/>Azure SQL Database サーバーまたは Managed Instance がプライベート エンドポイントで構成されている場合は、宛先として *VirtualNetwork* サービス タグを使用します。<br/><br/>サーバー接続ポリシーが "*リダイレクト*" ではなく "*プロキシ*" に設定されている場合は、ポート *1433* のみが必要です。 | 
| 送信 | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | (省略可能) Azure Storage BLOB コンテナーを使用して標準のカスタム セットアップ スクリプトやファイルを格納する場合にのみ必要です。<br/><br/>Azure Storage がパブリック エンドポイントまたは仮想ネットワーク サービス エンドポイントで構成されている場合は、宛先として *Storage*  サービス タグを使用します。<br/><br/>Azure Storage がプライベート エンドポイントで構成されている場合は、宛先として *VirtualNetwork* サービス タグを使用します。 | 
| 送信 | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | (省略可能) Azure Files にアクセスする必要がある場合にのみ必要です。<br/><br/>Azure Storage がパブリック エンドポイントまたは仮想ネットワーク サービス エンドポイントで構成されている場合は、宛先として *Storage*  サービス タグを使用します。<br/><br/>Azure Storage がプライベート エンドポイントで構成されている場合は、宛先として *VirtualNetwork* サービス タグを使用します。 | 

## <a name="configure-udrs"></a><a name="udr"></a>UDR を構成する

Azure-SSIS IR からのアウトバウンド トラフィックを監査または検査する場合は、[ユーザー定義ルート(UDR)](../virtual-network/virtual-networks-udr-overview.md)を使用して、仮想ネットワークに境界ゲートウェイ プロトコル (BGP) ルート *0.0.0.0/0* をアドバタイズする [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 強制トンネリングを介してオンプレミスのファイアウォール アプライアンスに、またはファイアウォールとして構成されたネットワーク仮想アプライアンス (NVA) に、または [Azure Firewall](../firewall/overview.md) サービスに、リダイレクトすることができます。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="Azure-SSIS IR の NVA シナリオ":::

それを機能させるには、以下のことを確認する必要があります。

- Azure Batch 管理サービスと Azure-SSIS IR の間のトラフィックを、ファイアウォール アプライアンスまたはサービスにルーティングすることはできません。

- ファイアウォール アプライアンスまたはサービスでは、Azure-SSIS IR に必要なアウトバウンド トラフィックを許可する必要があります。

Azure Batch 管理サービスと Azure-SSIS IR の間のトラフィックがファイアウォール アプライアンスまたはサービスにルーティングされた場合、非対称ルーティングが原因で破損します。 このトラフィックには、入ってきたルートと同じルートを通って出て行くことができるように、UDR を定義する必要があります。 ネクスト ホップの種類を "*インターネット*" として Azure Batch 管理サービス と Azure-SSIS IR の間のトラフィックをルーティングするように、UDR を構成できます。

たとえば、Azure-SSIS IR が "*英国南部*" にあり、Azure Firewall を使ってアウトバウンド トラフィックを検査する場合は、まず、[サービス タグ IP 範囲のダウンロード リンク](https://www.microsoft.com/download/details.aspx?id=56519)または [Service Tag Discovery API](../virtual-network/service-tags-overview.md#service-tags-on-premises) から、*BatchNodeManagement.UKSouth* サービス タグの IP 範囲を取得できます。 その後、ネクスト ホップの種類が "*インターネット*" である関連 IP 範囲ルートと、ネクスト ホップの種類が "*仮想アプライアンス*" である *0.0.0.0/0*  ルートに対して、以下の UDR を構成できます。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Azure Batch UDR の設定":::

> [!NOTE]
> この方法では、Azure-SSIS IR が壊れないように、定期的に関連 IP 範囲をチェックし、新しいものに対する UDR を追加する必要があるため、追加のメンテナンス コストが発生します。 関連するサービス タグに新しい IP 範囲が出現した場合、有効になるまでにさらに 1 ヶ月かかるので、毎月調べることをお勧めします。 

次の PowerShell スクリプトを実行して、Azure Batch 管理サービス用の UDR を追加できます。

```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your route table]"
$RouteTableResourceName = "[resource name of your route table]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start adding UDRs to your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch Azure service tag, please confirm that your location is valid."
}
```

上の「[NSG を構成する](#nsg)」セクションのガイダンスに従って、同様のルールをファイアウォール アプライアンスまたはサービスに実装し、Azure-SSIS IR からのアウトバウンド トラフィックを許可する必要があります。

- Azure Firewall を使用する場合:
  - 宛先として *AzureCloud* サービス タグが指定されたアウトバウンド TCP トラフィック用に、ポート *443* を開く必要があります。

  - Azure SQL Database サーバーまたは Managed Instance を使用して SSISDB をホストする場合は、宛先として *Sql/VirtualNetwork* サービス タグが指定されたアウトバウンド TCP トラフィック用にポート *1433、11000 - 11999* を開く必要があります。

  - Azure Storage BLOB コンテナーを使用して、標準のカスタム セットアップのスクリプトまたはファイルを格納する場合は、宛先として *Storage/VirtualNetwork* サービス タグが指定されたアウトバウンド TCP トラフィック用にポート *443* を開く必要があります。

  - Azure Files にアクセスする必要がある場合は、宛先として *Storage/VirtualNetwork* サービス タグが指定されたアウトバウンド TCP トラフィック用にポート *445* を開く必要があります。

- 他のファイアウォール アプライアンスまたはサービスを使用する場合:
  - 宛先として *0.0.0.0/0* または次の Azure 環境固有の FQDN が指定されたアウトバウンド TCP トラフィック用に、ポート *443* を開く必要があります。

    | Azure 環境 | FQDN |
    |-------------------|-------|
    | <b>Azure Public</b> | <ul><li><b>Azure Data Factory (管理)</b><ul><li>_\*.frontend.clouddatahub.net_</li></ul></li><li><b>Azure Storage (管理)</b><ul><li>_\*.blob.core.windows.net_</li><li>_\*.table.core.windows.net_</li></ul></li><li><b>Azure Container Registry (カスタム セットアップ)</b><ul><li>_\*.azurecr.io_</li></ul></li><li><b>Event Hubs (ログ記録)</b><ul><li>_\*.servicebus.windows.net_</li></ul></li><li><b>Microsoft ログ サービス (内部使用)</b><ul><li>_gcs.prod.monitoring.core.windows.net_</li><li>_prod.warmpath.msftcloudes.com_</li><li>_azurewatsonanalysis-prod.core.windows.net_</li></ul></li></ul> |
    | <b>Azure Government</b> | <ul><li><b>Azure Data Factory (管理)</b><ul><li>_\*.frontend.datamovement.azure.us_</li></ul></li><li><b>Azure Storage (管理)</b><ul><li>_\*.blob.core.usgovcloudapi.net_</li><li>_\*.table.core.usgovcloudapi.net_</li></ul></li><li><b>Azure Container Registry (カスタム セットアップ)</b><ul><li>_\*.azurecr.us_</li></ul></li><li><b>Event Hubs (ログ記録)</b><ul><li>_\*.servicebus.usgovcloudapi.net_</li></ul></li><li><b>Microsoft ログ サービス (内部使用)</b><ul><li>_fairfax.warmpath.usgovcloudapi.net_</li><li>_azurewatsonanalysis.usgovcloudapp.net_</li></ul></li></ul> |
    | <b>Azure China 21Vianet</b> | <ul><li><b>Azure Data Factory (管理)</b><ul><li>_\*.frontend.datamovement.azure.cn_</li></ul></li><li><b>Azure Storage (管理)</b><ul><li>_\*.blob.core.chinacloudapi.cn_</li><li>_\*.table.core.chinacloudapi.cn_</li></ul></li><li><b>Azure Container Registry (カスタム セットアップ)</b><ul><li>_\*.azurecr.cn_</li></ul></li><li><b>Event Hubs (ログ記録)</b><ul><li>_\*.servicebus.chinacloudapi.cn_</li></ul></li><li><b>Microsoft ログ サービス (内部使用)</b><ul><li>_mooncake.warmpath.chinacloudapi.cn_</li><li>_azurewatsonanalysis.chinacloudapp.cn_</li></ul></li></ul> |

  - Azure SQL Database サーバーまたは Managed Instance を使用して SSISDB をホストする場合は、宛先として *0.0.0.0/0* または Azure SQL Database サーバーや Managed Instance の FQDN が指定されたアウトバウンド TCP トラフィック用に、ポート *1433、11000 - 11999* を開く必要があります。

  - Azure Storage BLOB コンテナーを使用して、標準のカスタム セットアップのスクリプトまたはファイルを格納する場合は、宛先として *0.0.0.0/0* または Azure Blob Storage の FQDN が指定されたアウトバウンド TCP トラフィック用に、ポート *443* を開く必要があります。

  - Azure Files にアクセスする必要がある場合は、宛先として *0.0.0.0/0* または Azure Files の FQDN が指定されたアウトバウンド TCP トラフィック用に、ポート *445* を開く必要があります。

- サブネットで *Microsoft.Storage*/*Microsoft.ContainerRegistry*/*Microsoft.EventHub*/*Microsoft.Sql* リソースをそれぞれ有効にして、Azure Storage、Container Registry、Event Hubs、または SQL の仮想ネットワーク サービス エンドポイントを構成した場合、Azure-SSIS IR と、同じリージョンまたはペアになったリージョン内にあるこれらのサービスとの間のすべてのトラフィックは、ファイアウォール アプライアンスまたはサービスではなく、Azure バックボーン ネットワークにルーティングされます。

- 宛先として次の証明書失効リスト (CRL) ダウンロード サイトが指定されたアウトバウンド TCP トラフィック用に、ポート *80* を開く必要があります。

  - *crl.microsoft.com:80*
  - *mscrl.microsoft.com:80*
  - *crl3.digicert.com:80*
  - *crl4.digicert.com:80*
  - *ocsp.digicert.com:80*
  - *cacerts.digicert.com:80*
  
  異なる CRL で証明書を使用する場合は、それらのダウンロード サイトも宛先として追加する必要があります。 詳しくは、[証明書失効リスト](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)に関する記事をご覧ください。

  このトラフィックをブロックすると、Azure-SSIS IR を起動するときにパフォーマンスが低下し、証明書を使用するときに CRL を確認する機能が失われる可能性があります。これはセキュリティの観点から推奨されません。

Azure-SSIS IR からのアウトバウンド トラフィックを監査または検査する必要がない場合は、UDR を使用し、ネクスト ホップの種類を "*インターネット*" として指定してすべてのトラフィックを強制できます。

- Azure ExpressRoute を使用する場合は、ネクスト ホップの種類を "*インターネット*" として指定して、サブネット内の *0.0.0.0/0* ルート に対する UDR を構成できます。 

- NVA を使用する場合は、サブネット内の *0.0.0.0/0* ルートに対する既存の UDR を変更して、ネクスト ホップの種類を "*仮想アプライアンス*" から "*インターネット*" に切り替えることができます。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="ルートを追加する":::

> [!NOTE]
> ネクスト ホップの種類を "*インターネット*" として URS を構成しても、すべてのトラフィックがインターネットを経由するわけではありません。 宛先アドレスが Azure サービスのいずれかに属している限り、そのアドレスへのすべてのトラフィックは、Azure によってインターネットではなく Azure バックボーン ネットワーク経由でルーティングされます。

## <a name="configure-the-relevant-resource-group"></a><a name="rg"></a>関連するリソース グループを構成する

標準の仮想ネットワーク インジェクションを有効にするには、Azure-SSIS IR で仮想ネットワークと同じリソース グループ内に特定のネットワーク リソースを作成する必要があります。 これらのリソースには次が含まれます。

- Azure ロード バランサー。 _\<Guid\>-azurebatch-cloudserviceloadbalancer_ という名前で使用される。
- Azure パブリック IP アドレス。 _\<Guid\>-azurebatch-cloudservicepublicip_ という名前で使用される。
- NSG。 _\<Guid\>-azurebatch-cloudservicenetworksecuritygroup_ という名前で使用される。 

> [!NOTE]
> これで、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用できるようになりました。 このシナリオでは、仮想ネットワークではなく、静的パブリック IP アドレスと同じリソース グループに、Azure ロード バランサーと NSG を作成します。

これらのリソースは、Azure-SSIS IR の起動時に作成されます。 これらは、Azure-SSIS IR の停止時に削除されます。 Azure-SSIS IR 用に独自の静的パブリック IP アドレスを使用する場合は、Azure-SSIS IR の停止時に削除されません。 Azure-SSIS IR の停止がブロックされないように、他の目的にこれらのリソースを再利用しないでください。

仮想ネットワークまたは静的パブリック IP アドレスが属しているリソース グループまたはサブスクリプションに、リソース ロックがないことを確認します。 読み取り専用または削除ロックを構成した場合、Azure-SSIS IR の開始と停止が失敗するか、応答しなくなります。

仮想ネットワークまたは静的パブリック IP アドレスが属しているリソース グループまたはサブスクリプションで、次のリソースが作成されないようにする Azure Policy の割り当てがないことを確認します。 

- *Microsoft.Network/LoadBalancers* 
- *Microsoft.Network/NetworkSecurityGroups* 
- *Microsoft.Network/PublicIPAddresses* 

サブスクリプションのリソース クォータが、これらのリソースに対して十分であることを確認します。 具体的には、Azure-SSIS IR を定期的にアップグレードするときに余分なリソースが使用されるため、仮想ネットワーク内に作成された Azure-SSIS IR ごとに、2 倍の数のこれらのリソースを確保する必要があります。

## <a name="faq"></a><a name="faq"></a>FAQ

- 受信接続用に Azure-SSIS IR で公開されるパブリック IP アドレスを保護するにはどうすればよいですか。 パブリック IP アドレスを削除することはできますか。
 
  現時点では、Azure-SSIS IR が仮想ネットワークに参加すると、パブリック IP アドレスが自動的に作成されます。 NIC レベルの NSG を使用して、Azure-SSIS IR への受信接続のみを Azure Batch 管理サービスに許可します。 受信保護のために、サブネットレベルの NSG を指定することもできます。

  パブリック IP アドレスが公開されないようにするには、仮想ネットワークに Azure-SSIS IR を参加させるのではなく、[Azure-SSIS IR のプロキシとしてセルフホステッド IR を構成する](self-hosted-integration-runtime-proxy-ssis.md)ことを検討してください。
 
- データ ソースのファイアウォールの許可リストに Azure-SSIS IR のパブリック IP アドレスを追加できますか。

  これで、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用できるようになりました。 この場合、データ ソースのファイアウォールの許可リストに IP アドレスを追加することができます。 または、シナリオに応じて、Azure-SSIS IR からのデータ アクセスをセキュリティで保護するために、以下の他のオプションを検討することができます。

  - データ ソースがオンプレミスの場合は、仮想ネットワークをオンプレミス ネットワークに接続し、仮想ネットワーク サブネットに Azure-SSIS IR を参加させた後、そのサブネットのプライベート IP アドレス範囲を、データ ソースのファイアウォールの許可リストに追加することができます。

  - データ ソースが、仮想ネットワーク サービス エンドポイントをサポートする Azure サービスである場合は、仮想ネットワーク サブネット上で仮想ネットワーク サービス エンドポイントを構成し、そのサブネットに Azure-SSIS IR を参加させることができます。 その後、そのサブネットの仮想ネットワーク規則を、データ ソースのファイアウォールに追加できます。

  - データ ソースが Azure 以外のクラウド サービスの場合は、UDR を使用して、Azure-SSIS IR からのアウトバウンド トラフィックを、NVA または Azure Firewall を介して静的パブリック IP アドレスにルーティングできます。 その後、NVA または Azure Firewall の静的パブリック IP アドレスを、データ ソースのファイアウォールの許可リストに追加できます。

  - 上記のいずれのオプションもニーズを満たしていない場合は、[Azure-SSIS IR のプロキシとしてのセルフホステッド IR の構成](self-hosted-integration-runtime-proxy-ssis.md)を検討してください。 その後、セルフホステッド IR をホストするコンピューターの静的パブリック IP アドレスを、データ ソースのファイアウォールの許可リストに追加できます。

- Azure-SSIS IR 用に独自のものを使用する場合に、2 つの静的パブリック アドレスを指定する必要があるのはなぜですか。

  Azure-SSIS IR は、定期的に自動更新されます。 アップグレード中に新しいノードが作成され、古いものは削除されます。 しかし、ダウンタイムを回避するために、古いノードは新しいものが準備されるまで削除されません。 そのため、古いノードで使用されている最初の静的パブリック IP アドレスをすぐに解放することはできません。新しいノードを作成するには、2 番目の静的パブリック IP アドレスが必要です。

- Azure-SSIS IR 用に独自の静的パブリック IP アドレスを使用することにしましたが、それでもデータ ソースにアクセスできないのはなぜですか。

  2 つの静的パブリック IP アドレスが、両方ともデータ ソースのファイアウォールの許可リストに追加されていることを確認してください。 Azure-SSIS IR がアップグレードされるたびに、その静的パブリック IP アドレスが指定された 2 つの間で切り替わります。 そのうちの 1 つのみを許可リストに追加した場合、Azure-SSIS IR のデータ アクセスはそのアップグレード後に切断されます。

  データ ソースが Azure サービスの場合は、仮想ネットワーク サービス エンドポイントでそれを構成したかどうかを確認してください。 該当する場合、Azure-SSIS IR からデータ ソースへのトラフィックは、Azure サービスによって管理されているプライベート IP アドレスを使用するように切り替わります。データ ソースのファイアウォールの許可リストに独自の静的パブリック IP アドレスを追加しても、反映されません。

## <a name="next-steps"></a>次の手順

- [ADF UI を使用して仮想ネットワークに Azure SSIS IR を参加させる](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell を使用して仮想ネットワークに Azure-SSIS IR を参加させる](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 

- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 ここでは、SSISDB をホストするために Azure SQL Database サーバーを使用しています。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイント、IP ファイアウォール規則、プライベート エンドポイントで構成された Azure SQL Database サーバー、または仮想ネットワークに参加する Azure SQL Managed Instance を使用して SSISDB をホストする手順について説明しています。 Azure-SSIS IR を仮想ネットワークに参加させる方法について説明します。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
