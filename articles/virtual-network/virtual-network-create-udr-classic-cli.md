<properties 
   pageTitle="クラシック デプロイメント モデルで Azure CLI を使用してルーティングを制御し、仮想アプライアンスを使用する | Microsoft Azure"
   description="クラシック デプロイメント モデルで Azure CLI を使用して VNet でのルーティングを制御する方法を説明する"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

#Azure CLI を使用してルーティングを制御し仮想アプライアンス (クラシック) を使用する

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、クラシック デプロイメント モデルについて説明します。[リソース マネージャーのデプロイメント モデルでルーティングを制御し、仮想アプライアンスを使用する](virtual-network-create-udr-arm-cli.md)こともできます。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下の Azure CLI のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。このドキュメントに示されているようにコマンドを実行するのであれば、「[Azure CLI を使用して VNet (クラシック) を作成する](virtual-networks-create-vnet-classic-cli.md)」に示されている環境を構築します。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## フロントエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、フロントエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. **`azure config mode`** を実行してクラシック モードに切り替えます。

		azure config mode asm

	出力:

		info:    New mode is asm

3. **`azure network route-table create`** コマンドを実行して、フロントエンドのサブネットのルート テーブルを作成します。

		azure network route-table create -n UDR-FrontEnd -l uswest

	出力:

		info:    Executing command network route-table create
		info:    Creating route table "UDR-FrontEnd"
		info:    Getting route table "UDR-FrontEnd"
		data:    Name                            : UDR-FrontEnd
		data:    Location                        : West US
		info:    network route-table create command OK

	パラメーター: - **-l (または --location)**.NSG が作成される Azure リージョンです。ここでは、*westus* です。- -**n (または --name)**。新しい NSG の名前です。ここでは、*NSG-FrontEnd* です。

4. バックエンドのサブネット (192.168.2.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、**`azure network route-table route set`** コマンドを実行して、上記で作成済みのルート テーブル内にルートを作成します。

		azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

	出力:

		info:    Executing command network route-table route set
		info:    Getting route table "UDR-FrontEnd"
		info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    network route-table route set command OK

	パラメーター:- **-r (または --route-table-name)**。ルートが追加されるルート テーブルの名前です。ここでは、*UDR FrontEnd* です。- **-a (または --address-prefix)**。パケットの送信先であるサブネットのアドレス プレフィックスです。ここでは、*192.168.2.0/24* です。- **-t (または --next-hop-type)**。トラフィックの送信先となるオブジェクトの種類です。指定できる値は *VirtualAppliance*、*VirtualNetworkGateway*、*VNETLocal*、*Internet*、または *None* です。- **-p (または --next-hop-ip-address**)。次のホップ先の IP アドレスです。ここでは、*192.168.0.4* です。

5. **`azure network vnet subnet route-table add`** コマンドを実行して、上記で作成したルート テーブルを **FrontEnd** サブネットに関連付けます。

		azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

	出力:

		info:    Executing command network vnet subnet route-table add
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		data:    Route table name                : UDR-FrontEnd
		data:      Location                      : West US
		data:      Routes:
		info:    network vnet subnet route-table add command OK	

	パラメーター: - **-t (または --vnet-name)**。サブネットが置かれている VNet の名前です。ここでは、*TestVNet* です。- **-n (または --subnet-name)** です。ルート テーブルが追加されるサブネットの名前です。ここでは、*FrontEnd* です。
 
## バックエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、バックエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

3. バックエンドのサブネットのルート テーブルを作成するには、**`azure network route-table create`** コマンドを実行します。

		azure network route-table create -n UDR-BackEnd -l uswest

4. フロントエンドのサブネット (192.168.1.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、**`azure network route-table route set`** コマンドを実行して、上記で作成済みのルート テーブル内にルートを作成します。

		azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. **`azure network vnet subnet route-table add`** コマンドを実行して、上記で作成したルート テーブルを **BackEnd** サブネットに関連付けます。

		azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

<!---HONumber=AcomDC_1217_2015-->