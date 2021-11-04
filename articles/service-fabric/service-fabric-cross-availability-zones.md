---
title: Availability Zones をまたがるクラスターのデプロイ
description: Availability Zones をまたがる Azure Service Fabric クラスターを作成する方法について説明します。
author: peterpogorski
ms.topic: conceptual
ms.date: 05/24/2021
ms.author: pepogors
ms.openlocfilehash: 76232db15084f587181c7ff20821ebb0d5bdf362
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075089"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Availability Zones をまたがる Azure Service Fabric クラスターのデプロイ

Azure の Availability Zones は高可用性を備えたサービスで、アプリケーションとデータをデータセンターの障害から保護します。 可用性ゾーンは、Azure リージョン内に独立した電源、冷却手段、ネットワークを備えた一意の物理的な場所です。

複数の Availability Zones にまたがるクラスターをサポートするために、Azure Service Fabric では、次の記事で説明する 2 つの構成方法が用意されています。 Availability Zones は一部のリージョンでのみ使用できます。 詳細については、「[Azure のリージョンと Availability Zones](../availability-zones/az-overview.md)」を参照してください。

サンプル テンプレートは、[Service Fabric クロス可用性ゾーン テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)に関するページで入手できます。

## <a name="recommended-topology-for-spanning-a-primary-node-type-across-availability-zones"></a>Availability Zones をまたがるプライマリ ノードのタイプに推奨されるトポロジ

* `Platinum` に設定されたクラスター信頼性レベル
* Standard SKU を使用した単一のパブリック IP リソース
* Standard SKU を使用した単一のロード バランサー リソース
* 仮想マシン スケール セットをデプロイするサブネットで参照されているネットワーク セキュリティ グループ (NSG)

>[!NOTE]
>仮想マシン スケール セットの単一の配置グループ プロパティを `true` に設定する必要があります。

次のサンプル ノード リストには、ゾーンにまたがる仮想マシン スケール セット内の FD と UD の形式が示されています。

![ゾーンにまたがる仮想マシン スケール セット内の FD と UD の形式のサンプル ノード リストを示すスクリーンショット。][sf-multi-az-nodes]

### <a name="distribution-of-service-replicas-across-zones"></a>ゾーン間でのサービス レプリカの分布

サービスが可用性ゾーンにまたがるノード タイプにデプロイされている場合は、レプリカは別々のゾーンに分かれるように配置されます。 これらの各ノード タイプのノードに関する障害ドメインは、ゾーン情報 (つまり、FD = fd:/zone1/1 など) で構成されます。 たとえば、5 つのレプリカまたはサービス インスタンスの場合、分布は 2-2-1 となり、ランタイムはゾーン間での均等な分布を試みます。

### <a name="user-service-replica-configuration"></a>ユーザー サービス レプリカの構成

可用性ゾーンにまたがるノード タイプにデプロイされたステートフル ユーザー サービスは、たとえばレプリカ数のターゲット値が 9 で、最小値が 5 になるように構成する必要があります。 この構成では、1 つのゾーンがダウンした場合でも、6 つのレプリカが他の 2 つのゾーンで稼働するため、サービスを機能させることができます。 このシナリオでのアプリケーションのアップグレードも成功します。

### <a name="cluster-reliabilitylevel"></a>クラスター ReliabilityLevel

この値は、クラスター内のシード ノード数とシステム サービスのレプリカ サイズを定義します。 可用性ゾーン間のセットアップでは、ゾーンの回復性を有効にするためにゾーン間に分散されるノードの数が多くなります。
  
`ReliabilityLevel` 値を高くすると、存在するシード ノードとシステム サービス レプリカが多くなり、ゾーン間で均等に分散されます。この結果、ゾーンが失敗した場合でも、クラスターとシステム サービスに影響が及ばなくなります。 `ReliabilityLevel = Platinum` (推奨) では、クラスター内のゾーン間にまたがる 9 つのシード ノードがあり、各ゾーンに 3 つのシードが含まれます。

### <a name="zone-down-scenario"></a>ゾーンダウン シナリオ

ゾーンがダウンすると、すべてのゾーンとそのゾーンのサービス レプリカがダウン状態として表示されます。 残りのゾーンにレプリカが含まれるため、サービスは応答し続けます。 プライマリ レプリカは、機能しているゾーンにフェールオーバーします。 ターゲット レプリカ数にまだ到達しておらず、仮想マシン (VM) の数がまだターゲット レプリカの最小サイズを上回っているため、サービスは警告状態で表示されます。

