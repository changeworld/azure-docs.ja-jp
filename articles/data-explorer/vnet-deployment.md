---
title: Azure Data Explorer を仮想ネットワークにデプロイする
description: Azure Data Explorer を仮想ネットワークにデプロイする方法について説明します
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 5a2731e26ba4f371177cf2ae649f0695f27e6304
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096757"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network"></a>Azure Data Explorer を仮想ネットワークにデプロイする

この記事では、Azure Data Explorer クラスターをカスタム Azure Virtual Network にデプロイするときに存在するリソースについて説明します。 この情報は、Virtual Network (VNet) のサブネットにクラスターをデプロイする際に役立ちます。 Azure Virtual Network の詳細については、「[Azure Virtual Network とは](/azure/virtual-network/virtual-networks-overview)」をご覧ください。

   ![VNet の図](media/vnet-deployment/vnet-diagram.png)

Azure Data Explorer では、Virtual Network (VNet) のサブネットへのクラスターのデプロイがサポートされています。 この機能により、次のことが可能になります。

* Azure Data Explorer クラスター トラフィックに[ネットワーク セキュリティ グループ](/azure/virtual-network/security-overview) (NSG) 規則を適用する。
* オンプレミス ネットワークを Azure Data Explorer クラスターのサブネットに接続する。
* [サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)を使用してデータ接続ソース ([Event Hub](/azure/event-hubs/event-hubs-about) と [Event Grid](/azure/event-grid/overview)) のセキュリティを保護する。

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>VNet 内の Azure Data Explorer クラスターにアクセスする

各サービス (エンジンとデータ管理サービス) に対して次の IP アドレスを使用して、Azure Data Explorer クラスターにアクセスできます。

* **プライベート IP**:VNet 内のクラスターにアクセスするために使用されます。
* **[パブリック IP]** : 管理と監視のために VNet の外部からクラスターにアクセスするため、およびクラスターから開始された送信接続の発信元アドレスとして使用されます。

サービスにアクセスするために、次の DNS レコードが作成されます。 

* `[clustername].[geo-region].kusto.windows.net` (エンジン) `ingest-[clustername].[geo-region].kusto.windows.net` (データ管理) は、各サービスのパブリック IP にマップされます。 

* `private-[clustername].[geo-region].kusto.windows.net` (エンジン) `private-ingest-[clustername].[geo-region].kusto.windows.net` (データ管理) は、各サービスのプライベート IP にマップされます。

## <a name="plan-subnet-size-in-your-vnet"></a>VNet でサブネットのサイズを計画する

Azure Data Explorer クラスターをホストするために使用されるサブネットのサイズは、サブネットをデプロイした後に変更することはできません。 VNet では、Azure Data Explorer は VM ごとに 1 つのプライベート IP アドレスを使用し、内部ロード バランサー (エンジンとデータ管理) に 2 つのプライベート IP アドレスを使用します。 さらに Azure ネットワークでは、サブネットごとに 5 つの IP アドレスを使用します。 Azure Data Explorer は、データ管理サービス用に 2 つの VM をプロビジョニングします。 エンジン サービス VM は、ユーザー構成のスケール容量ごとにプロビジョニングされます。

IP アドレスの合計数は次のようになります。

| 用途 | アドレスの数 |
| --- | --- |
| エンジン サービス | インスタンスごとに 1 |
| データ管理サービス | 2 |
| 内部ロード バランサー | 2 |
| Azure 予約アドレス | 5 |
| **合計** | **#engine_instances + 9** |

> [!IMPORTANT]
> サブネットのサイズは、Azure Data Explorer をデプロイした後に変更することはできないため、事前に計画する必要があります。 そのため、必要に応じてサブネット サイズを確保してください。

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Azure Data Explorer に接続するためのサービス エンドポイント

[Azure サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview)を使用すると、仮想ネットワークに対して Azure マルチテナント リソースのセキュリティを保護することができます。
Azure Data Explorer クラスターをサブネットにデプロイすると、Azure Data Explorer サブネットの基になるリソースを制限しながら、[Event Hub](/azure/event-hubs/event-hubs-about) または [Event Grid](/azure/event-grid/overview) を使用してデータ接続を設定できます。

