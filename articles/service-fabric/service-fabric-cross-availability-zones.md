---
title: Availability Zones をまたがるクラスターのデプロイ
description: Availability Zones をまたがる Azure Service Fabric クラスターを作成する方法について説明します。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 9cc2a9d189e7a781dc6ba64a65af022150392485
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727764"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Availability Zones をまたがる Azure Service Fabric クラスターのデプロイ
Azure の Availability Zones は高可用性を備えたサービスで、アプリケーションとデータをデータセンターの障害から保護します。 可用性ゾーンは、Azure リージョン内に独立した電源、冷却手段、ネットワークを備えた一意の物理的な場所です。

Service Fabric では、特定のゾーンに固定されるノード タイプをデプロイすることによって、Availability Zones をまたがるクラスターをサポートします。 これにより、アプリケーションの高可用性が確保されます。 Azure Availability Zones は一部のリージョンでのみ使用できます。 詳細については、「[Azure の Availability Zones の概要](../availability-zones/az-overview.md)」をご覧ください。

サンプル テンプレートを使用できます。[Service Fabric クロス可用性ゾーン テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Availability Zones をまたがる Azure Service Fabric クラスターのプライマリ ノードのタイプに推奨されるトポロジ
Availability Zones 間で分散された Service Fabric クラスターでは、クラスター状態の高可用性が確保されます。 Service Fabric クラスターをゾーン間にまたがらせるには、リージョンでサポートされている各可用性ゾーンにプライマリ ノード タイプを作成する必要があります。 これにより、各プライマリ ノード タイプ間にシード ノードが均等に分散されます。

プライマリ ノード タイプの推奨されるトポロジでは、下記のリソースが必要です。

* Platinum に設定されたクラスター信頼性レベル。
* プライマリとしてマークされた 3 つのノード タイプ。
    * 各ノード タイプは異なるゾーンに配置されているその独自の仮想マシン スケール セットにマップされている必要があります。
    * 各仮想マシン スケール セットには少なくとも 5 つのノード (Silver 耐久性) が必要です。
* Standard SKU を使用した単一のパブリック IP リソース。
* Standard SKU を使用した単一のロード バランサー リソース。
* 仮想マシン スケール セットをデプロイするサブネットによって参照されている NSG。

>[!NOTE]
> 仮想マシン スケール セットの単一の配置グループ プロパティを true に設定する必要があります。

Azure Service Fabric 可用性ゾーンのアーキテクチャを示す図 ![Azure Service Fabric 可用性ゾーンのアーキテクチャを示す図。][sf-architecture]

ゾーンにまたがる仮想マシン スケール セット内の FD と UD の形式を示すサンプル ノード リスト

 ![ゾーンにまたがる仮想マシン スケール セット内の FD と UD の形式を示すサンプル ノード リスト。][sf-multi-az-nodes]

**ゾーン間でのサービス レプリカの分散**: ゾーンにまたがる nodeTypes にサービスがデプロイされている場合、レプリカが必ず別々のゾーンに存在するように配置されます。 これらの各 nodeTypes に存在するノード上の障害ドメインはゾーン情報を使用して構成されているため、このことが保証されます (つまり、 FD = fd:/zone1/1 ... といった具合です)。 たとえば、サービスに 5 個のレプリカまたはインスタンスがある場合、分散は 2-2-1 となり、ランタイムは AZ 間での均等な分散を試みます。

**ユーザー サービス レプリカの構成**: クロス可用性ゾーン nodeTypes にデプロイされたステートフル ユーザー サービスは、レプリカ数のターゲット値 9、最小値 5 という構成値を使用して構成する必要があります。 この構成は、1 つのゾーンがダウンした場合でも 6 つのレプリカが他の 2 つのゾーンで稼働しているため、サービスを機能させることができます。 このようなシナリオでは、アプリケーションのアップグレードも実行できます。

**クラスターの ReliabilityLevel**: これにより、クラスター内のシード ノードの数と、システム サービスのレプリカ サイズも定義されます。 ゾーンの回復性が有効になるように、ノードがゾーンにまたがって分散されるため、クロス可用性ゾーンのセットアップではより多くのノードを使用します。このため、高い信頼性値によってより多くのシード ノードとシステム サービス レプリカが存在することになり、ゾーン間で均等に分散されることが保証されます。その結果、ゾーンで障害が発生した場合に、クラスターとシステム サービスへの影響を回避できます。 "ReliabilityLevel = Platinum" を使用すると、クラスター内のゾーン間に 9 つのシード ノードが分散され、各ゾーンに 3 つのシードが割り当てられるため、クロス可用性ゾーンのセットアップにはこれをお勧めします。

**ゾーン ダウンのシナリオ**: ゾーンがダウンすると、そのゾーン内のすべてのノードがダウン状態として表示されます。 これらのノードのサービス レプリカもダウンします。 他のゾーンにレプリカがあるため、正常に動作しているゾーンにプライマリ レプリカがフェールオーバーすることで、サービスは引き続き応答します。 ターゲット レプリカ数にまだ到達しておらず、かつ VM 数が最小ターゲット レプリカ サイズを上回っているため、サービスは警告状態として表示されます。 その後、Service Fabric ロード バランサーは、構成されているターゲット レプリカの数と一致するように、作業ゾーンでレプリカを起動します。 この時点で、サービスは正常と表示されます。 ダウンしたゾーンが復帰すると、負荷分散によってすべてのサービス レプリカがすべてのゾーンに均等に分散されます。

## <a name="networking-requirements"></a>ネットワーク要件
### <a name="public-ip-and-load-balancer-resource"></a>パブリック IP とロード バランサーのリソース
仮想マシン スケール セット リソースに対するゾーン プロパティを有効にするには、その仮想マシン スケール セットによって参照されているロード バランサーと IP リソースの両方で *Standard* SKU が使用されている必要があります。 SKU プロパティを使用せずにロード バランサーまたは IP リソースを作成すると、Availability Zones をサポートしない Basic SKU が作成されます。 Standard SKU ロード バランサーは既定で外部からのすべてのトラフィックをブロックします。外部トラフィックを許可するには、NSG をサブネットにデプロイする必要があります。

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> パブリック IP およびロード バランサー リソースへの SKU のインプレース変更を行うことはできません。 Basic SKU のある既存のリソースから移行する場合は、この記事の移行のセクションを参照してください。

### <a name="virtual-machine-scale-set-nat-rules"></a>仮想マシン スケール セットの NAT 規則
ロード バランサーのインバウンド NAT 規則は、仮想マシン スケール セットの NAT プールと一致する必要があります。 各仮想マシン スケール セットに一意のインバウンド NAT プールが必要です。

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard SKU Load Balancer のアウトバウンド規則
Standard Load Balancer および Standard パブリック IP では、Basic SKU を使用する場合と比較して、アウトバウンド接続に新しい機能とさまざまな動作が導入されています。 Standard SKU を操作するときにアウトバウンド接続が必要な場合は、Standard パブリック IP アドレスまたは Standard パブリック Load Balancer で明示的に定義する必要があります。 詳細については、[アウトバウンド接続](../load-balancer/load-balancer-outbound-connections.md)と [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) に関するページを参照してください。

>[!NOTE]
> 標準テンプレートでは、既定ですべてのアウトバウンド トラフィックを許可する NSG が参照されます。 インバウンド トラフィックは、Service Fabric 管理操作に必要なポートに制限されます。 NSG 規則は、要件に合わせて変更できます。

>[!NOTE]
> Standard SKU SLB を使用しているすべての Service Fabric クラスターでは、ポート 443 での送信トラフィックを許可する規則を確実に各ノード タイプに設定する必要があります。 これはクラスターの設定を完了するために必要であり、このルールのないデプロイは失敗します。


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>仮想マシン スケール セットでのゾーンの有効化
ゾーンを有効にするには、仮想マシン スケール セットで、仮想マシン スケール セット リソースに次の 3 つの値を含める必要があります。

* 最初の値は、**zones** プロパティで、仮想マシン スケール セットをデプロイする可用性ゾーンを指定します。
* 2 つ目の値は、"singlePlacementGroup" プロパティで、true に設定する必要があります。
* 3 つ目の値は、Service Fabric 仮想マシン スケール セット拡張の "faultDomainOverride" プロパティです。 このプロパティの値には、この仮想マシン スケール セットを配置するゾーンのみを含める必要があります。 例: "faultDomainOverride": "az1" Azure Service Fabric クラスターにはクロス リージョンのサポートがないため、すべての仮想マシン スケール セット リソースを同じリージョンに配置する必要があります。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Service Fabric クラスター リソースでの複数のプライマリ ノード タイプの有効化
クラスター リソースにプライマリとして 1 つまたは複数のノード タイプを設定するには、"isPrimary" プロパティを "true" に設定します。 Availability Zones をまたがって Service Fabric クラスターをデプロイする場合、個別のゾーンに 3 つのノードのタイプが必要です。

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Basic SKU Load Balancer と Basic SKU IP を使用したクラスターから Availability Zones の使用に移行する
Basic SKU で Load Balancer と IP を使用していたクラスターを移行するには、まず Standard SKU を使用したまったく新しい Load Balancer と IP リソースを作成する必要があります。 これらのリソースをインプレースで更新することはできません。

使用する新しいクロス可用性ゾーン ノードのタイプで、新しい LB と IP が参照されている必要があります。 上の例では、3 つの新しい仮想マシン スケール セットのリソースがゾーン 1、2、および 3 に追加されています。 これらの仮想マシン スケール セットは、新しく作成された LB と IP を参照し、Service Fabric クラスター リソース内でプライマリ ノードのタイプとしてマークされます。

まず、既存の Resource Manager テンプレートに新しいリソースを追加する必要があります。 これらのリソースには次が含まれます。
* Standard SKU を使用したパブリック IP リソース。
* Standard SKU を使用した Load Balancer リソース。
* 仮想マシン スケール セットをデプロイするサブネットによって参照されている NSG。
* プライマリとしてマークされた 3 つのノード タイプ。
    * 各ノード タイプは異なるゾーンに配置されているその独自の仮想マシン スケール セットにマップされている必要があります。
    * 各仮想マシン スケール セットには少なくとも 5 つのノード (Silver 耐久性) が必要です。

これらのリソースの例については、[サンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)を参照してください。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

リソースのデプロイが完了したら、元のクラスターから、プライマリ ノード タイプのノードを無効にしていくことができます。 ノードを無効にすると、システム サービスが、上記の手順でデプロイされた新しいプライマリ ノード タイプに移行されます。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

ノードをすべて無効にすると、ゾーンに分散されたプライマリ ノード タイプで、システム サービスが実行されます。 その後、無効にしたノードをクラスターから削除できます。 ノードを削除したら、元の IP、Load Balancer、および仮想マシン スケール セット リソースを削除できます。

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

さらに、デプロイした Resource Manager テンプレートから、これらのリソースへの参照を削除する必要があります。

最後の手順には、DNS 名とパブリック IP の更新が含まれます。

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>(プレビュー) 単一の仮想マシン スケール セットで、複数の可用性ゾーンを有効にする

前述のソリューションでは、AZ ごとに 1 つの nodeType が使用されています。 次のソリューションでは、ユーザーは、同じ nodeType に 3 つの AZ をデプロイできます。

**この機能は現在プレビュー段階であるため、現在のところ、運用環境ではサポートされていません。**

完全なサンプル テンプレートは、[ここ](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)にあります。

![Azure Service Fabric 可用性ゾーンのアーキテクチャ][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>仮想マシン スケール セットでのゾーンの構成
仮想マシン スケール セットでゾーンを有効にするには、仮想マシン スケール セット リソースに次の 3 つの値を含める必要があります。

* 最初の値は、**zones** プロパティで、仮想マシン スケール セットに存在する Availability Zones を指定します。
* 2 つ目の値は、"singlePlacementGroup" プロパティで、true に設定する必要があります。 **3 つの AZ にまたがるスケールセットでは、"singlePlacementGroup = true" の場合でも、最大 300 VM までスケールアップできます。**
* 3 つ目の値は、"zoneBalance" です。これを指定すると、厳密なゾーン バランシングが確実に行われます。 これは "true" に設定する必要があります。 これにより、ゾーン間での VM の分散が不均衡になることがなくなり、いずれかのゾーンがダウンしたときに、他の 2 つのゾーンに十分な VM が確保され、クラスターは中断されることなく実行され続けます。 VM の分散が不均衡なクラスターの場合、ゾーンがダウンしたときに、そのゾーンにほとんどの VM が含まれている可能性があるため、中断される可能性があります。 ゾーン間で VM の分散が不均衡になっていると、サービス配置関連の問題が発生したり、インフラストラクチャの更新がスタックしたりすることもあります。 [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing) について確認します。
* FaultDomain と UpgradeDomain のオーバーライドは、構成する必要はありません。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **Service Fabric クラスターには少なくとも 1 つのプライマリ nodeType が必要です。プライマリ nodeTypes の DurabilityLevel は、Silver 以上である必要があります。**
> * AZ 拡張仮想マシン スケール セットは、durabilityLevel に関係なく、少なくとも 3 つの可用性ゾーンで構成する必要があります。
> * AZ 拡張仮想マシン スケール セットには、持続性が Silver の VM が少なくとも 15 台必要です。
> * AZ 拡張仮想マシン スケール セットには、持続性が Bronze の VM が少なくとも 6 台必要です。

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Service Fabric nodeType での複数ゾーンのサポートの有効化
複数の可用性ゾーンをサポートするために Service Fabric nodeType を有効にする必要があります。

* 最初の値は **multipleAvailabilityZones** であり、nodeType には true に設定する必要があります。
* 2 番目の値は **sfZonalUpgradeMode** であり、省略可能です。 AZ が複数ある nodeType がクラスター内に既に存在する場合、このプロパティを変更することはできません。
  プロパティは、アップグレード ドメイン内の VM の論理グループ化を制御します。
  **値が "Parallel" に設定されている場合:** nodeType の下にある VM は、5 UD のゾーン情報を無視して UD にグループ化されます。 この結果、すべてのゾーンの UD0 が同時にアップグレードされます。 このデプロイ モードはアップグレードの場合は高速ですが、更新プログラムを一度に 1 ゾーンずつ適用する必要があるとしている SDP のガイドラインに沿っていないため、推奨されません。
  **値が省略されているか、"Hierarchical" に設定されている場合:** VM は、最大 15 の UD に、ゾーン分布を反映するようにグループ化されます。 3 つのゾーンそれぞれに 5 つの UD があります。 これにより、更新はゾーンごとに実行され、1 つ目のゾーン内で 5 UD が完了して初めて次のゾーンへと移動し、ゆっくりと 15 UD (3 ゾーン、5 UD) すべてが更新されます。クラスターとユーザー アプリケーションの観点からは、こちらの方がより安全です。
  このプロパティは、ServiceFabric アプリケーションおよびコード アップグレードのアップグレード動作を定義するだけです。 基になる仮想マシン スケール セットのアップグレードは、引き続きすべての AZ で並列に実行されます。
  このプロパティは、複数のゾーンが有効になっていないノード型の UD 分布には影響しません。
* 3 つ目の値は **vmssZonalUpgradeMode = Parallel** です。 これは、複数の AZ を持つ nodeType が追加された場合にクラスターで構成される *必須* のプロパティです。 このプロパティは、一度にすべての AZ で並列に発生する仮想マシン スケール セットの更新のアップグレード モードを定義します。
  現時点では、このプロパティは Parallel にのみ設定できます。
* Service Fabric クラスター リソース apiVersion は、"2020-12-01-preview" 以上である必要があります。
* クラスター コード バージョンが "7.2.445" 以上である必要があります。

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * パブリック IP と Load Balancer リソースは、この記事で既に説明したように、Standard SKU を使用する必要があります。
> * nodeType の "multipleAvailabilityZones" プロパティは、nodeType の作成時にのみ定義できます。後で変更することはできません。 このため、既存の nodeTypes をこのプロパティで構成することはできません。
> * "SfZonalUpgradeMode" を省略した場合、または "Hierarchical" に設定した場合、クラスター内のアップグレード ドメインが増えるにつれて、クラスターとアプリケーションのデプロイ速度が低下します。 15 のアップグレード ドメインのアップグレード期間に組み入れるようにアップグレード ポリシーのタイムアウトを適切に調整することが重要です。 アプリとクラスターの両方のアップグレード ポリシーを更新して、デプロイが Azure リソース サービスのデプロイ タイムアウト (12 時間) を超えないようにする必要があります。 つまり、デプロイに、15 UD で 12 時間を超えないようにする必要があるので、 UD あたり 40 分以内ということになります。
> * 1 ゾーン ダウン シナリオでクラスターが存続できるようにするには、クラスターを **reliabilityLevel = Platinum** に設定してください。

>[!NOTE]
> ベスト プラクティスとして、sfZonalUpgradeMode を Hierarchical に設定するか、省略することをお勧めします。 デプロイは、影響を与えるレプリカやインスタンスを減らす VM のゾーン分布に従って、それらの安全性を高めます。
> デプロイ速度を優先する場合、またはステートレス ワークロードだけが複数の AZ を持つノードの種類で実行される場合は、sfZonalUpgradeMode を Parallel に設定します。 これにより、UD ウォークがすべて AZ で並列に実行されます。

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>複数の Availability Zones を持つノードの種類への移行
すべての移行シナリオで、複数の可用性ゾーンがサポートされる新しい nodeType を追加する必要があります。 既存の nodeType を移行して複数のゾーンをサポートすることはできません。
[こちら](./service-fabric-scale-up-primary-node-type.md)の記事には、新しい nodeType を追加し、さらに IP や LB リソースなどの新しい nodeType に必要な他のリソースを追加する詳細な手順が記載されています。 同じ記事には、複数の可用性ゾーンを持つ nodeType がクラスターに追加された後、既存の nodeType をインベントリから削除する方法も記載されています。

* 基本的な LB および IP リソースを使用している nodeType からの移行:これは、AZ ごとに 1 つのノードの種類を持つソリューションについて、[ここで](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip)既に説明されています。 
    新しいノード の種類の場合、唯一の違いは、AZ ごとに 1 つではなく、すべての AZ について仮想マシン スケール セットが 1 つ、ノードの種類が 1 つだけであるということです。
* 標準 SKU LB および IP リソースを NSG で使用している nodeType からの移行: 前に説明したものと同じ手順に従いますが、新しい LB、IP、NSG リソースを追加する必要はなく、同じリソースを新しい nodeType で再利用できます。


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png
