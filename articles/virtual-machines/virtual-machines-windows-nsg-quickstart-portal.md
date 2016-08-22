<properties
   pageTitle="ポータルを使用して VM への外部アクセスを許可する |Microsoft Azure"
   description="Azure ポータルで Resource Manager デプロイメント モデルを使用して、VM に対するアクセスを許可するためにポートを開き、エンドポイントを作成する方法について説明します。"
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

# Azure ポータルを使用して VM への外部アクセスを許可する
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## クイック コマンド
[これらの手順は、Azure PowerShell を使用して実行](virtual-machines-windows-nsg-quickstart-powershell.md)することもできます。

最初に、ネットワーク セキュリティ グループを作成します。ポータルでリソース グループを選択し、[追加] をクリックして、[ネットワーク セキュリティ グループ] を探して選択します。

![ネットワーク セキュリティ グループの追加](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

ネットワーク セキュリティ グループの名前を入力し、場所を選択します。

![ネットワーク セキュリティ グループの作成](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

新しいネットワーク セキュリティ グループを選択します。ここで、受信規則を作成します。

![受信規則の追加](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

新しい規則の名前を指定します。ポート 80 が既定で入力されていることに注意してください。他の規則をネットワーク セキュリティ グループに追加する場合は、ここでソース、プロトコル、および宛先を変更します。

![受信規則の作成](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

最後に、ネットワーク セキュリティ グループを、サブネットまたは特定のネットワーク インターフェイスに関連付けます。ネットワーク セキュリティ グループをサブネットに関連付けてみましょう。

![ネットワーク セキュリティ グループとサブネットの関連付け](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

仮想ネットワークを選択し、適切なサブネットを選択します。

![ネットワーク セキュリティ グループと仮想ネットワークの関連付け](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

これで、ネットワーク セキュリティ グループの作成、ポート 80 のトラフィックを許可する受信規則の作成、ネットワーク セキュリティ グループとサブネットの関連付けが完了しました。そのサブネットに接続するすべての VM に、ポート 80 でアクセスできます。


## ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。詳細については、[ネットワーク セキュリティ グループと ACL 規則の作成](../virtual-network/virtual-networks-create-nsg-arm-ps.md)に関するページをご覧ください。

ネットワーク セキュリティ グループと ACL 規則は、Azure Resource Manager のテンプレートの一部として定義できます。詳細については、[テンプレートを使用したネットワーク セキュリティ グループの作成](../virtual-network/virtual-networks-create-nsg-arm-template.md)に関するページをご覧ください。

ポート転送を使用して一意の外部ポートを VM 上の内部ポートにマップする必要がある場合は、ロード バランサーとネットワーク アドレス変換 (NAT) 規則を使用する必要があります。たとえば、TCP ポート 8080 を外部に公開し、VM 上の TCP ポート 80 にトラフィックを送ることができます。詳細については、[インターネットに接続するロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-arm-ps.md)に関するページをご覧ください。

## 次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。より精密な環境の作成については、次の記事で確認できます。

- [Azure リソース マネージャーの概要](../resource-group-overview.md)
- [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)
- [ロード バランサー用の Azure Resource Manager の概要](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0810_2016-->