Service Fabric ロード バランサーにより、ターゲット レプリカの数と一致するように、作業ゾーンでレプリカが起動されます。 この時点で、サービスは正常と表示されます。 ダウンしたゾーンが復帰すると、ロード バランサーによってすべてのサービス レプリカがゾーンに均等に分散されます。

## <a name="upcoming-optimizations"></a>今後の最適化
* 信頼性の高いインフラストラクチャの更新を提供するために、Service Fabric では、仮想マシン スケール セットの持続性を少なくともシルバーに設定する必要があります。 これにより、基になる仮想マシン スケール セットと Service Fabric ランタイムが、信頼性の高い更新を提供できるようになります。 また、各ゾーンに少なくとも 5 つの VM が必要です。 この要件を緩和して、プライマリ ノード タイプと非プライマリ ノード タイプでそれぞれゾーンごとに 3 つの VM と 2 つの VM にする準備を進めています。
* 以下のすべての構成と今後の作業により、お客様にインプレース移行が提供され、新しい nodeTypes を追加して古い nodeTypes を廃止することによって、新しい構成を使用するように同じクラスターをアップグレードできます。

## <a name="networking-requirements"></a>ネットワーク要件

### <a name="public-ip-and-load-balancer-resource"></a>パブリック IP とロード バランサーのリソース

仮想マシン スケール セット リソースに対して `zones` プロパティを有効にするには、その仮想マシン スケール セットで参照されるロード バランサーと IP リソースの両方で、Standard SKU が使用されることが必要になります。 SKU プロパティを使用せずにロード バランサーまたは IP リソースを作成すると、Availability Zones をサポートしない Basic SKU が作成されます。 Standard SKU ロード バランサーでは、既定で外部からのすべてのトラフィックがブロックされます。 外部トラフィックを許可するには、NSG をサブネットにデプロイします。

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
> パブリック IP およびロード バランサー リソースへの SKU のインプレース変更を行うことはできません。 Basic SKU を持つ既存のリソースから移行する場合は、この記事の移行のセクションを参照してください。

### <a name="nat-rules-for-virtual-machine-scale-sets"></a>仮想マシン スケール セットの NAT 規則

ロード バランサーの受信ネットワーク アドレス変換 (NAT) 規則は、仮想マシン スケール セットの NAT プールと一致している必要があります。 各仮想マシン スケール セットに一意のインバウンド NAT プールが必要です。

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

### <a name="outbound-rules-for-a-standard-sku-load-balancer"></a>Standard SKU ロード バランサーのアウトバウンド規則

Standard SKU ロード バランサーおよびパブリック IP では、Basic SKU を使用する場合と比較して、アウトバウンド接続に新しい機能とさまざまな動作が導入されています。 Standard SKU を操作しているときにアウトバウンド接続が必要な場合は、Standard SKU パブリック IP アドレスまたは Standard SKU ロード バランサーで明示的に定義する必要があります。 詳細については、「[アウトバウンド接続に送信元ネットワーク アドレス変換 (SNAT) を使用する](../load-balancer/load-balancer-outbound-connections.md)」と「[Azure Load Balancer の概要](../load-balancer/load-balancer-overview.md)」を参照してください。

>[!NOTE]
> 標準テンプレートでは、既定ですべてのアウトバウンド トラフィックを許可する NSG が参照されます。 インバウンド トラフィックは、Service Fabric 管理操作に必要なポートに制限されます。 NSG 規則は、要件に合わせて変更できます。

>[!IMPORTANT]
> Standard SKU ロード バランサーを使用する Service Fabric クラスター内の各ノード タイプには、ポート 443 での送信トラフィックを許可する規則が必要です。 これは、クラスターのセットアップを完了するために必要です。 この規則がないデプロイは失敗します。


## <a name="1-preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>1. (プレビュー) 単一の仮想マシン スケール セットで、複数の Availability Zones を有効にする

このソリューションでは、同じノード タイプの 3 つの Availability Zones にまたがらせることができます。 これは、単一の仮想マシン スケール セットを維持しながら、すべての可用性ゾーンにデプロイできるため、推奨されるデプロイ トポロジです。

> [!NOTE]
> この機能は現在プレビュー段階であるため、現在のところ、運用環境のシナリオではサポートされていません。

完全なサンプル テンプレートは [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure) で入手できます。

