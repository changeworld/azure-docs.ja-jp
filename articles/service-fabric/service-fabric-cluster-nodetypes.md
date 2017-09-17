---
title: "Service Fabric ノードの種類と VM Scale Sets | Microsoft Docs"
description: "Service Fabric のノードの種類を VM スケール セットに関連付ける方法と、VM スケール セットのインスタンスまたはクラスター ノードにリモート接続する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 8c9e91d122591a19d34d944e2d9aaeb327cdafe4
ms.contentlocale: ja-jp
ms.lasthandoff: 09/05/2017

---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Service Fabric ノードの種類と仮想マシン スケール セットの関係
仮想マシン スケール セットは Azure コンピューティング リソースです。 仮想マシンのコレクションをセットとしてデプロイおよび管理する場合に使用できます。 Service Fabric クラスターで定義されているすべてのノード タイプは、個別の仮想マシン スケール セットとしてセットアップされます。 各ノードの種類は、個別にスケール アップまたはスケール ダウンすることができ、さまざまなセットのポートを開き、異なる容量のメトリックスを持つことができます。

次のスクリーン ショットでは、ノードが 2 種類あるクラスターを示します (FrontEnd と BackEnd)。  各ノードの種類に、5 つのノードがあります。

![ノードが 2 種類あるクラスターのスクリーン ショット][NodeTypes]

## <a name="mapping-virtual-machine-scale-set-instances-to-nodes"></a>仮想マシン スケール セットのインスタンスをノードにマップする
上述のように、仮想マシン スケール セットのインスタンスは、インスタンス 0 から始まり増えていきます。 番号設定は名前に反映されます。 たとえば、BackEnd_0 は、BackEnd 仮想マシン スケール セットのインスタンス 0 です。 この特定の仮想マシン スケール セットには、BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4 という名前の 5 つのインスタンスがあります。

仮想マシン スケール セットをスケール アップすると、新しいインスタンスが作成されます。 通常、新しい仮想マシン スケール セットのインスタンス名は、仮想マシン スケール セットの名前 + 次のインスタンス番号になります。 この例では、BackEnd_5 です。

## <a name="mapping-virtual-machine-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>仮想マシン スケール セットのロード バランサーを各ノードの種類/VM スケール セットにマッピングする
ポータルからクラスターをデプロイした場合、またはサンプル Resource Manager テンプレートを使用した場合、リソース グループ以下にすべてのリソース一覧が表示されます。 また、各仮想マシン スケール セットまたはノードの種類のロード バランサーが表示されます。

名前は次のようになります: **LB-&lt;NodeType name&gt;**。 たとえば、次のスクリーンショットで示すように、LB-sfcluster4doc-0 などです。

![リソース][Resources]

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>仮想マシン スケール セット インスタンスまたはクラスター ノードにリモート接続する
クラスターで定義されているすべてのノード タイプは、個別の仮想マシン スケール セットとして設定されます。  つまり、ノードの種類は個別にスケール アップまたはスケール ダウンすることができます。 また、異なる VM SKU から作成することができます。 単一のインスタンス VM とは異なり、仮想マシン スケール セットのインスタンスは、独自の仮想 IP アドレスを取得しません。 そのため、特定のインスタンスにリモート接続するために使用できる IP アドレスとポートを検索するときに、少し難しい場合があります。

IP アドレスとポートを検出する手順を次に示します。

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>手順 1: ノードの種類に仮想 IP アドレスを検索し、RDP に受信 NAT 規則を検索する
これを取得するには、 **Microsoft.Network/loadBalancers**のリソース定義の一部として定義された受信 NAT 規則の値を取得する必要があります。

ポータルで、[ロード バランサー] ブレード、 **[設定]**の順に移動します。

![LBBlade][LBBlade]

**[設定]** で、**[受信 NAT 規則]** をクリックします。 ここでは、最初の仮想マシン スケール セットのインスタンスにリモート接続するために使用できる IP アドレスとポートを提供します。 次のスクリーンショットでは、**104.42.106.156** と **3389** です。

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-virtual-machine-scale-set-instancenode"></a>手順 2: 特定の仮想マシン スケール セットのインスタンス/ノードにリモート接続するために使用できるポートを検索する
このドキュメントの前半では、仮想マシン スケール セットのインスタンスをノードにマップする方法について説明しました。 ここでは、正確なポートを特定するために使用します。

