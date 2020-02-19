---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 6f0bc57bf73ae1bb44f022e399c941418ccafc03
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169919"
---
|Name |説明 |効果 |Version |
|---|---|---|---|
|[カスタムの IPsec/IKE ポリシーをすべての Azure 仮想ネットワーク ゲートウェイ接続に適用する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VPNGateways_CustomIpSecPolicies_Audit.json) |このポリシーでは、すべての Azure 仮想ネットワーク ゲートウェイ接続がカスタム インターネット プロトコル セキュリティ (Ipsec) またはインターネット キー交換 (IKE) ポリシーを使用することを確認します。 サポートされているアルゴリズムとキーの強度については、 https://aka.ms/AA62kb0 をご覧ください |Audit、Disabled |1.0.0 |
|[App Service は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての App Service を監査します。 |AuditIfNotExists、Disabled |1.0.0 |
|[Azure VPN ゲートウェイで 'Basic' SKU を使用しないでください](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VPNGateways_BasicSKU_Audit.json) |このポリシーでは、VPN ゲートウェイが 'Basic' SKU を使用していないことを確認します。 |Audit、Disabled |1.0.0 |
|[Cosmos DB は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Cosmos DB を監査します。 |Audit、Disabled |1.0.0 |
|[仮想ネットワーク作成時の Network Watcher のデプロイ](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |このポリシーは、仮想ネットワークのあるリージョンに Network Watcher リソースを作成します。 Network Watcher インスタンスをデプロイするために使用される、networkWatcherRG という名前のリソース グループが存在することを確認する必要があります。 |DeployIfNotExists |1.0.0 |
|[イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのイベント ハブを監査します。 |AuditIfNotExists、Disabled |1.0.0 |
|[ゲートウェイ サブネットをネットワーク セキュリティ グループで構成してはならない](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |このポリシーは、ゲートウェイ サブネットがネットワーク セキュリティ グループで構成されている場合に拒否します。 ネットワーク セキュリティ グループをゲートウェイ サブネットに割り当てると、ゲートウェイが機能しなくなります。 |deny |1.0.0 |
|[Key Vault は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Key Vault を監査します。 |Audit、Disabled |1.0.0 |
|[ネットワーク インターフェイスで IP 転送を無効にする](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkIPForwardingNic_Deny.json) |このポリシーは、IP 転送を有効にしたネットワーク インターフェイスを拒否します。 IP 転送の設定により、Azure によるネットワーク インターフェイスの送信元と送信先のチェックが無効になります。 これは、ネットワーク セキュリティ チームが確認する必要があります。 |deny |1.0.0 |
|[ネットワーク インターフェイスにはパブリック IP を設定できない](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkPublicIPNic_Deny.json) |このポリシーは、パブリック IP で構成されているネットワーク インターフェイスを拒否します。 パブリック IP アドレスを使用すると、インターネット リソースから Azure リソースへの受信通信を許可したり、Azure リソースからインターネットへの送信通信を許可したりすることができます。 これは、ネットワーク セキュリティ チームが確認する必要があります。 |deny |1.0.0 |
|[Network Watcher を有効にする必要がある](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。 |auditIfNotExists |1.0.0 |
|[インターネットからの RDP アクセスをブロックする必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |このポリシーは、インターネットからの RDP アクセスを許可するすべてのネットワーク セキュリティ規則を監査します |Audit、Disabled |1.0.0 |
|[Service Bus は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Service Bus を監査します。 |AuditIfNotExists、Disabled |1.0.0 |
|[SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての SQL Server を監査します。 |AuditIfNotExists、Disabled |1.0.0 |
|[インターネットからの SSH アクセスをブロックする必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |このポリシーは、インターネットからの SSH アクセスを許可するすべてのネットワーク セキュリティ規則を監査します |Audit、Disabled |1.0.0 |
|[ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのストレージ アカウントを監査します。 |Audit、Disabled |1.0.0 |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |1.0.0 |
|[仮想ネットワークは、指定された仮想ネットワーク ゲートウェイを使用する必要があります](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |このポリシーは、指定された仮想ネットワーク ゲートウェイを既定のルートが指していない場合に、仮想ネットワークを監査します。 |AuditIfNotExists、Disabled |1.0.0 |