![Azure Service Fabric 可用性ゾーンのアーキテクチャの図。][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>仮想マシン スケール セットでのゾーンの構成

仮想マシン スケール セットでゾーンを有効にするには、仮想マシン スケール セット リソースに次の 3 つの値を含めます。

* 最初の値は、`zones` プロパティであり、仮想マシン スケール セットにある可用性ゾーンを指定します。
* 2 つ目の値は、`singlePlacementGroup` プロパティであり、`true` に設定する必要があります。 3 つの可用性ゾーンにまたがるスケール セットは、`singlePlacementGroup = true` でも最大 300 の VM に拡張できます。
* 3 つ目の値は、`zoneBalance` です。これを指定すると、厳密なゾーン バランシングが確実に行われます。 この値は `true` である必要があります。 これにより、ゾーン間での VM の分散が不均衡になることがなくなるので、あるゾーンがダウンしたときに、他の 2 つのゾーンに十分な VM が確保され、クラスターは実行し続けられます。

  VM の分散が不均衡なクラスターの場合、ゾーンがダウンしたときに中断される可能性があります。そのゾーンにほとんどの VM が含まれている可能性があるためです。 ゾーン間で VM の分散が不均衡になっていると、サービス配置の問題が発生したり、インフラストラクチャの更新がスタックしたりすることもあります。 [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing) の詳細を確認します。

`FaultDomain` および `UpgradeDomain` のオーバーライドを構成する必要はありません。

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[parameters('vmNodeType1Name')]",
  "location": "[parameters('computeLocation')]",
  "zones": [ "1", "2", "3" ],
  "properties": {
    "singlePlacementGroup": true,
    "zoneBalance": true
  }
}
```

>[!NOTE]
>
> * Service Fabric クラスターには少なくとも 1 つのプライマリ ノード タイプが必要です。 プライマリ ノード タイプの持続性レベルは、シルバー以上である必要があります。
> * 仮想マシン スケール セット間にまたがる可用性ゾーンは、持続性レベルに関係なく、少なくとも 3 つの可用性ゾーンで構成する必要があります。
> * シルバー以上の持続性を備えた仮想マシン スケール セットにまたがる可用性ゾーンには、少なくとも 15 の VM が必要です。
> * ブロンズ持続性を備えた仮想マシン スケール セットにまたがる可用性ゾーンには、少なくとも 6 つの VM が必要です。

### <a name="enable-support-for-multiple-zones-in-the-service-fabric-node-type"></a>Service Fabric ノード タイプでの複数ゾーンのサポートを有効にする

複数の可用性ゾーンをサポートするために、Service Fabric ノード タイプを有効にする必要があります。

* 最初の値は `multipleAvailabilityZones` であり、ノード タイプには `true` に設定する必要があります。
* 2 番目の値は `sfZonalUpgradeMode` であり、省略可能です。 可用性ゾーンが複数あるノード タイプがクラスター内に既に存在する場合、このプロパティを変更することはできません。
  このプロパティは、アップグレード ドメイン (UD) 内の VM の論理グループ化を制御します。

  * この値が `Parallel` に設定されている場合: ノード タイプ下の VM は UD にグループ化され、5 つの UD のゾーン情報は無視されます。 この設定により、すべてのゾーンにまたがる UD が同時にアップグレードされます。 このデプロイ モードはアップグレードの場合は高速ですが、更新プログラムを一度に 1 ゾーンずつ適用する必要があるとしている SDP のガイドラインに沿っていないので、お勧めできません。
  * この値が省略されているか、`Hierarchical` に設定されている場合: VM は、最大 15 の UD でのゾーン分布を反映するようにグループ化されます。 3 つのゾーンそれぞれに 5 つの UD が含められます。 これにより、ゾーンは一度に 1 つずつ更新され、最初のゾーン内で 5 つの UD が完了した後にのみ、次のゾーンに移ります。 この更新プロセスにより、クラスターとユーザー アプリケーションにとって安全性が高まります。

  このプロパティは、Service Fabric アプリケーションおよびコード アップグレードのアップグレード動作を定義するだけです。 基になる仮想マシン スケール セットのアップグレードは、引き続きすべての可用性ゾーンで並列に実行されます。 このプロパティは、複数のゾーンが有効になっていないノード タイプの UD 分布には影響しません。
* 3 つ目の値は `vmssZonalUpgradeMode = Parallel` です。 このプロパティは、複数の可用性ゾーンを備えたノード タイプを追加する場合に必須です。 このプロパティは、一度にすべての可用性ゾーンで行われる仮想マシン スケール セットの更新のアップグレード モードを定義します。

  現在、このプロパティは Parallel にのみ設定できます。

>[!IMPORTANT]
>Service Fabric クラスター リソース API バージョンは、2020-12-01-preview 以降である必要があります。
>
>クラスター コード バージョンは、少なくとも 8.1.321 以降である必要があります。

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
    "sfZonalUpgradeMode": "Hierarchical",
    "vmssZonalUpgradeMode": "Parallel",
    "nodeTypes": [
      {
        "name": "[parameters('vmNodeType0Name')]",
        "multipleAvailabilityZones": true
      }
    ]
  }
}
```

