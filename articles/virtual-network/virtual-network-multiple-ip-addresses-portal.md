---
title: "仮想マシンの複数の IP アドレス - Portal | Microsoft Docs"
description: "Azure Portal を使用して、仮想マシンに複数の IP アドレスを割り当てる方法 | Resource Manager"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 7ce83952f0f16e01a4837ce2155571d223d7b551
ms.openlocfilehash: b1a2549e0f04dbc00a47a57ecc888ca36339c646


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Azure Portal を使用して仮想マシンに複数の IP アドレスを割り当てる

> [!div class="op_single_selector"]
> * [ポータル](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)
>

Azure 仮想マシン (VM) には、1 つ以上のネットワーク インターフェイス (NIC) を接続できます。 NIC には、1 つ以上の静的または動的パブリックおよびプライベート IP アドレスを割り当てることができます。 VM に複数の IP アドレスを割り当てると、次のことが可能になります。

* 異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1つのサーバーでホストする。
* ファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。
* 複数の NIC いずれかの複数のプライベート IP アドレスいずれかを Azure Load Balancer のバックエンド プールに追加する。 以前は、プライマリ NIC のプライマリ IP アドレスのみをバックエンド プールに追加できました。 複数の IP 構成の負荷分散方法の詳細については、[複数の IP 構成の負荷分散](../load-balancer/load-balancer-multiple-ip.md)に関する記事を参照してください。

VM に接続された各 NIC には、1 つ以上の IP 構成が関連付けられています。 各構成には、1 つの静的または動的プライベート IP アドレスが割り当てられています。 また、1 つのパブリック IP アドレス リソースが関連付けられている場合もあります。 パブリック IP アドレス リソースには、動的または静的 IP アドレスが割り当てられています。 Azure における IP アドレスの詳細については、「[Azure 内の IP アドレス](virtual-network-ip-addresses-overview-arm.md)」を参照してください。

この記事では、Azure Portal を使用して、Azure Resource Manager デプロイ モデルで作成された VM に複数の IP アドレスを割り当てる方法について説明します。 クラシック デプロイ モデルで作成されたリソースには、複数の IP アドレスを割り当てることはできません。 Azure のデプロイ モデルの詳細については、[デプロイ モデルの概要](../resource-manager-deployment-model.md)に関する記事をご覧ください。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>シナリオ
1 つの NIC が接続された VM を作成し、仮想ネットワークに接続します。 VM には、3 つの "*プライベート*" IP アドレスと 2 つの "*パブリック*" IP アドレスが必要です。 各 IP アドレスを次の IP 構成に割り当てます。