> [!NOTE]
> [Storage](/azure/storage/common/storage-introduction) と [Event Hub] で EventGrid セットアップを使用する場合、サブスクリプションで使用されているストレージ アカウントは、信頼できる Azure プラットフォーム サービスを[ファイアウォール構成](/azure/storage/common/storage-network-security)で許可しながら、Azure Data Explorer のサブネットへのサービス エンドポイントを使用してロックすることができます。しかし、イベント ハブでは、信頼できる [Azure プラットフォーム サービス](/azure/event-hubs/event-hubs-service-endpoints)がサポートされないため、サービス エンドポイントを有効にできません。

## <a name="dependencies-for-vnet-deployment"></a>VNet デプロイの依存関係

### <a name="network-security-groups-configuration"></a>ネットワーク セキュリティ グループ構成

[ネットワーク セキュリティ グループ (NSG)](/azure/virtual-network/security-overview) は、VNet 内でネットワーク アクセスを制御する機能を提供します。 Azure Data Explorer には、2 つのエンドポイント、HTTPs (443) および TDS (1433) を使用してアクセスできます。 クラスターの管理、監視、および適切な操作のために、これらのエンドポイントへのアクセスを許可するように次の NSG ルールを構成する必要があります。

#### <a name="inbound-nsg-configuration"></a>受信 NSG 構成