>[!NOTE]
>
> * パブリック IP とロード バランサー リソースでは、この記事で既に説明したように、Standard SKU を使用する必要があります。
> * ノード タイプの `multipleAvailabilityZones` プロパティは、このノード タイプが作成されている場合にのみ定義でき、後から変更することはできません。 既存のノード タイプをこのプロパティで構成することはできません。
> * `sfZonalUpgradeMode` を省略した場合、または `Hierarchical` に設定した場合、クラスター内のアップグレード ドメインが増えるので、クラスターとアプリケーションのデプロイ速度が低下します。 15 のアップグレード ドメインに必要なアップグレード時間が取れるようにアップグレード ポリシーのタイムアウトを適切に調整することが重要です。 アプリとクラスターの両方のアップグレード ポリシーを更新して、デプロイが Azure Resource Service のデプロイ制限時間 (12 時間) を超えないようにする必要があります。 つまり、15 の UD の場合、デプロイには 12 時間を超えないようにする必要があります (つまり、それぞれの UD に対して 40 分を超えないようにする必要があります)。
> * 1 ゾーン ダウン シナリオでクラスターが存続できるようにするには、クラスターの信頼性レベルを `Platinum` に設定します。

>[!TIP]
> `sfZonalUpgradeMode` を `Hierarchical` に設定するか、省略することをお勧めします。 デプロイは、VM のゾーン分布に従い、影響を与えるレプリカやインスタンスの数を減らして、それらの安全性を高めます。
> デプロイ速度を優先する場合、またはステートレス ワークロードだけが、複数の可用性ゾーンを含むノード タイプで実行される場合は、`sfZonalUpgradeMode` を `Parallel` に設定します。 これにより、UD ウォークがすべての可用性ゾーンで並列に実行されます。

### <a name="migrate-to-the-node-type-with-multiple-availability-zones"></a>複数の可用性ゾーンを持つノード タイプに移行する

すべての移行シナリオで、複数の可用性ゾーンをサポートする新しいノード タイプを追加する必要があります。 既存のノード タイプを移行して複数のゾーンをサポートすることはできません。
「[Service Fabric クラスターのプライマリ ノード タイプをスケールアップする](./service-fabric-scale-up-primary-node-type.md)」の記事には、新しいノード タイプと、新しいノード タイプに必要なその他のリソース (IP やロード バランサーのリソースなど) を追加する詳細な手順が記載されています。 その記事には、複数の可用性ゾーンを持つ新しいノード タイプがクラスターに追加された後、既存のノード タイプをインベントリから削除する方法も記載されています。

