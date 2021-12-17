---
title: Service Fabric マネージド クラスターのネットワーク設定を構成する
description: NSG ルール、RDP ポート アクセス、負荷分散規則などに関して Service Fabric マネージド クラスターを構成する方法について説明します。
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 3482f414029c79ceea9c0ee8bcc258ed2fc495e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558879"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>Service Fabric マネージド クラスターのネットワーク設定を構成する

Service Fabric マネージド クラスターは、既定のネットワーク構成を使用して作成されます。 この構成は、パブリック IP を使用する [Azure ロード バランサー](../load-balancer/load-balancer-overview.md)、1 つのサブネットが割り当てられた VNet、および基本的なクラスター機能用に構成された NSG から成ります。 また、お客様による構成を容易にすることを目的としてすべての送信トラフィックを既定で許可するなどの、オプションの NSG ルールも適用されます。 このドキュメントでは、次のようなネットワーク構成オプションを変更する方法について説明します。

- [NSG ルールの管理](#nsgrules)
- [RDP アクセスの管理](#rdp)
- [ロード バランサー構成の管理](#lbconfig)
- [IPv6 を有効にする](#ipv6)
- [Bring Your Own Virtual Network](#byovnet)
- [Bring Your Own Load Balancer](#byolb)

<a id="nsgrules"></a>
## <a name="manage-nsg-rules"></a>NSG ルールの管理

### <a name="nsg-rules-guidance"></a>NSG ルールのガイダンス

マネージド クラスター用に新しい NSG ルールを作成するときは、次の点に注意してください。

* NSG ルールの 0 から 999 の優先順位範囲は、Service Fabric マネージド クラスターにより必須機能用に予約されています。 優先順位の値が 1000 未満のカスタム NSG ルールは作成できません。
* 3001 から 4000 の優先順位範囲は、Service Fabric マネージド クラスターによりオプションの NSG ルールの作成用に予約されています。 これらのルールは、すばやく簡単に構成できるように自動的に追加されます。 1000 から 3000 の優先順位範囲でカスタム NSG ルールを追加することにより、これらのルールを上書きできます。
* カスタム NSG ルールは、1000 から 3000 の範囲内の優先順位にする必要があります。

### <a name="apply-nsg-rules"></a>NSG ルールを適用する
Service Fabric マネージド クラスターを使用すると、展開テンプレートのクラスター リソース内で NSG ルールを直接割り当てることができます。

NSG ルールを割り当てるには、*Microsoft.ServiceFabric/managedclusters* リソース (バージョン `2021-05-01` 以降) の [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) プロパティを使用します。 次に例を示します。

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "properties": {
    "networkSecurityRules": [
      {
        "name": "AllowCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33000-33499",
        "access": "Allow",
        "priority": 2001,
        "direction": "Inbound"
      },
      {
        "name": "AllowARM",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "AzureResourceManager",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33500-33699",
        "access": "Allow",
        "priority": 2002,
        "direction": "Inbound"
      },
      {
        "name": "DenyCustomers",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "destinationPortRange": "33700-33799",
        "access": "Deny",
        "priority": 2003,
        "direction": "Outbound"
      },
      {
        "name": "DenyRDP",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "destinationPortRange": "3389",
        "access": "Deny",
        "priority": 2004,
        "direction": "Inbound",
        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
      }
    ],
    "fabricSettings": [
      "..."
    ]
  }
}
```

## <a name="clientconnection-and-httpgatewayconnection-default-and-optional-rules"></a>ClientConnection と HttpGatewayConnection の既定およびオプションの規則
### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG ルール: SFMC_AllowServiceFabricGatewayToSFRP

既定の NSG ルールが追加され、Service Fabric リソース プロバイダーはクラスターの clientConnectionPort と httpGatewayConnectionPort にアクセスできるようになります。 このルールにより、サービス タグ "ServiceFabric" でのポートへのアクセスが許可されます。

>[!NOTE]
>このルールは常に追加され、オーバーライドすることはできません。

```json
{
    "name": "SFMC_AllowServiceFabricGatewayToSFRP",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.",
        "protocol": "TCP",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "ServiceFabric",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 500,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG ルール: SFMC_AllowServiceFabricGatewayPorts

このオプションのルールを使用することで、お客様は、SFX にアクセスしたり、PowerShell を使用してクラスターに接続したり、clientConnectionPort と httpGatewayPort の LB ポートを開いてインターネットから Service Fabric クラスター API エンドポイントを使用したりできます。

>[!NOTE]
>同じポートに同じアクセス、方向、プロトコルの値を持つカスタム ルールがある場合、このルールは追加されません。 このルールは、カスタム NSG ルールでオーバーライドできます。

```json
{
    "name": "SFMC_AllowServiceFabricGatewayPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3001,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

<a id="rdp"></a>
## <a name="enable-access-to-rdp-ports-from-internet"></a>インターネットから RDP ポートへのアクセスを有効にする

Service Fabric マネージド クラスターを使用して、インターネットから RDP ポートへの受信アクセスを既定で有効にすることはできません。 Service Fabric マネージド クラスター リソースで次のプロパティを設定することにより、インターネットから RDP ポートへの受信アクセスを開くことができます。

```json
"allowRDPAccess": true
```

AllowRDPAccess プロパティを true に設定すると、次の NSG ルールがクラスターのデプロイに追加されます。

```json
{
    "name": "SFMC_AllowRdpPort",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

Service Fabric マネージド クラスターにより、ノード タイプのインスタンスごとにインバウンド NAT 規則が自動的に作成されます。 特定のインスタンス (クラスター ノード) に到達するためのポート マッピングを見つけるには、次の手順のようにします。

Azure portal を使用して、リモート デスクトップ プロトコル (RDP) 用にマネージド クラスターで作成されたインバウンド NAT 規則を見つけます。

1. サブスクリプション内にある、SFC_{cluster-id} という形式の名前が付けられたマネージド クラスター リソース グループに移動します

2. そのクラスターに対する、LB-{cluster-name} という形式のロード バランサーを選択します

3. ロード バランサーのページで、インバウンド NAT 規則を選択します。 インバウンド NAT 規則を調べて、ノードでのインバウンド フロントエンド ポートからターゲット ポートへのマッピングを確認します。 

   次のスクリーンショットでは、3 つの異なるノード タイプに対するインバウンド NAT 規則が示されています。

   ![受信 NAT 規則][Inbound-NAT-Rules]

   既定では、Windows クラスターの場合、フロントエンド ポートの割り当ては 50000 から開始し、ターゲット ポートはポート 3389 であり、ターゲット ノードの RDP サービスにマップされます。
   >[!NOTE]
   > BYOLB 機能を使用していて、RDP が必要な場合は、NAT プールをそのように個別に構成する必要があります。 これによって、これらのノード タイプに対して NAT 規則が自動的に作成されることはありません。

4. 特定のノード (スケール セット インスタンス) にリモート接続します。 クラスターまたはその他の構成した資格情報を作成したときに設定したユーザー名とパスワードを使用することができます。

次のスクリーンショットでは、リモート デスクトップ接続を使用した、Windows クラスター内のアプリ (インスタンス 0) ノードへの接続が示されています。

![リモート デスクトップ接続][sfmc-rdp-connect]

<a id="lbconfig"></a>
## <a name="modify-default-load-balancer-configuration"></a>既定のロード バランサー構成を変更する

### <a name="load-balancer-ports"></a>ロード バランサーのポート

Service Fabric マネージド クラスターにより、*ManagedCluster* プロパティの loadBalancingRules セクションで構成されているすべてのロード バランサー (LB) ポートに対する NSG ルールが、既定の優先順位範囲に作成されます。 このルールにより、インターネットからの受信トラフィック用に LB ポートが開かれます。  

>[!NOTE]
>このルールは、オプションの優先順位範囲に追加され、カスタム NSG ルールを追加することによってオーバーライドできます。

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>ロード バランサーのプローブ

Service Fabric マネージド クラスターにより、ファブリック ゲートウェイのポート用、およびマネージド クラスターのプロパティの `loadBalancingRules` セクションで構成されているすべてのポート用のロード バランサー プローブが、自動的に作成されます。

```json
{
  "value": [
    {
        "name": "FabricTcpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19000,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "FabricHttpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "probe1_tcp_8080",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 8080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
            {
                "id": "<>"
            }
        ]
      },
      "type": "Microsoft.Network/loadBalancers/probes"
    }
  ]
}
```

<a id="ipv6"></a>
## <a name="enable-ipv6-preview"></a>IPv6 を有効にする (プレビュー)
IPv6 は、既定ではマネージド クラスターで有効になっていません。 この機能により、フロントエンドのロード バランサーからバックエンド リソースへの完全なデュアル スタックの IPv4 と IPv6 の機能が有効になります。 マネージド クラスターのロード バランサー構成または NSG 規則に加えた変更は、IPv4 と IPv6 の両方のルーティングに影響します。

> [!NOTE]
> この設定はポータルでは使用できず、クラスター作成後に変更することはできません

1. Service Fabric マネージド クラスター リソースで次のプロパティを設定します。
   ```json
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                "enableIpv6": true
                },
            }
   ```

2. IPv6 対応マネージド クラスターをデプロイします。 必要に応じて[サンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-IPv6)をカスタマイズするか、独自のものを構築します。
   次の例では、`westus` に `MyResourceGroup` という名前のリソース グループを作成し、この機能を有効にしたクラスターをデプロイします。
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```
   デプロイ後、クラスターの仮想ネットワークとリソースはデュアル スタックになります。 その結果、クラスターのフロントエンドのロード バランサーには、Azure ロード バランサーのパブリック IPv6 アドレスと VM 上のプライベート IPv6 アドレスに関連付けられた、一意の DNS アドレス (たとえば `mycluster-ipv6.southcentralus.cloudapp.azure.com`) が作成されます。 


<a id="byovnet"></a>
## <a name="bring-your-own-virtual-network-preview"></a>Bring Your Own Virtual Network (プレビュー)
この機能を使用すると、マネージド クラスターのリソースのデプロイ先となる専用サブネットを指定することで、お客様は既存の仮想ネットワークを使用できます。 これは、必要な関連するセキュリティ ポリシーとトラフィック ルーティングを適用した、構成済みの VNet とサブネットが既にある場合に便利です。 既存の仮想ネットワークへのデプロイ後、Azure ExpressRoute、Azure VPN Gateway、ネットワーク セキュリティ グループ、仮想ネットワーク ピアリングなどの他のネットワーク機能を簡単に使用したり組み込んだりできます。 また、必要に応じて [Bring Your Own Azure Load Balancer](#byolb) も使用できます。

> [!NOTE]
> BYOVNET を使用した場合、マネージド クラスター リソースは 1 つのサブネットにデプロイされます。 

> [!NOTE]
> この設定は、クラスターが作成され、指定したサブネットにマネージド クラスターで NSG が割り当てられると、変更できません。 NSG の割り当てをオーバーライドしないでください。そうしないとトラフィックが中断するおそれがあります。


**Bring Your Own Virtual Network を使用するには:**

1. Service Fabric リソース プロバイダー アプリケーションのサブスクリプションからサービスの `Id` を取得します。
   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > 正しいサブスクリプションを使用していることを確認します。サブスクリプションが別のテナントにある場合は、プリンシパル ID が変わります。

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   後の手順で使用するため、上記の出力の **Id** を **principalId** としてメモしておきます

   |ロール定義名|ロール定義 ID|
   |----|-------------------------------------|
   |Network Contributor|4d97b98b-1d4f-4787-a291-c67834d212e7|

   `Role definition name` と `Role definition ID` のプロパティ値は後の手順で使用するので、メモしておいてください

2. Service Fabric リソース プロバイダー アプリケーションにロールの割り当てを追加します。 ロールの割り当ての追加は、1 回限りの操作です。 ロールを追加するには、次の PowerShell コマンドを実行するか、以下に示す Azure Resource Manager (ARM) テンプレートを構成します。 

   以下の手順では、ExistingRG リソース グループにある ExistingRG-vnet という名前の既存の仮想ネットワークを使用します。 サブネットの名前は "default" です。

   既存の VNet から必要な情報を取得します。

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzVirtualNetwork -Name ExistingRG-vnet -ResourceGroupName ExistingRG
   ```
   後の手順で使用するため、応答の `Subnets` セクションから返される次のサブネット名と `Id` プロパティ値をメモしておきます。

   ```JSON
   Subnets:[
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/virtualNetworks/ExistingRG-vnet/subnets/default"
   }]
   ```

   プリンシパル ID、手順 2 のロール定義名、および上記で取得した割り当てスコープ `Id` を使用して、次の PowerShell コマンドを実行します。
   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
   ```

   または、`principalId`、`roleDefinitionId`、`vnetName`、および `subnetName` に適切な値が構成された Azure Resource Manager (ARM) テンプレートを使用して、ロールの割り当てを追加することもできます。

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('VNetRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > VNetRoleAssignmentID は [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16) である必要があります。 このロールの割り当てを含むテンプレートを再びデプロイする場合は、GUID が最初に使用したものと同じであることを確認します。 作成が必要なのは 1 回だけなので、これを分離して実行するか、デプロイ後にクラスター テンプレートからこのリソースを削除することをお勧めします。

   この手順に使用できる、[VNet サブネットを作成してロールの割り当てを行う Azure Resource Manager (ARM) テンプレート](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOVNET/createVNet-assign-role.json)のサンプル全体を次に示します。

3. 次に示すように、ロールを設定した後で、クラスター デプロイの `subnetId` プロパティを構成します。

   ```JSON
    "resources": [
        {
            "apiVersion": "2021-07-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            },
            "properties": {
                "subnetId": "subnetId",
            ...
            }
   ```
   [Bring Your Own VNet クラスターのサンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOVNET)を参照するか、独自のものをカスタマイズします。

4. 構成したマネージド クラスターの Azure Resource Manager (ARM) テンプレートをデプロイします。

   次の例では、`westus` に `MyResourceGroup` という名前のリソース グループを作成し、この機能を有効にしたクラスターをデプロイします。
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   Bring Your Own VNet サブネットを使用する場合でも、パブリック エンドポイントはリソース プロバイダーによって作成および管理されますが、構成されたサブネット内にあります。 この機能を使用して Azure ロード バランサーでパブリック IP を指定することや静的 IP を再利用することはできません。 [Bring Your Own Azure Load Balancer](#byolb) は、この機能と組み合わせて使用するか、ネイティブでサポートされていないそれらまたは他のロード バランサー シナリオを必要とする場合は、単独で使用できます。

<a id="byolb"></a>
## <a name="bring-your-own-azure-load-balancer-preview"></a>Bring Your Own Azure Load Balancer (プレビュー)
マネージド クラスターで、プライマリとセカンダリの両方のノード タイプ用に、静的パブリック IP を使用して Azure ロード バランサーと完全修飾のドメイン名が作成されます。 この機能を使用すると、受信と送信の両方のトラフィック用に、セカンダリ ノード タイプの Azure ロード バランサーを作成または再利用できます。 Bring Your Own Azure Load Balancer を使用すると、次のことができます。

* プライベートまたはパブリックのトラフィック用に事前構成済みのロード バランサーの静的 IP アドレスを使用する
* ロード バランサーを特定のノード タイプにマップする
* 各ノード タイプは一意の NSG を持つ独自のサブネットにデプロイされるので、ノード タイプごとにネットワーク セキュリティ グループの規則を構成します 
* 既存のポリシーとコントロール (適用されている場合) を維持する

> [!NOTE]
> BYOVNET を使用すると、ロード バランサーを追加で構成した場合でも、マネージド クラスターリソースは 1 つの NSG を備えた 1 つのサブネットにデプロイされます。

> [!NOTE]
> あるノード タイプのクラスターのデプロイ後に既定からカスタムに切り替えることはできませんが、デプロイ後にカスタム ロード バランサーの構成を変更することはできます。

**機能の要件**
 * Basic と Standard の SKU Azure Load Balancer タイプがサポートされています
 * バックエンドおよび NAT プールが既存の Azure ロード バランサーに構成されている必要があります。 例については、[こちらにある作成とロールの割り当てのサンプル](https://raw.githubusercontent.com/Azure-Samples/service-fabric-cluster-templates/master/SF-Managed-Standard-SKU-2-NT-BYOLB/createlb-and-assign-role.json)全体を参照してください。 

これを使用したお客様のシナリオの例を次に示します。

この例では、お客様は、既存の静的 IP アドレスが構成された既存の Azure ロード バランサーを 2 つのノード タイプにルーティングしたいと考えています。
![Bring Your Own Load Balancer の例 1][sfmc-byolb-example-1]

この例では、お客様は既存の Azure ロード バランサーを使用してトラフィックをルーティングし、個別のノード タイプで動作するアプリケーションへのトラフィック フローを個別に管理したいと考えています。 この例のように設定すると、各ノード タイプは、管理できる独自の NSG の背後に置かれます。
![Bring Your Own Load Balancer の例 2][sfmc-byolb-example-2]

Bring Your Own Load Balancer を構成するには:

1. Service Fabric リソース プロバイダー アプリケーションのサブスクリプションからサービス `Id` を取得します。

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
   ```

   > [!NOTE]
   > 正しいサブスクリプションを使用していることを確認します。サブスクリプションが別のテナントにある場合は、プリンシパル ID が変わります。

   ```powershell
   ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
   ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
   ObjectType            : ServicePrincipal
   DisplayName           : Azure Service Fabric Resource Provider
   Id                    : 00000000-0000-0000-0000-000000000000
   ```

   後の手順で使用するため、上記の出力の **Id** を **principalId** としてメモしておきます

   |ロール定義名|ロール定義 ID|
   |----|-------------------------------------|
   |Network Contributor|4d97b98b-1d4f-4787-a291-c67834d212e7|

   `Role definition name` と `Role definition ID` のプロパティ値は後の手順で使用するので、メモしておいてください

2. Service Fabric リソース プロバイダー アプリケーションにロールの割り当てを追加します。 ロールの割り当ての追加は、1 回限りの操作です。 ロールを追加するには、次の PowerShell コマンドを実行するか、以下に示す Azure Resource Manager (ARM) テンプレートを構成します。

   以下の手順では、Existing-RG リソース グループにある Existing-LoadBalancer1 という名前の既存のロード バランサーを使用します。

   既存の Azure ロード バランサーから必須の `Id` プロパティの情報を取得します。 次のことを行います。 

   ```powershell
   Login-AzAccount
   Select-AzSubscription -SubscriptionId <SubId>
   Get-AzLoadBalancer -Name "Existing-LoadBalancer1" -ResourceGroupName "Existing-RG"
   ```
   次の手順で使用する、次の `Id` をメモします。
   ```JSON
   {
   ...
   "Id": "/subscriptions/<subscriptionId>/resourceGroups/Existing-RG/providers/Microsoft.Network/loadBalancers/Existing-LoadBalancer1"
   }
   ```
   プリンシパル ID、手順 2 のロール定義名、および今取得した割り当てスコープ `Id` を使用して、次の PowerShell コマンドを実行します。

   ```powershell
   New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Network Contributor" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/loadBalancers/<LoadBalancerName>"
   ```

   あるいは、`principalId` (手順 1. で取得)、`loadBalancerRoleAssignmentID`、および `roleDefinitionId` に適切な値が構成された Azure Resource Manager (ARM) テンプレートを使用して、ロールの割り当てを追加することもできます。

   ```JSON
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2020-04-01-preview",
      "name": "[parameters('loadBalancerRoleAssignmentID')]",
      "scope": "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]",
      "dependsOn": [
        "[concat('Microsoft.Network/loadBalancers/', variables('lbName'))]"
      ],
      "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7')]",
        "principalId": "00000000-0000-0000-0000-000000000000"
      }
   ```
   > [!NOTE]
   > loadBalancerRoleAssignmentID は [GUID](../azure-resource-manager/templates/template-functions-string.md#examples-16) である必要があります。 このロールの割り当てを含むテンプレートを再びデプロイする場合は、GUID が最初に使用したものと同じであることを確認します。 作成が必要なのは 1 回だけなので、これを分離して実行するか、デプロイ後にクラスター テンプレートからこのリソースを削除することをお勧めします。

3. 必要な送信接続を構成します。 すべてのノードは、ポート 443 で ServiceFabric リソース プロバイダーへの送信をルーティングできる必要があります。 NSG で `ServiceFabric` サービス タグを使用して、トラフィックの送信先を Azure エンドポイントに制限できます。

4. 必要に応じて、既存の Azure ロード バランサーで受信アプリケーション ポートと関連プローブを構成します。

5. 必要に応じて、ノード タイプに適用されるマネージド クラスター NSG ルールを構成し、Azure ロード バランサーで構成した必要なトラフィックを許可します。そうしないと、トラフィックがブロックされます。

   インバウンド規則を開く方法の例については、[Bring Your Own Load Balancer の Azure Resource Manager (ARM) サンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-BYOLB)を参照してください。

6. 構成したマネージド クラスターの ARM テンプレートをデプロイする

   次の例では、`westus` に `MyResourceGroup` という名前のリソース グループを作成し、この機能を有効にしたクラスターをデプロイします。
   ```powershell
    New-AzResourceGroup -Name MyResourceGroup -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName MyResourceGroup -TemplateFile AzureDeploy.json
   ```

   デプロイ後、受信と送信のトラフィック用に指定されたロード バランサーを使用するようにセカンダリ ノード タイプが構成されます。 Service Fabric のクライアント接続およびゲートウェイ エンドポイントは、マネージド クラスターのプライマリ ノード タイプの静的 IP アドレスのパブリック DNS を引き続き指します。


## <a name="next-steps"></a>次のステップ
[Service Fabric マネージド クラスターの構成オプション](how-to-managed-cluster-configuration.md)
[Service Fabric マネージド クラスターの概要](overview-managed-cluster.md)

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png
[sfmc-byolb-example-1]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-1.png
[sfmc-byolb-example-2]: ./media/how-to-managed-cluster-networking/sfmc-byolb-scenario-2.png

