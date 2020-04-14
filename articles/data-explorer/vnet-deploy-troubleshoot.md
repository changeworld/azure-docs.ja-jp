---
title: 仮想ネットワーク内の Azure Data Explorer クラスターのアクセス、インジェスト、操作に関するトラブルシューティング
description: 仮想ネットワークでの Azure Data Explorer クラスターの接続性、インジェスト、クラスター作成、および操作に関するトラブルシューティング
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241325"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>仮想ネットワーク内の Azure Data Explorer クラスターのアクセス、インジェスト、操作に関するトラブルシューティング

このセクションでは、[Virtual Network](/azure/virtual-network/virtual-networks-overview) にデプロイされているクラスターの接続、操作、およびクラスター作成に関する問題をトラブルシューティングする方法を学習します。

## <a name="access-issues"></a>アクセスの問題

パブリック (cluster.region.kusto.windows.net) またはプライベート (private-cluster.region.kusto.windows.net) エンドポイントを使用してクラスターにアクセスしているときに問題が発生し、それが仮想ネットワークの設定に関するものと思われる場合は、次の手順を行って、その問題をトラブルシューティングします。

### <a name="check-tcp-connectivity"></a>TCP 接続を確認する

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

### <a name="check-the-network-security-group-nsg"></a>ネットワーク セキュリティ グループ (NSG) を確認する

   クラスターのサブネットに接続されている[ネットワーク セキュリティ グループ](/azure/virtual-network/security-overview) (NSG) に、ポート 443 のクライアント マシンの IP からのアクセスを許可する受信規則があることを確認します。

### <a name="check-route-table"></a>ルート テーブルを確認する

   クラスターのサブネットで、ファイアウォールに対して強制トンネリングが設定されている場合 (既定のルート '0.0.0.0/0' を含む[ルート テーブル](/azure/virtual-network/virtual-networks-udr-overview)を持つサブネット)、マシンの IP アドレスに、[次ホップの種類](/azure/virtual-network/virtual-networks-udr-overview)が VirtualNetwork または Internet になっているルートがあることを確認します。 このルートは、非対称ルートの問題を防ぐために必要です。

## <a name="ingestion-issues"></a>インジェストの問題

インジェストの問題が発生しており、それが仮想ネットワークの設定に関するものと思われる場合は、次の手順を行います。

### <a name="check-ingestion-health"></a>インジェストの正常性を確認する

[クラスター インジェスト メトリック](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) が正常な状態であることを確認します。

### <a name="check-security-rules-on-data-source-resources"></a>データ ソース リソースのセキュリティ規則を確認する

データソースから処理されたイベントがないことがメトリックに示されている場合 (イベント/ IoT ハブの場合、*処理済みイベント*のメトリック) には、データソース リソース (イベント ハブまたはストレージ) が、ファイアウォールのルールに基づくクラスター サブネットやサービス エンドポイントからのアクセスを許可していることを確認します。

### <a name="check-security-rules-configured-on-clusters-subnet"></a>クラスターのサブネットに構成されているセキュリティ規則を確認する

クラスターのサブネットに NSG、UDR、およびファイアウォール規則が正しく構成されていることを確認します。 さらに、すべての依存エンドポイントのネットワーク接続をテストします。 

## <a name="cluster-creation-and-operations-issues"></a>クラスターの作成と操作の問題

クラスターの作成または操作の問題が発生しており、それが仮想ネットワークのセットアップに関するものと思われる場合は、これらの手順に従って問題のトラブルシューティングを行います。

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>REST API を使用して仮想ネットワークを診断する

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

*Findings* プロパティに空の結果が表示された場合は、すべてのネットワーク テストが成功し、接続が切断されていないことを意味します。 *送信依存関係 ' {dependencyName}: {port} ' が満たされていません (送信)* のエラーが表示された場合には、クラスターは依存サービス エンドポイントに接続できません。 その場合には、次の手順に進みます。

### <a name="check-network-security-group-nsg"></a>ネットワーク セキュリティ グループ (NSG) を確認する

「[VNet デプロイの依存関係](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)」の手順に従って、[ネットワーク セキュリティ グループ](/azure/virtual-network/security-overview)が適切に構成されていることを確認します

### <a name="check-route-table"></a>ルート テーブルを確認する

クラスターのサブネットで、ファイアウォールに対して強制トンネリングが設定されている場合 (既定のルート '0.0.0.0/0' を含む[ルート テーブル](/azure/virtual-network/virtual-networks-udr-overview)を持つサブネット) には、[管理 IP アドレス](vnet-deployment.md#azure-data-explorer-management-ip-addresses)および[正常性監視 IP アドレス](vnet-deployment.md#health-monitoring-addresses)に[ネクスト ホップ型](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet* のルートがあり、[ソース アドレスのプレフィックス](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route)が *'management-ip/32'* および *'health-monitoring-ip/32'* になっていることを確認します。 このルートは、非対称ルートの問題を防ぐために必要です。

### <a name="check-firewall-rules"></a>ファイアウォール規則を確認する

ファイアウォールにサブネットの送信トラフィックを強制的にトンネリングする場合は、「[ファイアウォールを使用した送信トラフィックのセキュリティ保護](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)」の説明に従って、すべての依存関係の FQDN (たとえば、 *.blob.core.windows.net*) がファイアウォール構成で許可されていることを確認してください。
