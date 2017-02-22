---
title: "仮想マシンの複数の IP アドレス - Azure CLI | Microsoft Docs"
description: "Azure CLI を使用して Resource Manager で仮想マシンに複数の IP アドレスを割り当てる方法について説明します。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 482e0d8084d84f9a3170180c2e5414ca77364da8
ms.openlocfilehash: 6fb458d47173b4922f085e8b1e6339cabefc7da6


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Azure CLI を使用して仮想マシンに複数の IP アドレスを割り当てる

> [!div class="op_single_selector"]
> * [ポータル](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)

Azure 仮想マシン (VM) には、1 つ以上のネットワーク インターフェイス (NIC) を接続できます。 NIC には、1 つ以上の静的または動的パブリックおよびプライベート IP アドレスを割り当てることができます。 VM に複数の IP アドレスを割り当てると、次のことが可能になります。

* 異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1つのサーバーでホストする。
* ファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。
* 複数の NIC いずれかの複数のプライベート IP アドレスいずれかを Azure Load Balancer のバックエンド プールに追加する。 以前は、プライマリ NIC のプライマリ IP アドレスのみをバックエンド プールに追加できました。 複数の IP 構成の負荷分散方法の詳細については、[複数の IP 構成の負荷分散](../load-balancer/load-balancer-multiple-ip.md)に関する記事を参照してください。

VM に接続された各 NIC には、1 つ以上の IP 構成が関連付けられています。 各構成には、1 つの静的または動的プライベート IP アドレスが割り当てられています。 また、1 つのパブリック IP アドレス リソースが関連付けられている場合もあります。 パブリック IP アドレス リソースには、動的または静的 IP アドレスが割り当てられています。 Azure 内の IP アドレスの詳細については、「 [Azure 内の IP アドレス](virtual-network-ip-addresses-overview-arm.md) 」の記事をご覧ください。

この記事では、PowerShell を使用して、Azure Resource Manager デプロイ モデルで作成された VM に複数の IP アドレスを割り当てる方法について説明します。 クラシック デプロイ モデルで作成されたリソースには、複数の IP アドレスを割り当てることはできません。 Azure のデプロイ モデルの詳細については、[デプロイ モデルの概要](../resource-manager-deployment-model.md)に関する記事をご覧ください。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>シナリオ
1 つの NIC が接続された VM を作成し、仮想ネットワークに接続します。 VM には、3 つの "*プライベート*" IP アドレスと 2 つの "*パブリック*" IP アドレスが必要です。 各 IP アドレスを次の IP 構成に割り当てます。