| **用途**   | **From**   | **To**   | **プロトコル**   |
| --- | --- | --- | --- |
| 管理  |[ADX 管理アドレス](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | ADX サブネット: 443  | TCP  |
| 正常性の監視  | [ADX 正常性の監視アドレス](#health-monitoring-addresses)  | ADX サブネット: 443  | TCP  |
| ADX 内部通信  | ADX サブネット:すべてのポート  | ADX サブネット: すべてのポート  | All  |
| Azure Load Balancer の着信を許可 (正常性プローブ)  | AzureLoadBalancer  | ADX サブネット: 80、443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>送信 NSG 構成

| **用途**   | **From**   | **To**   | **プロトコル**   |
| --- | --- | --- | --- |
| Azure Storage への依存関係  | ADX サブネット  | Storage:443  | TCP  |
| Azure Data Lake への依存関係  | ADX サブネット  | AzureDataLake:443  | TCP  |
| EventHub インジェストとサービス監視  | ADX サブネット  | EventHub:443,5671  | TCP  |
| メトリックの発行  | ADX サブネット  | AzureMonitor:443 | TCP  |
| Azure Monitor 構成のダウンロード  | ADX サブネット  | [Azure Monitor 構成エンドポイント アドレス](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| Active Directory (該当する場合) | ADX サブネット | AzureActiveDirectory:443 | TCP |
| 証明機関 | ADX サブネット | Internet:80 | TCP |
| 内部通信  | ADX サブネット  | ADX サブネット: すべてのポート  | All  |
| `sql\_request` と `http\_request` のプラグインに使用されるポート  | ADX サブネット  | Internet:Custom  | TCP  |

### <a name="relevant-ip-addresses"></a>関連する IP アドレス

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure Data Explorer 管理 IP アドレス

| リージョン | アドレス |
| --- | --- |
| オーストラリア中部 | 20.37.26.134 |
| オーストラリア中部 2 | 20.39.99.177 |
| オーストラリア東部 | 40.82.217.84 |
| オーストラリア南東部 | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| カナダ中部 | 40.82.188.208 |
| カナダ東部 | 40.80.255.12 |
| インド中部 | 40.81.249.251 |
| 米国中部 | 40.67.188.68 |
| 米国中部 EUAP | 40.89.56.69 |
| 東アジア | 20.189.74.103 |
| 米国東部 | 52.224.146.56 |
| 米国東部 2 | 52.232.230.201 |
| 米国東部 2 EUAP | 52.253.226.110 |
| フランス中部 | 40.66.57.91 |
| フランス南部 | 40.82.236.24 |
| 東日本 | 20.43.89.90 |
| 西日本 | 40.81.184.86 |
| 韓国中部 | 40.82.156.149 |
| 韓国南部 | 40.80.234.9 |
| 米国中北部 | 40.81.45.254 |
| 北ヨーロッパ | 52.142.91.221 |
| 南アフリカ北部 | 102.133.129.138 |
| 南アフリカ西部 | 102.133.0.97 |
| 米国中南部 | 20.45.3.60 |
| 東南アジア | 40.119.203.252 |
| インド南部 | 40.81.72.110 |
| 英国南部 | 40.81.154.254 |
| 英国西部 | 40.81.122.39 |
| 米国中西部 | 52.159.55.120 |
| 西ヨーロッパ | 51.145.176.215 |
| インド西部 | 40.81.88.112 |
| 米国西部 | 13.64.38.225 |
| 米国西部 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>正常性の監視アドレス

| リージョン | アドレス |
| --- | --- |
| オーストラリア中部 | 191.239.64.128 |
| オーストラリア中部 2 | 191.239.64.128 |
| オーストラリア東部 | 191.239.64.128 |
| オーストラリア南東部 | 191.239.160.47 |
| ブラジル南部 | 23.98.145.105 |
| カナダ中部 | 168.61.212.201 |
| カナダ東部 | 168.61.212.201 |
| インド中部 | 23.99.5.162 |
| 米国中部 | 168.61.212.201 |
| 米国中部 EUAP | 168.61.212.201 |
| 東アジア | 168.63.212.33 |
| 米国東部 | 137.116.81.189 |
| 米国東部 2 | 137.116.81.189 |
| 米国東部 2 EUAP | 137.116.81.189 |
| フランス中部 | 23.97.212.5 |
| フランス南部 | 23.97.212.5 |
| 東日本 | 138.91.19.129 |
| 西日本 | 138.91.19.129 |
| 韓国中部 | 138.91.19.129 |
| 韓国南部 | 138.91.19.129 |
| 米国中北部 | 23.96.212.108 |
| 北ヨーロッパ | 191.235.212.69 
| 南アフリカ北部 | 104.211.224.189 |
| 南アフリカ西部 | 104.211.224.189 |
| 米国中南部 | 23.98.145.105 |
| インド南部 | 23.99.5.162 |
| 東南アジア | 168.63.173.234 |
| 英国南部 | 23.97.212.5 |
| 英国西部 | 23.97.212.5 |
| 米国中西部 | 168.61.212.201 |
| 西ヨーロッパ | 23.97.212.5 |
| インド西部 | 23.99.5.162 |
| 米国西部 | 23.99.5.162 |
| 米国西部 2 | 23.99.5.162 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure Monitor 構成エンドポイント アドレス

| リージョン | アドレス |
| --- | --- |
| オーストラリア中部 | 52.148.86.165 |
| オーストラリア中部 2 | 52.148.86.165 |
| オーストラリア東部 | 52.148.86.165 |
| オーストラリア南東部 | 52.148.86.165 |
| ブラジル南部 | 13.68.89.19 |
| カナダ中部 | 13.90.43.231 |
| カナダ東部 | 13.90.43.231 |
| インド中部 | 13.71.25.187 |
| 米国中部 | 52.173.95.68 |
| 米国中部 EUAP | 13.90.43.231 |
| 東アジア | 13.75.117.221 |
| 米国東部 | 13.90.43.231 |
| 米国東部 2 | 13.68.89.19 |    
| 米国東部 2 EUAP | 13.68.89.19 |
| フランス中部 | 52.174.4.112 |
| フランス南部 | 52.174.4.112 |
| 東日本 | 13.75.117.221 |
| 西日本 | 13.75.117.221 |
| 韓国中部 | 13.75.117.221 |
| 韓国南部 | 13.75.117.221 |
| 米国中北部 | 52.162.240.236 |
| 北ヨーロッパ | 52.169.237.246 |
| 南アフリカ北部 | 13.71.25.187 |
| 南アフリカ西部 | 13.71.25.187 |
| 米国中南部 | 13.84.173.99 |
| インド南部 | 13.71.25.187 |
| 東南アジア | 52.148.86.165 |
| 英国南部 | 52.174.4.112 |
| 英国西部 | 52.169.237.246 |
| 米国中西部 | 52.161.31.69 |
| 西ヨーロッパ | 52.174.4.112 |
| インド西部 | 13.71.25.187 |
| 米国西部 | 40.78.70.148 |
| 米国西部 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>ExpressRoute セットアップ

ExpressRoute を使用して、オンプレミス ネットワークを Azure 仮想ネットワークに接続できます。 一般的なセットアップでは、Border Gateway Protocol (BGP) セッションを介して既定のルート (0.0.0.0/0) をアドバタイズします。 これにより、Virtual Network からのトラフィックが、トラフィックを破棄する可能性がある顧客のオンプレミス ネットワークに強制的に転送されるため、送信フローが中断される結果となります。 この既定の設定を解決するために、[ユーザー定義ルート (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) を構成でき、次ホップは*インターネット*になります。 UDR は BGP よりも優先されるため、トラフィックはインターネットに送られます。

## <a name="securing-outbound-traffic-with-firewall"></a>ファイアウォールを使用した送信トラフィックのセキュリティ保護

[Azure Firewall](/azure/firewall/overview) または任意の仮想アプライアンスを使用して送信トラフィックのセキュリティを保護し、ドメイン名を制限する場合は、ファイアウォールで次の完全修飾ドメイン名 (FQDN) を許可する必要があります。

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

また、非対称ルートの問題を防ぐために、次ホップが*インターネット*である[管理アドレス](#azure-data-explorer-management-ip-addresses)および[正常性監視アドレス](#health-monitoring-addresses)を使用するサブネット上の[ルート テーブル](/azure/virtual-network/virtual-networks-udr-overview)を定義する必要があります。

たとえば、**米国西部**リージョンでは、次の UDR を定義する必要があります。

| 名前 | アドレス プレフィックス | 次ホップ |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | インターネット |
| ADX_Monitoring | 23.99.5.162/32 | インターネット |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Azure Data Explorer クラスターを VNet にデプロイする

Azure Data Explorer クラスターを仮想ネットワークにデプロイするには、「[Azure Data Explorer クラスターを VNet にデプロイする](https://azure.microsoft.com/resources/templates/101-kusto-vnet/)」の Azure Resource Manager テンプレートを使用します。

このテンプレートは、クラスター、仮想ネットワーク、サブネット、ネットワーク セキュリティ グループ、およびパブリック IP アドレスを作成します。

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、[Virtual Network](/azure/virtual-network/virtual-networks-overview) にデプロイされているクラスターの接続、操作、およびクラスター作成に関する問題をトラブルシューティングする方法を学習します。

### <a name="access-issues"></a>アクセスの問題

パブリック (cluster.region.kusto.windows.net) またはプライベート (private-cluster.region.kusto.windows.net) エンドポイントを使用してクラスターにアクセスしているときに問題が発生し、それが仮想ネットワークの設定に関するものと思われる場合は、次の手順を行って、その問題をトラブルシューティングします。

#### <a name="check-tcp-connectivity"></a>TCP 接続を確認する

最初の手順には、Windows または Linux OS を使用した TCP 接続の確認が含まれます。

# <a name="windows"></a>[Windows](#tab/windows)

   1. クラスターに接続しているマシンに、[TCping](https://www.elifulkerson.com/projects/tcping.php) をダウンロードします。
   2. 次のコマンドを使用して、ソース マシンからターゲットに ping を実行します。

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. クラスターに接続しているマシンに、*netcat* をインストールします

    ```bash
    $ apt-get install netcat
     ```

   2. 次のコマンドを使用して、ソース マシンからターゲットに ping を実行します。

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

テストが成功しなかった場合は、次の手順に進みます。 テストが成功した場合、問題は TCP 接続の問題によるものではありません。 [操作の問題](#cluster-creation-and-operations-issues)に関する説明に移動し、さらにトラブルシューティングを行います。

#### <a name="check-the-network-security-group-nsg"></a>ネットワーク セキュリティ グループ (NSG) を確認する

   クラスターのサブネットに接続されている[ネットワーク セキュリティ グループ](/azure/virtual-network/security-overview) (NSG) に、ポート 443 のクライアント マシンの IP からのアクセスを許可する受信規則があることを確認します。

#### <a name="check-route-table"></a>ルート テーブルを確認する

   クラスターのサブネットで、ファイアウォールに対して強制トンネリングが設定されている場合 (既定のルート '0.0.0.0/0' を含む[ルート テーブル](/azure/virtual-network/virtual-networks-udr-overview)を持つサブネット)、マシンの IP アドレスに、[次ホップの種類](/azure/virtual-network/virtual-networks-udr-overview)が VirtualNetwork または Internet になっているルートがあることを確認します。 これは、非対称ルートの問題を防ぐために必要です。

### <a name="ingestion-issues"></a>インジェストの問題

インジェストの問題が発生しており、それが仮想ネットワークの設定に関するものと思われる場合は、次の手順を行います。

#### <a name="check-ingestion-health"></a>インジェストの正常性を確認する

    Check that the [cluster ingestion metrics](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) indicate a healthy state.

#### <a name="check-security-rules-on-data-source-resources"></a>データ ソース リソースのセキュリティ規則を確認する

データ ソースから処理されたイベントがなかったことがメトリックで示されている場合 ( *[処理されたイベント]* (Event または IoT Hub の場合) メトリック) は、ファイアウォール規則またはサービス エンドポイントで、クラスターのサブネットからのアクセスがデータ ソース リソース (Event Hub または Storage) で許可されていることを確認します。

#### <a name="check-security-rules-configured-on-clusters-subnet"></a>クラスターのサブネットに構成されているセキュリティ規則を確認する

クラスターのサブネットに NSG、UDR、およびファイアウォール規則が正しく構成されていることを確認します。 さらに、すべての依存エンドポイントのネットワーク接続をテストします。 

### <a name="cluster-creation-and-operations-issues"></a>クラスターの作成と操作の問題

クラスターの作成または操作の問題が発生しており、それが仮想ネットワークのセットアップに関するものと思われる場合は、これらの手順に従って問題のトラブルシューティングを行います。

#### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>REST API を使用して仮想ネットワークを診断する

[ARMClient](https://chocolatey.org/packages/ARMClient) は、PowerShell を使用して REST API を呼び出すために使用されます。 

1. ARMClient でログインする

   ```powerShell
   armclient login
   ```

1. 診断操作を呼び出します

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. 応答を確認します

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. 操作の完了を待ちます

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   *status* プロパティに *Completed* と表示されるまで待ちます。その後、*properties* フィールドには次のように表示されるはずです。

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

*Findings* プロパティに空の結果が表示された場合は、すべてのネットワーク テストが成功し、接続が切断されていないことを意味します。 次のようなエラーが表示された場合: *送信依存関係 '{dependencyName}:{port}' が満たされていない可能性があります (送信)* 、クラスターは依存サービス エンドポイントに到達できません。 次の手順に進んでトラブルシューティングを行います。

#### <a name="check-network-security-group-nsg"></a>ネットワーク セキュリティ グループ (NSG) を確認する

「[VNet デプロイの依存関係](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)」の手順に従って、[ネットワーク セキュリティ グループ](/azure/virtual-network/security-overview)が適切に構成されていることを確認します

#### <a name="check-route-table"></a>ルート テーブルを確認する

クラスターのサブネットで、ファイアウォールに対して強制トンネリングが設定されている場合 (既定のルート '0.0.0.0/0' を含む[ルート テーブル](/azure/virtual-network/virtual-networks-udr-overview)を持つサブネット)、[管理 IP アドレス](#azure-data-explorer-management-ip-addresses)および[正常性監視 IP アドレス](#health-monitoring-addresses)に、[次ホップの種類](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools)が *Internet* のルートがあり、[ソース アドレスのプレフィックス](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route)が *'management-ip/32'* および *'health-monitoring-ip/32'* になっていることを確認します。 これは、非対称ルートの問題を防ぐために必要です。

#### <a name="check-firewall-rules"></a>ファイアウォール規則を確認する

ファイアウォールにサブネットの送信トラフィックを強制的にトンネリングする場合は、「[ファイアウォールを使用した送信トラフィックのセキュリティ保護](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)」の説明に従って、すべての依存関係の FQDN (たとえば、 *.blob.core.windows.net*) がファイアウォール構成で許可されていることを確認してください。