* **IPConfig-1:** "*動的*" プライベート IP アドレス (既定) と "*静的*" パブリック IP アドレスを割り当てます。
* **IPConfig-2:** "*静的*" プライベート IP アドレスと "*静的*" パブリック IP アドレスを割り当てます。
* **IPConfig-3:** "*動的*" プライベート IP アドレスを割り当てます。パブリック IP アドレスは割り当てません。
  
    ![複数の IP アドレス](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

NIC の作成時に IP 構成を NIC に関連付け、VM の作成時に NIC を VM に接続します。 シナリオで使用する IP アドレスの種類は、わかりやすく説明するためのものです。 必要な IP アドレスの種類と割り当ての種類を使用できます。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>複数の IP アドレスを持つ VM を作成する

複数の IP アドレスを持つ VM を作成する場合は、PowerShell または Azure CLI を使用して VM を作成する必要があります。 この記事の上部にある PowerShell または CLI オプションをクリックすると、その方法が表示されます。 「[Windows VM の作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md)」または「[Linux VM の作成](../virtual-machines/virtual-machines-linux-quick-create-portal.md)」に関する記事の手順に従ってポータルを使用し、1 つの静的プライベート IP アドレスと (必要に応じて) 1 つのパブリック IP アドレスを持つ VM を作成できます。 VM を作成したら、この記事の「[VM に IP アドレスを追加する](#add)」セクションの手順に従ってポータルを使用することにより、IP アドレス タイプを変更して、別の IP アドレスを追加できます。

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>VM に IP アドレスを追加する

プライベート IP アドレスとパブリック IP アドレスを NIC に追加するには、次の手順を実行します。 次のセクションの例では、この記事の[シナリオ](#Scenario)で説明している 3 つの IP 構成を使用した VM を既に所有していることを前提としていますが、必須ではありません。

> [!NOTE]
> この記事では、すべての IP 構成を 1 つの NIC に割り当てていますが、複数の IP 構成を VM の任意の NIC に割り当てることもできます。 複数の NIC を持つ VM の作成方法については、[複数の NIC を持つ VM の作成](virtual-network-deploy-multinic-arm-ps.md)に関する記事を参照してください。

### <a name="a-namecoreaddacore-steps"></a><a name="coreadd"></a>主な手順

1. サブスクリプション ID と使用目的を[複数の IP 係](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)まで電子メールで連絡して、プレビューに登録します。 次の場合、残りの手順は実行しないでください。
    - プレビューへの登録が受諾されたことを通知する電子メールを受け取っていない場合
    - 受け取った電子メールの指示に従っていない場合
2. 必要に応じて、https://portal.azure.com で Azure Portal を開き、サインインします。
3. そのポータルで、**[その他のサービス]** をクリックし、フィルター ボックスに「*virtual machines*」と入力して、**[Virtual Machines]** をクリックします。
4. **[Virtual Machines]** ブレードで、IP アドレスを追加する VM をクリックします。 表示された仮想マシン ブレードで **[ネットワーク インターフェイス]** をクリックして、IP アドレスを追加するネットワーク インターフェイスを選択します。 次の図に示す例では、「*myVM*」という名前の VM の「*myNIC*」という名前の NIC を選択しています。

    ![ネットワーク インターフェイス](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

5. 選択した NIC について表示されるブレードで、次の図に示すように、**[IP 構成]** をクリックします。

    ![IP 構成](./media/virtual-network-multiple-ip-addresses-portal/figure2.png)

追加する IP アドレスのタイプに基づいて、以下のセクションのいずれかの手順を実行します。

### <a name="add-a-private-ip-address"></a>**プライベート IP アドレスを追加する**

新しいプライベート IP アドレスを追加するには、次の手順を完了します。

1. この記事の「[主な手順](#coreadd)」で述べた手順を完了します。
2. **[追加]**をクリックします。 次の図に示すように、表示された **[IP 構成の追加]** ブレードで、"*静的*"プライベート IP アドレスとして「*10.0.0.7*」を持つ「*IPConfig-4*」という名前の IP 構成を作成し、**[OK]** をクリックします。

    ![Add private IP](./media/virtual-network-multiple-ip-addresses-portal/figure3.png)

    > [!NOTE]
    > 静的 IP アドレスを追加するときは、NIC が接続されているサブネット上に未使用の有効なアドレスを指定する必要があります。 選択したアドレスが利用できない場合はポータルで IP アドレスに X が表示され、別のアドレスを選択する必要があります。

    プライベート IP アドレスの**割り当て方法**を"*動的*"に設定した場合は、その選択によって、IP アドレスを指定する必要がなくなります。
3. [OK] をクリックするとブレードが閉じ、次の図に示すように、新しい IP 構成が表示されます。

    ![IP 構成](./media/virtual-network-multiple-ip-addresses-portal/figure4.png)

    **[OK]** をクリックして、**[IP 構成の追加]** ブレードを閉じます。
4. **[追加]** をクリックすると、別の IP 構成を追加できます。または、開いているすべてのブレードを閉じて IP アドレスの追加を完了できます。
5. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載された、お使いのオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。

### <a name="add-a-public-ip-address"></a>パブリック IP アドレスを追加する

パブリック IP アドレスを追加するには、新しい IP 構成または既存の IP 構成にパブリック IP アドレス リソースを関連付けます。

> [!NOTE]
> パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。
> 

### <a name="a-namecreate-public-ipacreate-a-public-ip-address-resource"></a><a name="create-public-ip"></a>パブリック IP アドレス リソースの作成

パブリック IP アドレスは、パブリック IP アドレス リソースの設定の 1 つです。 IP 構成に関連付ける予定で、まだ IP 構成に関連付けられていないパブリック IP アドレス リソースがある場合は、次の手順を省略して、必要に応じてそれに続く手順の 1 つを実行します。 利用できるパブリック IP アドレス リソースがない場合は、次の手順を実行して 1 つ作成します。

1. 必要に応じて、https://portal.azure.com で Azure Portal を開き、サインインします。
3. ポータルで、**[新規]** > **[ネットワーク]** > **[パブリック IP アドレス]** の順に選択します。
4. 次の図に示すように、表示される **[パブリック IP アドレスの作成]** ブレードで **[名前]** を入力し、**[IP アドレスの割り当て]** のタイプ、**[サブスクリプション]**、**[リソース グループ]**、**[場所]** を選択してから、**[作成]** をクリックします。

    ![パブリック IP アドレス リソースの作成](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. パブリック IP アドレス リソースを、IP 構成に関連付けるには、次のいずれかの手順を実行します。

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>パブリック IP アドレス リソースを新しい IP 構成に関連付ける

1. この記事の「[主な手順](#coreadd)」で述べた手順を完了します。
2. **[追加]**をクリックします。 表示される **[IP 構成の追加]** ブレードで、「*IPConfig-4*」という名前の IP 構成を作成します。 次の図に示すように、**[パブリック IP アドレス]** を有効にして、表示された **[パブリック IP アドレスの選択]** ブレードから利用できる既存のパブリック IP アドレス リソースを選択します。

    ![新しい IP 構成](./media/virtual-network-multiple-ip-addresses-portal/figure6.png)

    パブリック IP アドレス リソースを選択したら、**[OK]** をクリックするとブレードが閉じます。 既存のパブリック IP アドレスがない場合は、この記事の「[パブリック IP アドレス リソースの作成](#create-public-ip)」セクションの手順を実行して、パブリック IP アドレスを作成できます。 

3. 次の図に示すように、新しい IP 構成を確認します。

    ![IP 構成](./media/virtual-network-multiple-ip-addresses-portal/figure7.png)

    > [!NOTE]
    > プライベート IP アドレスが明示的に割り当てられていないとしても、すべての IP 設定にはプライベート IP アドレスが必要であるため、IP 設定にはプライベート IP アドレスが自動的に割り当てられています。
    >

4. **[追加]** をクリックすると、別の IP 構成を追加できます。または、開いているすべてのブレードを閉じて IP アドレスの追加を完了できます。
5. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載された、お使いのオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>パブリック IP アドレス リソースを既存の IP 構成に関連付ける

1. この記事の「[主な手順](#coreadd)」で述べた手順を完了します。
2. パブリック IP アドレス リソースを追加するIP構成を選択し、パブリック IP アドレスを有効にして、利用できる既存のパブリック IP アドレス リソースを選択します。 次の図に示した例では、「*myPublicIp3*」というパブリック IP アドレス リソースが「*IPConfig-3*」に関連付けられています。

    ![Existing IP configuration](./media/virtual-network-multiple-ip-addresses-portal/figure8.png)

    パブリック IP アドレス リソースを選択したら、**[保存]** をクリックするとブレードが閉じます。 既存のパブリック IP アドレスがない場合は、この記事の「[パブリック IP アドレス リソースの作成](#create-public-ip)」セクションの手順を実行して、パブリック IP アドレスを作成できます。

3. 次の図に示すように、新しい IP 構成を確認します。

    ![IP 構成](./media/virtual-network-multiple-ip-addresses-portal/figure9.png)

4. **[追加]** をクリックすると、別の IP 構成を追加できます。または、開いているすべてのブレードを閉じて IP アドレスの追加を完了できます。 オペレーティング システムにパブリック IP アドレスは追加しないでください。


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Dec16_HO2-->