* 基本的なロード バランサーと IP リソースを使用するノード タイプからの移行: このプロセスは、可用性ゾーンごとに 1 つのノード タイプを使用するソリューションに関する[以下のサブセクション](#migrate-to-availability-zones-from-a-cluster-by-using-a-basic-sku-load-balancer-and-a-basic-sku-ip)で説明しています。

  新しいノード タイプの場合、唯一の違いは、可用性ゾーンごとに 1 つではなく、すべての可用性ゾーンに対して仮想マシン スケール セットが 1 つ、ノード タイプが 1 つだけであるということです。
* NSG とともに Standard SKU ロード バランサーと IP リソースを使用するノード タイプからの移行: 前述のものと同じ手順に従います。 ただし、新しいロード バランサー、IP、NSG のリソースを追加する必要はありません。 同じリソースを新しいノード タイプで再利用できます。


## <a name="2-deploy-zones-by-pinning-one-virtual-machine-scale-set-to-each-zone"></a>2. 1 つの仮想マシン スケール セットを各ゾーンに固定することでゾーンをデプロイする

これは、現在一般提供されている構成です。
Service Fabric クラスターを可用性ゾーン間にまたがらせるには、リージョンでサポートされている各可用性ゾーンにプライマリ ノード タイプを作成する必要があります。 これにより、各プライマリ ノード タイプ間にシード ノードが均等に分散されます。

プライマリ ノード タイプの推奨されるトポロジでは、以下が必要です。
* プライマリとしてマークされた 3 つのノード タイプ
  * 各ノード タイプは異なるゾーンに配置されているその独自の仮想マシン スケール セットにマップされている必要があります。
  * 各仮想マシン スケール セットには少なくとも 5 つのノード (Silver 耐久性) が必要です。

次の図には、Azure Service Fabric 可用性ゾーンのアーキテクチャが示されています。

![Azure Service Fabric 可用性ゾーンのアーキテクチャを示す図。][sf-architecture]

### <a name="enable-zones-on-a-virtual-machine-scale-set"></a>仮想マシン スケール セットでゾーンを有効にする

ゾーンを有効にするには、仮想マシン スケール セットで、仮想マシン スケール セット リソースに次の 3 つの値を含めます。

* 最初の値は、`zones` プロパティであり、仮想マシン スケール セットをデプロイする可用性ゾーンを指定します。
* 2 つ目の値は、`singlePlacementGroup` プロパティであり、`true` に設定する必要があります。
* 3 つ目の値は、Service Fabric 仮想マシン スケール セット拡張機能の `faultDomainOverride` プロパティです。 このプロパティには、この仮想マシン スケール セットを配置するゾーンのみを含める必要があります。 例: `"faultDomainOverride": "az1"`. Azure Service Fabric クラスターにはクロス リージョンのサポートがないため、すべての仮想マシン スケール セット リソースを同じリージョンに配置する必要があります。

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[parameters('vmNodeType1Name')]",
  "location": "[parameters('computeLocation')]",
  "zones": [
    "1"
  ],
  "properties": {
    "singlePlacementGroup": true
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
      ]
    }
  }
}
```

### <a name="enable-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Service Fabric クラスター リソースで複数のプライマリ ノード タイプを有効にする

クラスター リソースで 1 つまたは複数のノード タイプをプライマリとして設定するには、`isPrimary` プロパティを `true` に設定します。 可用性ゾーンにまたがって Service Fabric クラスターをデプロイする場合、個別のゾーンに 3 つのノード タイプが必要です。

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
  ]
}
```

## <a name="migrate-to-availability-zones-from-a-cluster-by-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Basic SKU ロード バランサーと Basic SKU IP を使用してクラスターから Availability Zones の使用に移行する

Basic SKU でロード バランサーと IP を使用しているクラスターを移行するには、まず Standard SKU を使用したまったく新しいロード バランサーと IP リソースを作成する必要があります。 これらのリソースを更新することはできません。

使用する新しいクロス可用性ゾーン ノード タイプで新しいロード バランサーと IP を参照します。 前述の例では、3 つの新しい仮想マシン スケール セットのリソースがゾーン 1、2、3 に追加されています。 これらの仮想マシン スケール セットは、新しく作成されたロード バランサーと IP を参照し、Service Fabric クラスター リソース内でプライマリ ノード タイプとしてマークされます。

1. まず、既存の Azure Resource Manager テンプレートに新しいリソースを追加します。 これらのリソースには次が含まれます。

   * Standard SKU を使用したパブリック IP リソース
   * Standard SKU を使用したロード バランサー リソース
   * 仮想マシン スケール セットをデプロイするサブネットによって参照されている NSG
   * プライマリとしてマークされた 3 つのノード タイプ
     * 各ノード タイプは異なるゾーンに配置されているその独自の仮想マシン スケール セットにマップされている必要があります。
     * 各仮想マシン スケール セットには少なくとも 5 つのノード (Silver 耐久性) が必要です。

   これらのリソースの例については、[サンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)を参照してください。

   ```powershell
   New-AzureRmResourceGroupDeployment `
       -ResourceGroupName $ResourceGroupName `
       -TemplateFile $Template `
       -TemplateParameterFile $Parameters
   ```

1. リソースのデプロイが完了したら、元のクラスターから、プライマリ ノード タイプのノードを無効にできます。 ノードを無効にすると、システム サービスが、前にデプロイした新しいプライマリ ノード タイプに移行されます。

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

1. ノードをすべて無効にすると、ゾーンに分散されたプライマリ ノード タイプで、システム サービスが実行されます。 その後、無効にしたノードをクラスターから削除できます。 ノードを削除したら、元の IP、ロード バランサー、仮想マシン スケール セット リソースを削除できます。

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

1. 次に、デプロイした Resource Manager テンプレートから、これらのリソースへの参照を削除します。

1. 最後に、DNS 名とパブリック IP を更新します。

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

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png
