---
title: Azure Virtual WAN でプライベート エンドポイント宛てのトラフィックをセキュリティで保護する
description: ネットワーク ルールとアプリケーション ルールを使用して、Azure Virtual WAN でプライベート エンドポイント宛てのトラフィックをセキュリティで保護する方法について説明します。
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: 7322bab635d398fc7a5335546ba6fef327ff24b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259355"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Azure Virtual WAN でプライベート エンドポイント宛てのトラフィックをセキュリティで保護する

> [!NOTE]
> この記事は、セキュリティ保護付き仮想ハブに対してのみ適用されます。 ハブ仮想ネットワーク内の Azure Firewall を使用して、プライベート エンドポイント宛てのトラフィックを検査する場合は、「 [Azure Firewall を使用してプライベート エンドポイント宛てのトラフィックの検査する](../private-link/inspect-traffic-with-azure-firewall.md)」を参照してください。

[Azure プライベート エンドポイント](../private-link/private-endpoint-overview.md)は、[Azure Private Link](../private-link/private-link-overview.md) の基本的な構成要素です。 プライベート エンドポイントを使用すると、仮想ネットワークにデプロイされた Azure リソースから、プライベート リンク リソースとプライベートに通信できます。

プライベート エンドポイントを使用すると、リソースから、仮想ネットワークにデプロイされたプライベート リンク サービスにアクセスできます。 仮想ネットワーク ピアリングとオンプレミス ネットワーク接続を介したプライベート エンドポイントへのアクセスにより、接続性が拡張されます。

Virtual WAN に接続された仮想ネットワーク内のプライベート エンドポイント経由で公開されるサービスに対して、オンプレミスまたは Azure 内のクライアントからのトラフィックをフィルター処理することが必要になる場合があります。 この記事では、セキュリティ プロバイダーとして [Azure Firewall](../firewall/overview.md) で[セキュリティ保護付き仮想ハブ](../firewall-manager/secured-virtual-hub.md)を使用してこのタスクを行う手順について説明します。

Azure Firewall は、次のいずれかの方法を使用してトラフィックをフィルター処理します。