* **IPConfig-1:** "*動的*" プライベート IP アドレス (既定) と "*静的*" パブリック IP アドレスを割り当てます。
* **IPConfig-2:** "*静的*" プライベート IP アドレスと "*静的*" パブリック IP アドレスを割り当てます。
* **IPConfig-3:** "*動的*" プライベート IP アドレスを割り当てます。パブリック IP アドレスは割り当てません。
  
    ![複数の IP アドレス](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

NIC の作成時に IP 構成を NIC に関連付け、VM の作成時に NIC を VM に接続します。 シナリオで使用する IP アドレスの種類は、わかりやすく説明するためのものです。 必要な IP アドレスの種類と割り当ての種類を使用できます。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>複数の IP アドレスを持つ VM を作成する

以下の手順は、シナリオの説明に従って複数の IP アドレスを持つ VM を作成する方法を示しています。 変数名と IP アドレスの種類は、実際の実装に合わせて変更してください。

1. [Azure CLI のインストールと構成](../xplat-cli-install.md)に関する記事の手順に従って、Azure CLI をインストールして構成し、Azure アカウントにログインします。

2. サブスクリプション ID と使用目的を[複数の IP 係](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)まで電子メールで連絡して、プレビューに登録します。 次の場合、残りの手順は実行しないでください。
    - プレビューへの登録が受諾されたことを通知する電子メールを受け取っていない場合
    - 受け取った電子メールの指示に従っていない場合
3. [リソース グループを作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations)し、次に[仮想ネットワークとサブネット](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet)を作成します。 この記事で説明するシナリオに従うために、``` --address-prefixes ``` フィールドと ```--address-prefix``` フィールドを次のように変更します。

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >上記の参照先の記事では、リソースを作成する場所として西ヨーロッパを使用していますが、この記事では米国中西部を使用します。 場所は適宜変更してください。

4. VM の[ストレージ アカウントを作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account)します。

5. NIC と NIC に割り当てる IP 構成を作成します。 構成は、必要に応じて追加、削除、または変更できます。 このシナリオで説明する構成は次のとおりです。

    **IPConfig 1**

    次のコマンドを入力して、以下を作成します。

    - 静的パブリック IP アドレスが割り当てられたパブリック IP アドレス リソース
    - パブリック IP アドレス リソースと動的プライベート IP アドレスが割り当てられた IP 構成

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     次のコマンドを入力して、新しいパブリック IP アドレス リソース、および静的パブリック IP アドレスと静的プライベート IP アドレスが割り当てられた新しい IP 構成を作成します。
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    次のコマンドを入力して、動的プライベート IP アドレスが割り当てられ、パブリック IP アドレスは割り当てられていない IP 構成を作成します。

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >この記事では、すべての IP 構成を 1 つの NIC に割り当てていますが、複数の IP 構成を VM の任意の NIC に割り当てることもできます。 複数の NIC が接続された VM の作成方法については、複数の NIC が接続された VM の作成に関する記事をご覧ください。

6. [Linux VM の作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms)手順に従って、Linux VM を作成します。 ```  --availset-name myAvailabilitySet \ ``` プロパティはこのシナリオでは不要であるため、必ず削除してください。 実際のシナリオに基づいて適切な場所を使用します。 

    >[!WARNING] 
    > 選択した場所で該当の VM サイズがサポートされていない場合、VM の作成に関する記事の手順 6 は失敗します。 たとえば、米国中西部の VM の一覧を取得するには、`azure vm sizes --location westcentralus` コマンドを実行します。この場所の名前は、実際のシナリオに基づいて変更できます。

    たとえば、VM サイズを Standard DS2 v2 に変更するには、手順 6 の ``` azure vm create ``` コマンドに ```  --vm-size Standard_DS3_v2``` プロパティを追加します。

7. 次のコマンドを入力して、NIC および関連付けられた IP 構成を表示します。

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```
8. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載されたご使用のオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>VM に IP アドレスを追加する

プライベートおよびパブリック IP アドレスを既存の NIC に追加するには、次の手順を実行します。 各例はこの記事で説明する[シナリオ](#Scenario)に基づいています。

1. Azure CLI を開き、1 つの CLI セッションでこのセクションの残りの手順を実行します。 Azure CLI のインストールと構成をまだ行っていない場合は、[Azure CLI のインストールと構成](../xplat-cli-install.md)に関する記事の手順を完了し、Azure アカウントにログインします。

2. サブスクリプション ID と使用目的を[複数の IP 係](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)まで電子メールで連絡して、プレビューに登録します。 次の場合、残りの手順は実行しないでください。
    - プレビューへの登録が受諾されたことを通知する電子メールを受け取っていない場合
    - 受け取った電子メールの指示に従っていない場合

3. 要件に基づいて、以下のいずれかのセクションの手順を実行します。

    **プライベート IP アドレスを追加する**
    
    NIC にプライベート IP アドレスを追加するには、次のコマンドを使用して IP 構成を作成する必要があります。  動的プライベート IP アドレスを追加する場合は、コマンドを入力する前に、```-PrivateIpAddress 10.0.0.7``` を削除します。 静的 IP アドレスを指定するときは、サブネットの未使用のアドレスを指定する必要があります。

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    一意の構成名とプライベート IP アドレス (静的 IP アドレスを使用する構成の場合) を使用して、必要な数だけ構成を作成します。

    **パブリック IP アドレスを追加する**
    
    パブリック IP アドレスを追加するには、新しい IP 構成または既存の IP 構成にパブリック IP アドレスを関連付けます。 必要に応じて、以下のいずれかのセクションの手順を実行します。

    > [!NOTE]
    > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。
    >

    **リソースを新しい IP 構成に関連付ける**
    
    すべての IP 構成にプライベート IP アドレスが必要であるため、パブリック IP アドレスを新しい IP 構成に追加するときは、必ずプライベート IP アドレスも追加する必要があります。 既存のパブリック IP アドレス リソースを追加することも、新しいリソースを作成することもできます。 新しいパブリック IP アドレス リソースを作成するには、次のコマンドを入力します。
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

    動的プライベート IP アドレスと、関連付けられた *myPublicIP3* パブリック IP アドレス リソースで新しい IP 構成を作成するには、次のコマンドを入力します。

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **リソースを既存の IP 構成に関連付ける**
    パブリック IP アドレス リソースは、このリソースがまだ関連付けられていない IP 構成にのみ関連付けることができます。 IP 構成にパブリック IP アドレスが関連付けられているかどうかを確認するには、次のコマンドを入力します。

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    返された出力で次のような行を探します。
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    *IpConfig-3* の **Public IP** 列が空白であるため、現在、この構成にはパブリック IP アドレス リソースは関連付けられていません。 IpConfig-3 に既存のパブリック IP アドレス リソースを追加することも、次のコマンドを入力して新しいリソースを作成することもできます。

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    パブリック IP アドレス リソースを、*IPConfig-3* という名前の既存の IP 構成に関連付けるには、次のコマンドを入力します。
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```

7. 次のコマンドを入力して、NIC に割り当てられたプライベート IP アドレスとパブリック IP アドレス リソースを表示します。

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    次のような出力が表示されます。 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」の手順に従って、NIC に追加したプライベート IP アドレスを、VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]


<!--HONumber=Dec16_HO1-->