ポートは、仮想マシン スケール セットのインスタンスの昇順で割り当てられます。 そのため、FrontEnd のノードの種類の例では、5 つのインスタンスの各ポートは次のようになります。 ここでは、仮想マシン スケール セットのインスタンスと同じマッピングを行う必要があります。

| **仮想マシン スケール セットのインスタンス** | **ポート** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-virtual-machine-scale-set-instance"></a>手順 3: 特定の仮想マシン スケール セットのインスタンスにリモート接続する
次のスクリーンショットでは、リモート デスクトップ接続を使用して、FrontEnd_1 に接続します。

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>RDP ポート範囲の値を変更する方法
### <a name="before-cluster-deployment"></a>クラスター デプロイの前
Resource Manager テンプレートを使用してクラスターを設定している場合、**inboundNatPools** の範囲を指定できます。

**Microsoft.Network/loadBalancers**のリソース定義に移動します。 その下に、 **inboundNatPools**の説明があります。  *frontendPortRangeStart* と *frontendPortRangeEnd* の値を置き換えます。

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>クラスター デプロイの後
クラスター デプロイの後は少し複雑で、VM がリサイクルされる可能性があります。 Azure PowerShell を使用して、新しい値を設定します。 Azure PowerShell 1.0 以降がコンピューターにインストールされていることを確認します。 Azure PowerShell 1.0 以降をまだインストールしていない場合は、[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)に関するページの手順に従うことを強くお勧めします。

Azure アカウントにサインインします。 この PowerShell コマンドが何らかの理由で失敗する場合、Azure PowerShell が正しくインストールされているかどうかを確認することをお勧めします。

```
Login-AzureRmAccount
```

次を実行して、ロード バランサーに関する詳細を取得すると、 **inboundNatPools**の説明の値が表示されます。

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

*frontendPortRangeEnd* と *frontendPortRangeStart* を必要な値に設定します。

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```

## <a name="how-to-change-the-rdp-username--password-for-nodes"></a>ノードの RDP ユーザー名とパスワードを変更する方法

次の手順は、指定されたノードの種類の全ノードについてパスワードを変更する方法を説明しています。 これらの変更は、仮想マシン スケール セットの現在のノードと今後のノードすべてに適用されます。

### <a name="step-1-open-powershell-with-elevated-privileges-administrator-mode"></a>手順 1: 管理者特権 (管理者モード) で PowerShell を開きます。 
### <a name="step-2-run-the-following-commands-to-log-in-and-select-your-subscription-for-the-session-change-the-subscriptionid-parameter-to-your-subscription-id"></a>手順 2: 次のコマンドを実行してログインし、セッションのサブスクリプションを選択します。 `SUBSCRIPTIONID` パラメーターを自分のサブスクリプション ID に変更します。 

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
```

### <a name="step-3-run-the-following-script-with-the-appropriate-nodetypename-resourcegroup-username-and-password-values-the-username-and-password-values-will-be-the-new-credentials-that-should-be-used-in-future-rdp-sessions"></a>手順 3: 適切な `NODETYPENAME`、`RESOURCEGROUP`、`USERNAME`、`PASSWORD` の値を使用して、次のスクリプトを実行します。 `USERNAME` 値と `PASSWORD` 値は、今後の RDP セッションで使用される新しい資格情報になります。 

```powershell
$nodeTypeName = 'NODETYPENAME'
$resourceGroup = 'RESOURCEGROUP'
$publicConfig = @{'UserName' = 'USERNAME'}
$privateConfig = @{'Password' = 'PASSWORD'}
$extName = 'VMAccessAgent'
$publisher = 'Microsoft.Compute'
$node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
$node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
```

## <a name="next-steps"></a>次のステップ
* ["任意の場所にデプロイ" 機能の概要と Azure で管理されるクラスターとの比較](service-fabric-deploy-anywhere.md)
* [クラスターのセキュリティ](service-fabric-cluster-security.md)
* [ Service Fabric SDK と概要](service-fabric-get-started.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

