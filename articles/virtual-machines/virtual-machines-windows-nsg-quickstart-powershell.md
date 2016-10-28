<properties
   pageTitle="PowerShell を使用した VM へのポートの開放 | Microsoft Azure"
   description="Azure Resource Manager デプロイメント モデルと Azure PowerShell を使用して、Windows VM へのポートを開き、エンドポイントを作成する方法について説明します。"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="iainfou"/>

# PowerShell を使用した Azure の VM へのポートの開放
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## クイック コマンド
ネットワーク セキュリティ グループと ACL 規則を作成するには、[最新バージョンの Azure PowerShell がインストールされている](../powershell-install-configure.md)必要があります。[これらの手順は、Azure Portal を使用して実行](virtual-machines-windows-nsg-quickstart-portal.md)することもできます。

まず、TCP ポート 80 で HTTP トラフィックを許可する規則を作成し、独自の名前と説明を入力します。

```
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name http-rule -Description "Allow HTTP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

そして、次のように、ネットワーク セキュリティ グループを作成し、先ほど作成した HTTP 規則を割り当てて、独自のリソース グループ名と場所を入力します。

```
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `
    -Name "TestNSG" -SecurityRules $httprule
```

ここで、ネットワーク セキュリティ グループをサブネットに割り当ててみましょう。まず、仮想ネットワークを選択します。

```
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
```

ネットワーク セキュリティ グループとサブネットを関連付けます。

```
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet `
    -NetworkSecurityGroup $nsg
```

最後に、仮想ネットワークを更新して、変更を有効にします。

```
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。詳細については、[ネットワーク セキュリティ グループと ACL 規則の作成](../virtual-network/virtual-networks-create-nsg-arm-ps.md)に関するページをご覧ください。

ネットワーク セキュリティ グループと ACL 規則は、Azure Resource Manager のテンプレートの一部として定義できます。詳細については、[テンプレートを使用したネットワーク セキュリティ グループの作成](../virtual-network/virtual-networks-create-nsg-arm-template.md)に関するページをご覧ください。

ポート フォワーディングを使用して、一意の外部ポートを VM 上の内部ポートにマップする必要がある場合は、ロード バランサーとネットワーク アドレス変換 (NAT) 規則を使用します。たとえば、TCP ポート 8080 を外部に公開し、VM 上の TCP ポート 80 にトラフィックを送ることができます。詳細については、[インターネットに接続するロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-arm-ps.md)に関するページをご覧ください。

## 次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。より精密な環境の作成については、次の記事で確認できます。

- [Azure リソース マネージャーの概要](../resource-group-overview.md)
- [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)
- [ロード バランサー用の Azure Resource Manager の概要](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0907_2016-->