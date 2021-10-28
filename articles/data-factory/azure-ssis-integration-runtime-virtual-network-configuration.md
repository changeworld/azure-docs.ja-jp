---
title: Azure-SSIS 統合ランタイム用に Azure 仮想ネットワークを構成する
description: Azure-SSIS 統合ランタイム用に Azure 仮想ネットワークを構成する方法を説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b4274144e8437bd47adad0854fec5c3ed9550ffb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229410"
---
# <a name="virtual-network-configuration-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイム用の仮想ネットワーク構成

次の要件を満たすように仮想ネットワークを設定します。 

- `Microsoft.Batch` が、Azure-SSIS IR をホストする仮想ネットワーク サブネットのサブスクリプションの登録済みプロバイダーであることを確認します。 従来の仮想ネットワークを使用している場合は、その仮想ネットワークの従来の仮想マシン共同作成者ロールにも `MicrosoftAzureBatch` を参加させます。 

- 必要なアクセス許可を持っていることを確認します。 詳細については、「[アクセス許可を設定する](#perms)」を参照してください。

- Azure-SSIS IR をホストするための適切なサブネットを選択します。 詳細については、「[サブネットを選択する](#subnet)」を参照してください。 

- Azure-SSIS IR 用に独自のパブリック IP アドレスを使用する場合は、「[静的パブリック IP アドレスの選択](#publicIP)」を参照してください

- 仮想ネットワーク上で独自のドメイン ネーム システム (DNS) サーバーを使用する場合は、「[DNS サーバーを設定する](#dns_server)」を参照してください。 

- サブネット上でネットワーク セキュリティ グループ (NSG) を使用する場合は、「[NSG を設定する](#nsg)」を参照してください。 

- Azure ExpressRoute またはユーザー定義ルート (UDR) を使用する場合は、「[Azure ExpressRoute または UDR を使用する](#route)」を参照してください。 

- 仮想ネットワークのリソース グループ (独自のパブリック IP アドレスを使用する場合は、パブリック IP アドレスのリソース グループ) が特定の Azure ネットワーク リソースを作成および削除できることを確認します。 詳細については、「[リソース グループを設定する](#resource-group)」を参照してください。 

- [Azure-SSIS IR のカスタム セットアップ](./how-to-configure-azure-ssis-ir-custom-setup.md)に関する記事で説明されているように Azure-SSIS IR をカスタマイズすると、そのノードを管理する内部プロセスでは、事前に定義された 172.16.0.0 ～ 172.31.255.255 の範囲からプライベート IP アドレスを消費します。 結果として、仮想またはオンプレミスのネットワークのプライベート IP アドレスの範囲が、この範囲と競合しないようにしてください。

次の図は、Azure-SSIS IR に必要な接続を示しています。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Azure-SSIS IR に必要な接続を示している図。":::

## <a name="set-up-permissions"></a><a name="perms"></a> アクセス許可を設定する

Azure-SSIS IR を作成するユーザーは、次のアクセス許可を持っている必要があります。

- SSIS IR を Azure Resource Manager 仮想ネットワークに参加させようとしている場合は、次の 2 つのオプションがあります。

  - 組み込みのネットワーク共同作成者ロールを使用します。 このロールには、必要なスコープよりずっと大きなスコープを持つ _Microsoft.Network/\*_ アクセス許可が備わっています。

  - 必要な _Microsoft.Network/virtualNetworks/\*/join/action_ アクセス許可のみを含むカスタム ロールを作成してください。 また、Azure-SSIS IR を Azure Resource Manager 仮想ネットワークに参加させながら、独自のパブリック IP アドレスを使用する場合は、_Microsoft.Network/publicIPAddresses/*/join/action_ アクセス許可もロールに含めてください。

- SSIS IR を従来の仮想ネットワークに参加させる場合は、組み込みの従来の仮想マシン共同作成者ロールを使用することをお勧めします。 そうしない場合は、仮想ネットワークに参加するためのアクセス許可を含むカスタム ロールを定義する必要があります。

## <a name="select-the-subnet"></a><a name="subnet"></a> サブネットを選択する

サブネットを選択するとき: 

- Azure-SSIS IR をデプロイするには、GatewaySubnet を選択しないでください。 これは仮想ネットワーク ゲートウェイ専用です。 

- Azure-SSIS IR が使用するための利用可能なアドレス領域が選択したサブネットに十分にあることを確認してください。 IR ノード番号の 2 倍以上の使用可能な IP アドレスを残しておきます。 Azure は、各サブネット内で一部の IP アドレスを予約します。 これらのアドレスは使用できません。 サブネットの最初と最後の IP アドレスはプロトコル準拠用に予約され、3 つ以上のアドレスが Azure サービスに使用されます。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- (SQL Database SQL Managed Instance、App Service など) 他の Azure サービスによって排他的に専有されているサブネットを使用しないでください。 

## <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>静的パブリック IP アドレスの選択

Azure-SSIS IR を仮想ネットワークに参加させながら、独自の静的パブリック IP アドレスを使用する場合は、以下の要件を満たしていることを確認してください。

- まだ他の Azure リソースに関連付けられていない 2 つの未使用のもののみを指定する必要があります。 余分なものは、定期的に Azure-SSIS IR をアップグレードするときに使用されます。 アクティブな Azure-SSIS IR 間で 1 つのパブリック IP アドレスを共有することはできません。

- これらの両方が、標準の種類の静的なものである必要があります。 詳細については、[パブリック IP アドレスの SKU](../virtual-network/ip-services/public-ip-addresses.md#sku) に関するページを参照してください。

- これらの両方に DNS 名が必要です。 作成時に DNS 名を指定していない場合は、Azure portal で行うことができます。

:::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Azure-SSIS IR":::

- これらと仮想ネットワークは、同じサブスクリプションと同じリージョンにある必要があります。

## <a name="set-up-the-dns-server"></a><a name="dns_server"></a> DNS サーバーを設定する 
プライベート ホスト名を解決するために Azure-SSIS IR が参加している仮想ネットワークで独自の DNS サーバーを使用する必要がある場合は、グローバルな Azure ホスト名 (たとえば、`<your storage account>.blob.core.windows.net` という名前の Azure Storage Blob) を解決できることも確認してください。 

推奨される方法の 1 つを以下に示します。 

-   要求を Azure DNS に転送するようにカスタム DNS を構成します。 独自の DNS サーバー上で、未解決の DNS レコードを Azure の再帰的リゾルバーの IP アドレス (168.63.129.16) に転送することができます。 

詳細については、「[独自の DNS サーバーを使用する名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。 

> [!NOTE]
> プライベート ホスト名には完全修飾ドメイン名 (FQDN) を使用してください (たとえば、`<your_private_server>` ではなく `<your_private_server>.contoso.com` を使用します)。 または、Azure-SSIS IR 上で標準のカスタム セットアップを使用して、独自の DNS サフィックス (たとえば `contoso.com`) を非修飾の 1 つのラベル ドメイン名に自動的に追加することで、DNS クエリで使用する前に FQDN に変換することもできます。[標準のカスタム セットアップのサンプル](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples)に関するページを参照してください。 

## <a name="set-up-an-nsg"></a><a name="nsg"></a> NSG を設定する
Azure-SSIS IR によって使用されるサブネットに NSG を実装する必要がある場合は、次のポートを経由する受信および送信トラフィックを許可します。 

-   **Azure-SSIS IR の受信要件**

| Direction | トランスポート プロトコル | source | 発信元ポート範囲 | 到着地 | Destination port range | 説明 |
|---|---|---|---|---|---|---|
| 受信 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877 (IR を Resource Manager 仮想ネットワークに参加させる場合) <br/><br/>10100、20100、30100 (IR をクラシック仮想ネットワークに参加させる場合)| Data Factory サービスはこれらのポートを使って、仮想ネットワークの Azure-SSIS IR のノードと通信します。 <br/><br/> サブネットレベルの NSG を作成するかどうかにかかわらず、Azure-SSIS IR をホストする仮想マシンにアタッチされているネットワーク インターフェイス カード (NIC) のレベルで、Data Factory は NSG を常に構成します。 Data Factory の IP アドレスから指定したポートで受信したトラフィックのみが、その NIC レベルの NSG によって許可されます。 サブネット レベルでインターネット トラフィックに対してこれらのポートを開いている場合でも、Data Factory の IP アドレスではない IP アドレスからのトラフィックは NIC レベルでブロックされます。 |
| 受信 | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (省略可能) この規則は、Microsoft サポーターがお客様に対して、高度なトラブルシューティングのために開くように依頼した場合にのみ必要になり、トラブルシューティングの直後に閉じることができます。 **CorpNetSaw** サービス タグでは、Microsoft 企業ネットワーク上のセキュリティで保護されたアクセス ワークステーションでのみ、リモート デスクトップの使用が許可されます。 このサービス タグはポータルから選択することはできず、Azure PowerShell または Azure CLI 経由でのみ使用できます。 <br/><br/> NIC レベルの NSG では、ポート 3389 が既定で開かれ、サブネット レベルの NSG ではポート 3389 を制御できます。一方、保護のために各 IR ノードの Windows ファイアウォール規則では既定で、Azure-SSIS IR によってポート 3389 の送信が禁止されています。 |
||||||||

-   **Azure-SSIS IR の送信要件**

| Direction | トランスポート プロトコル | source | 発信元ポート範囲 | 到着地 | Destination port range | 説明 |
|---|---|---|---|---|---|---|
| 送信 | TCP | VirtualNetwork | * | AzureCloud | 443 | 仮想ネットワークの Azure-SSIS IR のノードはこのポートを使って、Azure Storage や Azure Event Hubs などの Azure サービスにアクセスします。 |
| 送信 | TCP | VirtualNetwork | * | インターネット | 80 | (省略可能) 仮想ネットワーク内の Azure-SSIS IR のノードでは、このポートを使用して、インターネットから証明書失効リストをダウンロードします。 このトラフィックをブロックすると、IR の開始時にパフォーマンスが低下し、証明書の使用状況について証明書失効リストを確認する機能が失われる可能性があります。 送信先を特定の FQDN にさらに絞り込む場合は、「**Azure ExpressRoute または UDR を使用する**」のセクションを参照してください|
| 送信 | TCP | VirtualNetwork | * | Sql | 1433、11000 ～ 11999 | (省略可能) この規則は、仮想ネットワーク内の Azure-SSIS IR のノードから、サーバーによってホストされている SSISDB にアクセスする場合にのみ必要です。 サーバー接続ポリシーが **[リダイレクト]** ではなく **[プロキシ]** に設定されている場合、ポート 1433 のみが必要です。 <br/><br/> この送信セキュリティ規則は、プライベート エンドポイントで構成された SQL Database または仮想ネットワーク内の SQL Managed Instance によってホストされている SSISDB には適用できません。 |
| 送信 | TCP | VirtualNetwork | * | VirtualNetwork | 1433、11000 ～ 11999 | (省略可能) この規則は、仮想ネットワーク内の Azure-SSIS IR のノードから、プライベート エンドポイントで構成された SQL Database または仮想ネットワーク内の SQL Managed Instance によってホストされている SSISDB にアクセスする場合にのみ必要です。 サーバー接続ポリシーが **[リダイレクト]** ではなく **[プロキシ]** に設定されている場合、ポート 1433 のみが必要です。 |
| 送信 | TCP | VirtualNetwork | * | ストレージ | 445 | (省略可能) この規則は、Azure Files に格納されている SSIS パッケージを実行する場合にのみ必要です。 |
||||||||

## <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Azure ExpressRoute または UDR を使用する
Azure-SSIS IR からの送信トラフィックを検査する場合は、Azure-SSIS IR から開始されたトラフィックを、[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 強制トンネリングを使用して (仮想ネットワークへの、BGP ルート、0.0.0.0/0、をアドバタイズする) オンプレミス ファイアウォール アプライアンスに、あるいは [UDR](../virtual-network/virtual-networks-udr-overview.md) 経由で [Azure Firewall](../firewall/index.yml) またはファイアウォールとしてのネットワーク仮想アプライアンス (NVA) にルーティングできます。 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="Azure-SSIS IR の NVA シナリオ":::

シナリオ全体を機能させるには、以下の作業を行う必要があります
   -   Azure Batch 管理サービスと Azure-SSIS IR 間の受信トラフィックを、ファイアウォール アプライアンス経由でルーティングすることはできません。
   -   ファイアウォール アプライアンスでは、Azure-SSIS IR に必要な送信トラフィックを許可します。

Azure Batch 管理サービスと Azure-SSIS IR 間の受信トラフィックを、ファイアウォール アプライアンスにルーティングすることはできません。これを行うと、非対称ルーティングの問題によってトラフィックが切断されます。 受信トラフィックに対してルートを定義して、トラフィックが受信されたときと同じ方法で応答できるようにする必要があります。 Azure Batch 管理サービスと、次ホップの種類が **[インターネット]** である Azure-SSIS IR 間でトラフィックをルーティングする特定の UDR を定義できます。

たとえば、Azure-SSIS IR が `UK South` にあり、Azure Firewall 経由の送信トラフィックを検査する場合は、まず、[サービス タグの IP 範囲のダウンロード リンク](https://www.microsoft.com/download/details.aspx?id=56519)から、または [Service Tag Discovery API](../virtual-network/service-tags-overview.md#service-tags-on-premises) を通じて、サービス タグ `BatchNodeManagement.UKSouth` の IP 範囲一覧を取得します。 その後、次ホップの種類が **[インターネット]** である関連する IP 範囲ルートの以下の UDR を、次ホップの種類が **[仮想アプライアンス]** である 0.0.0.0/0 ルートと共に適用します。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Azure Batch UDR の設定":::

> [!NOTE]
> この方法では、追加のメンテナンス コストが発生します。 Azure-SSIS IR が中断されないように、IP 範囲を定期的に確認し、UDR に新しい IP 範囲を追加します。 IP 範囲は月単位で確認することをお勧めします。これは、新しい IP がサービス タグに示された場合、IP が有効になるまでさらに 1 か月かかるためです。 

UDR 規則のセットアップを簡単にするために、次の PowerShell スクリプトを実行して Azure Batch 管理サービスの UDR 規則を追加できます。
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

ファイアウォール アプライアンスで送信トラフィックを許可するには、NSG アウトバウンド規則の要件と同じように、以下のポートへの送信を許可する必要があります。
-   送信先が Azure クラウド サービスであるポート 443。

    Azure Firewall を使用する場合は、AzureCloud サービス タグでネットワーク規則を指定できます。 他の種類のファイアウォールについては、単にポート 443 の宛先をすべて許可するか、Azure 環境の種類に基づいて以下の FQDN を許可することができます。

    | Azure 環境 | エンドポイント                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li><b>Azure Data Factory (管理)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (管理)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure Container Registry (カスタム セットアップ)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>イベント ハブ (ログ)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Microsoft ログ サービス (内部使用)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (管理)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (管理)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (カスタム セットアップ)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>イベント ハブ (ログ)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft ログ サービス (内部使用)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (管理)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (管理)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (カスタム セットアップ)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>イベント ハブ (ログ)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft ログ サービス (内部使用)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Azure Storage、Azure Container Registry、およびイベント ハブの FQDN については、仮想ネットワークの次のサービス エンドポイントを有効にして、これらのエンドポイントへのネットワーク トラフィックが、ファイアウォール アプライアンスにルーティングされるのではなく、Azure バックボーン ネットワークを経由するようにすることもできます。
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   送信先が CRL のダウンロード サイトであるポート 80。

    Azure-SSIS IR の管理目的で、証明書の CRL (証明書失効リスト) ダウンロード サイトとして使用される以下の FQDN を許可します。
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    CRL が異なる証明書を使用する場合は、それらも含めることをお勧めします。 詳細については、こちらの「[証明書失効リスト](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)」を参照してください。

    このトラフィックを許可しない場合、Azure-SSIS IR の開始時にパフォーマンスが低下し、セキュリティの観点から推奨されていない証明書の使用状況について証明書失効リストを確認する機能が失われる可能性があります。

-   送信先が Azure SQL Database であるポート 1433、11000 から 11999 (仮想ネットワーク内の Azure-SSIS IR のノードから、サーバーによってホストされている SSISDB にアクセスする場合にのみ必要)。

    Azure Firewall を使用する場合は、Azure SQL サービス タグでネットワーク規則を指定できます。それ以外の場合は、ファイアウォール アプライアンスの特定の Azure SQL URL として送信先を許可することができます。

-   送信先が Azure Storage であるポート 445 (Azure Files に格納されている SSIS パッケージを実行する場合にのみ必要)。

    Azure Firewall を使用する場合は、Storage サービス タグでネットワーク規則を指定できます。それ以外の場合は、ファイアウォール アプライアンスの特定の Azure ファイル共有 URL として送信先を許可することができます。

> [!NOTE]
> Azure SQL と Storage の場合、サブネット上で仮想ネットワーク サービス エンドポイントを構成すると、Azure-SSIS IR と、同じリージョン内の Azure SQL、または同じリージョンあるいはペアのリージョン内の Azure Storage との間のトラフィックが、ファイアウォール アプライアンスではなく、Microsoft Azure バックボーン ネットワークに直接ルーティングされます。

Azure-SSIS IR の送信トラフィックを検査する機能が必要でない場合は、次ホップの種類である **[インターネット]** へのすべてのトラフィックを強制するルートを単に適用できます。

-   Azure ExpressRoute のシナリオでは、Azure-SSIS IR をホストするサブネット上で、次ホップの種類が **[インターネット]** の 0.0.0.0/0 ルートを適用できます。 
-   NVA シナリオでは、Azure-SSIS IR をホストするサブネット上で適用されている既存の 0.0.0.0/0 ルートの次ホップの種類を、 **[仮想アプライアンス]** から **[インターネット]** に変更できます。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="ルートを追加する":::

> [!NOTE]
> 次ホップの種類が **[インターネット]** であるルートを指定することは、すべてのトラフィックがインターネットを経由するようになることを意味するわけではありません。 送信先アドレスが Azure のいずれかのサービスに対するものである限り、Azure では、トラフィックをインターネットにルーティングするのではなく、Azure のバックボーン ネットワーク経由でサービスに直接トラフィックをルーティングします。

## <a name="set-up-the-resource-group"></a><a name="resource-group"></a> リソース グループを設定する

Azure SSIS IR では、仮想ネットワークと同じリソース グループ下に特定のネットワーク リソースを作成する必要があります。 これらのリソースには次が含まれます。
- Azure ロード バランサー。 *\<Guid>-azurebatch-cloudserviceloadbalancer* という名前で使用される。
- Azure パブリック IP アドレス。 *\<Guid>-azurebatch-cloudservicepublicip* という名前で使用される。
- ネットワーク ワーク セキュリティ グループ。 *\<Guid>-azurebatch-cloudservicenetworksecuritygroup* という名前で使用される。 

> [!NOTE]
> これで、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用できるようになりました。 このシナリオでは、仮想ネットワークではなく、静的パブリック IP アドレスと同じリソース グループに、Azure ロード バランサーとネットワーク セキュリティ グループのみを作成します。

これらのリソースは、Azure-SSIS IR の開始時に作成されます。 これらは、Azure-SSIS IR の停止時に削除されます。 Azure-SSIS IR 用に独自の静的パブリック IP アドレスを使用する場合は、Azure-SSIS IR の停止時に独自の静的パブリック IP アドレスは削除されません。 Azure-SSIS IR の停止がブロックされないように、他のリソースでこれらのネットワーク リソースを再利用しないでください。

仮想ネットワークまたは静的パブリック IP アドレスが属しているリソース グループまたはサブスクリプションに、リソース ロックがないことを確認します。 読み取り専用または削除ロックを構成した場合、Azure-SSIS IR の開始と停止が失敗するか、応答しなくなります。

仮想ネットワークまたは静的パブリック IP アドレスが属しているリソース グループまたはサブスクリプションで、次のリソースが作成されないようにする Azure Policy の割り当てがないことを確認します。 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

サブスクリプションのリソース クォータが、上記の 3 つのネットワーク リソースに対して十分であることを確認します。 具体的には、仮想ネットワークで作成された Azure-SSIS IR ごとに、上記の 3 つのネットワーク リソースそれぞれに対して 2 つの空きクォータを予約する必要があります。 追加の 1 つのクォータは、定期的に Azure-SSIS IR をアップグレードするときに使用されます。

## <a name="faq"></a><a name="faq"></a> FAQ

- 受信接続用に Azure-SSIS IR で公開されるパブリック IP アドレスを保護するにはどうすればよいですか。 パブリック IP アドレスを削除することはできますか。
 
  現時点では、Azure-SSIS IR が仮想ネットワークに参加すると、パブリック IP アドレスが自動的に作成されます。 NIC レベルの NSG を使用して、Azure-SSIS IR への受信接続のみを Azure Batch 管理サービスに許可します。 受信保護のために、サブネットレベルの NSG を指定することもできます。

  このシナリオに該当する場合、パブリック IP アドレスが公開されないようにするには、仮想ネットワークに Azure-SSIS IR を参加させるのではなく、[Azure-SSIS IR のプロキシとしてセルフホステッド IR を構成する](./self-hosted-integration-runtime-proxy-ssis.md)ことを検討してください。
 
- データ ソースのファイアウォールの許可リストに Azure-SSIS IR のパブリック IP アドレスを追加できますか。

  これで、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用できるようになりました。 この場合、データ ソースのファイアウォールの許可リストに IP アドレスを追加することができます。 また、シナリオに応じて、Azure-SSIS IR からのデータ アクセスをセキュリティで保護するために、以下の他のオプションを検討することができます。

  - データ ソースがオンプレミスの場合は、仮想ネットワークをオンプレミス ネットワークに接続し、仮想ネットワーク サブネットに Azure-SSIS IR を参加させた後、そのサブネットのプライベート IP アドレス範囲を、データ ソースのファイアウォールの許可リストに追加することができます。
  - データ ソースが、仮想ネットワーク サービス エンドポイントをサポートする Azure サービスである場合は、仮想ネットワーク サブネット上で仮想ネットワーク サービス エンドポイントを構成し、そのサブネットに Azure-SSIS IR を参加させることができます。 その後、そのサブネットの仮想ネットワーク規則を、データ ソースのファイアウォールに追加できます。
  - データソースが Azure 以外のクラウド サービスの場合は、UDR を使用して、Azure-SSIS IR からの送信トラフィックを、静的パブリック IP アドレスを介して NVA または Azure Firewall にルーティングできます。 その後、NVA または Azure Firewall の静的パブリック IP アドレスを、データ ソースのファイアウォールの許可リストに追加できます。
  - 上記のいずれのオプションもニーズを満たしていない場合は、[Azure-SSIS IR のプロキシとしてのセルフホステッド IR の構成](./self-hosted-integration-runtime-proxy-ssis.md)を検討してください。 その後、セルフホステッド IR をホストするコンピューターの静的パブリック IP アドレスを、データ ソースのファイアウォールの許可リストに追加できます。

- Azure-SSIS IR 用に独自のものを使用する場合に、2 つの静的パブリック アドレスを指定する必要があるのはなぜですか。

  Azure-SSIS IR は、定期的に自動更新されます。 アップグレード中に新しいノードが作成され、古いものは削除されます。 しかし、ダウンタイムを回避するために、古いノードは新しいものが準備されるまで削除されません。 そのため、古いノードで使用されている最初の静的パブリック IP アドレスをすぐに解放することはできません。新しいノードを作成するには、2 番目の静的パブリック IP アドレスが必要です。

- Azure-SSIS IR 用に独自の静的パブリック IP アドレスを使用することにしましたが、それでもデータ ソースにアクセスできないのはなぜですか。

  - 2 つの静的パブリック IP アドレスが、両方ともデータ ソースのファイアウォールの許可リストに追加されていることを確認してください。 Azure-SSIS IR がアップグレードされるたびに、その静的パブリック IP アドレスが指定された 2 つの間で切り替わります。 そのうちの 1 つのみを許可リストに追加した場合、Azure-SSIS IR のデータ アクセスはそのアップグレード後に切断されます。
  - データ ソースが Azure サービスの場合は、仮想ネットワーク サービス エンドポイントでそれを構成したかどうかを確認してください。 該当する場合、Azure-SSIS IR からデータ ソースへのトラフィックは、Azure サービスによって管理されているプライベート IP アドレスを使用するように切り替わります。データ ソースのファイアウォールの許可リストに独自の静的パブリック IP アドレスを追加しても、反映されません。

## <a name="next-steps"></a>次のステップ
- [Azure-SSIS Integration Runtime を仮想ネットワークに参加させる - 概要](join-azure-ssis-integration-runtime-virtual-network.md)
- [Azure Data Factory Studio UI を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell を使用して Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 
- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](./tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 Azure SQL Database を使って SSIS カタログをホストします。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイントまたは仮想ネットワーク内の SQL Managed Instance を含む Azure SQL Database を使用して、SSIS カタログをホストする手順について説明されます。 Azure-SSIS IR を仮想ネットワークに参加させる方法が示されます。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法が説明されています。 返された情報の状態の説明が提供されます。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。