* TCP および UDP プロトコルの場合、[ネットワーク ルール内の FQDN](../firewall/fqdn-filtering-network-rules.md)
* HTTP、HTTPS、MSSQL の場合、[アプリケーション ルール内の FQDN](../firewall/features.md#application-fqdn-filtering-rules)
* [ネットワーク ルール](../firewall/features.md#network-traffic-filtering-rules)を使用する送信元と送信先の IP アドレス、ポート、およびプロトコル

ネットワーク ルールに対するアプリケーション ルールを使用して、プライベート エンドポイント宛てのトラフィックを検査します。
セキュリティ保護付き仮想ハブは、Microsoft によって管理され、[プライベート DNS ゾーン](../dns/private-dns-privatednszone.md)にリンクすることはできません。 これは、[プライベート リンク リソース](../private-link/private-endpoint-overview.md#private-link-resource) の FQDN を、対応するプライベート エンドポイントの IP アドレスに解決するために必要です。

SQL の FQDN のフィルター処理は、[プロキシ モード](../azure-sql/database/connectivity-architecture.md#connection-policy)のみでサポートされます (ポート 1433)。 *プロキシ* モードを使用すると、*リダイレクト* と比較して待機時間が増加する可能性があります。 リダイレクト モードを使用して構成する場合は (Azure 内から接続するクライアントの既定)、代わりにファイアウォール ネットワーク ルールで FQDN を使ってアクセスをフィルター処理できます。

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>ネットワーク ルールとアプリケーション ルールで FQDN を使用してトラフィックをフィルター処理する

ネットワーク ルールとアプリケーション ルールで FQDN を使用してトラフィックをフィルター処理するために Azure Firewall を有効にするには、次の手順を実行します。

1. セキュリティ保護付き仮想ハブに接続され、プライベート エンドポイントのレコード型をホストするプライベート DNS ゾーンにリンクされている仮想ネットワークに [DNS フォワーダー](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)仮想マシンをデプロイします。

2. [カスタム DNS 設定](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal)を構成して、DNS フォワーダーの仮想マシンの IP アドレスを指定し、セキュリティ保護付き仮想ハブにデプロイされている Azure Firewall に関連付けられているファイアウォール ポリシーで DNS プロキシを有効にします。

3. セキュリティ保護付き仮想ハブに接続されている仮想ネットワークの[カスタム DNS サーバー](../virtual-network/manage-virtual-network.md#change-dns-servers)を構成して、セキュリティ保護付き仮想ハブにデプロイされている Azure Firewall に関連付けられているプライベート IP アドレスを指定します。

4. プライベート エンドポイントのパブリック DNS ゾーンの DNS クエリを、セキュリティ保護付き仮想ハブにデプロイされている Azure Firewall に関連付けられているプライベート IP アドレスに転送するように、オンプレミスの DNS サーバーを構成します。

5. 必要に応じて、セキュリティ保護付き仮想ハブにデプロイされている Azure Firewall に関連付けられているファイアウォール ポリシーで、*宛先の種類* が FQDN でプライベート リンク リソースのパブリック FQDN を *宛先* に設定して、[アプリケーション ルール](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)または[ネットワーク ルール](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)を構成します。

6. セキュリティ保護付き仮想ハブにデプロイされている Azure Firewall に関連付けられているファイアウォール ポリシーの *[セキュリティ保護付き仮想ハブ]* に移動し、プライベート エンドポイント宛てのトラフィック フィルターを構成するセキュリティ保護付き仮想ハブを選択します。

7. **[セキュリティ構成]** に移動し、 **[プライベート トラフィック]** の **[Azure Firewall 経由で送信]** を選択します。

8. **[プライベート トラフィック プレフィックス]** を選択して、セキュリティ保護付き仮想ハブの Azure Firewall で検査する CIDR プレフィックスを編集し、次のように、プライベート エンドポイントごとに 1 つの/32 プレフィックスを追加します。

   > [!IMPORTANT]
   > これらの/32 プレフィックスが構成されていない場合、プライベート エンドポイント宛てのトラフィックは Azure Firewall をバイパスします。

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Firewall Manager のセキュリティ構成" border="true":::

これらの手順は、クライアントとプライベート エンドポイントが、同じセキュリティ保護付き仮想ハブとオンプレミスのクライアントに接続されている異なる仮想ネットワークに展開されている場合にのみ機能します。 クライアントとプライベート エンドポイントが同じ仮想ネットワークにデプロイされている場合は、プライベート エンドポイントに対して /32 ルートの UDR を作成する必要があります。 **[ネクストホップの種類]** を **[仮想アプライアンス]** に設定し、 **[次ホップ アドレス]** を、セキュリティ保護付き仮想ハブにデプロイされている Azure Firewall のプライベート IP アドレスに設定して、これらのルートを構成します。 **[ゲートウェイのルートを伝達する]** を **[はい]** に設定する必要があります。

次の図は、さまざまなクライアントが Azure Virtual WAN にデプロイされたプライベート エンドポイントに接続するための DNS トラフィック フローとデータ トラフィック フローを示しています。

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="トラフィック フロー" border="true":::

## <a name="troubleshooting"></a>トラブルシューティング

セキュリティ保護付き仮想ハブ経由でプライベート エンドポイント宛てのトラフィックをフィルター処理しようとすると、主に次の問題が発生する可能性があります。

- クライアントがプライベート エンドポイントに接続できない。

- Azure Firewall がバイパスされる。 この現象は、Azure Firewall にネットワーク ルールまたはアプリケーション ルールのログ エントリがないことによって検証できます。

ほとんどの場合、これらの問題は次のいずれかの問題が原因で発生します。

- 不正確な DNS 名前解決

- 不正確なルーティング構成

### <a name="incorrect-dns-name-resolution"></a>不正確な DNS 名前解決

1. 仮想ネットワークの DNS サーバーが *[カスタム]* に設定されていること、および IP アドレスがセキュリティ保護付き仮想ハブの Azure Firewall のプライベート IP アドレスであることを確認します。

   Azure CLI:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. DNS フォワーダー仮想マシンと同じ仮想ネットワーク内のクライアントが、DNS フォワーダーとして構成されている仮想マシンに直接クエリを実行することで、プライベート エンドポイント パブリック FQDN を対応するプライベート IP アドレスに解決できることを確認します。

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. *AzureFirewallDNSProxy* Azure Firewall ログ エントリを検査し、クライアントから DNS クエリを受信して解決できることを確認します。

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. *DNS プロキシ* が有効になっていること、および DNS フォワーダーの仮想マシンの IP アドレスを指す *カスタム* DNS サーバーが、セキュリティ保護付き仮想ハブの Azure Firewall に関連付けられているファイアウォール ポリシーで構成されていることを確認します。

   Azure CLI:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>不正確なルーティング構成

1. セキュリティ保護付き仮想ハブにデプロイされている Azure Firewall に関連付けられているファイアウォール ポリシーの *セキュリティ構成* を確認します。 トラフィックをフィルター処理するすべての仮想ネットワークとブランチ接続について、**プライベート トラフィック** 列に **Azure Firewall によってセキュリティ保護** と表示されていることを確認します。

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Azure Firewall によってセキュリティ保護されたプライベート トラフィック" border="true":::

2. セキュリティ保護付き仮想ハブにデプロイされている Azure Firewall に関連付けられているファイアウォール ポリシーの **セキュリティ構成** を確認します。 トラフィックをフィルター処理するプライベート エンドポイント プライベート IP アドレスごとに、**プライベート トラフィック プレフィックス** で /32 エントリがあることを確認します。

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Firewall Manager のセキュリティ構成 - プライベート トラフィック プレフィックス" border="true":::

3. 仮想 WAN の下にあるセキュリティ保護付き仮想ハブで、トラフィックをフィルター処理する仮想ネットワークとブランチ接続に関連付けられているルート テーブルの有効なルートを調べます。 トラフィックをフィルター処理する各プライベート エンドポイント プライベート IP アドレスに対して /32 エントリがあることを確認します。

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="セキュリティ保護付き仮想ハブの有効なルート" border="true":::

4. トラフィックをフィルター処理する仮想ネットワークにデプロイされている仮想マシンに接続されている NIC の有効なルートを調べます。 トラフィックをフィルター処理する各プライベート エンドポイント プライベート IP アドレスに対して /32 エントリがあることを確認します。
 
   Azure CLI:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. オンプレミスのルーティング デバイスのルーティング テーブルを調べます。 プライベート エンドポイントがデプロイされている仮想ネットワークのアドレス空間を学習していることを確認します。

   Azure Virtual WAN は、ファイアウォール ポリシーの **セキュリティ構成** の **プライベート トラフィック プレフィックス** で構成されたプレフィックスをオンプレミスに公開しません。 オンプレミスのルーティング デバイスのルーティング テーブルに /32 エントリが表示されないことが想定されています。

6. **AzureFirewallApplicationRule** と **AzureFirewallNetworkRule** の Azure Firewall ログを調べます。 プライベート エンドポイント宛てのトラフィックがログに記録されていることを確認します。

   **AzureFirewallNetworkRule** ログ エントリには、FQDN 情報は含まれません。 ネットワーク ルールを検査するときに、IP アドレスとポートでフィルター処理します。

   [Azure Files](../storage/files/storage-files-introduction.md) プライベート エンドポイント宛てのトラフィックをフィルター処理する場合、**AzureFirewallNetworkRule** ログ エントリは、クライアントが最初にファイル共有にマウントまたは接続したときにのみ生成されます。 Azure Firewall は、ファイル共有内のファイルに対する [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 操作のログを生成しません。 これは、クライアントが最初にファイル共有に接続またはマウントするときに開いた永続的な TCP チャネルを介して CRUD 操作が実行されるためです。

   アプリケーション ルール ログのクエリ例:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>次のステップ

- [Azure Firewall を使用してプライベート エンドポイント宛てのトラフィックを検査する](../private-link/inspect-traffic-with-azure-firewall.md)
