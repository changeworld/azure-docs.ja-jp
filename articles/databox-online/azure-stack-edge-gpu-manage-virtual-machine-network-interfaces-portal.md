---
title: Azure portal を使用して Azure Stack Edge Pro 上の VM ネットワーク インターフェイスを管理する方法
description: Azure portal を使用して Azure Stack Edge Pro GPU にデプロイされている VM 上のネットワーク インターフェイスを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 3e709b04b4eac60e6cc0ba3e53eb77583162dfef
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078895"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure portal を使用して Azure Stack Edge Pro GPU の VM 上のネットワーク インターフェイスを管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure portal、テンプレート、Azure PowerShell コマンドレット、および Azure CLI/Python スクリプトを使用して、Azure Stack Edge デバイス上に仮想マシン (VM) を作成および管理できます。 この記事では、Azure portal を使用して、Azure Stack Edge デバイス上で実行されている VM 上のネットワーク インターフェイスを管理する方法について説明します。 

VM を作成するときに、作成する仮想ネットワーク インターフェイスを 1 つ指定します。 仮想マシンの作成後に、1 つまたは複数のネットワーク インターフェイスを仮想マシンに追加できます。 既存のネットワーク インターフェイスについて、既定のネットワーク インターフェイス設定を変更することもできます。

この記事では、既存の VM にネットワーク インターフェイスを追加する、IP の種類 (静的と動的) などの既存の設定を変更する、最終的に既存のインターフェイスを削除または切断する方法について説明します。 

        
## <a name="about-network-interfaces-on-vms"></a>VM 上のネットワーク インターフェイスについて

ネットワーク インターフェイスを使用すると、Azure Stack Edge Pro デバイスで実行されている仮想マシン (VM) が Azure およびオンプレミスのリソースと通信できるようになります。 デバイス上でコンピューティング ネットワークのポートを有効にすると、そのネットワーク インターフェイス上に仮想スイッチが作成されます。 その後、この仮想スイッチは、VM、コンテナー化されたアプリケーションなどのコンピューティング ワークロードをデバイスにデプロイするために使用されます。 

デバイスでサポートされる仮想スイッチは 1 つだけですが、仮想ネットワーク インターフェイスは複数です。 VM 上の各ネットワーク インターフェイスには、静的または動的 IP アドレスが割り当てられます。 VM 上の複数のネットワーク インターフェイスに IP アドレスが割り当てられている場合、VM 上で特定の機能が有効になります。 たとえば、VM では、異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1 つのサーバーでホストできます。 デバイス上の VM は、ファイアウォールやロード バランサーなどのネットワーク仮想アプライアンスとして機能できます。 <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>前提条件

Azure portal を使用してデバイスで VM の管理を開始する前に、次のことを確認してください。

1. アクティブ化済みの Azure Stack Edge Pro GPU デバイスにアクセスできること。 デバイスでコンピューティング用のネットワーク インターフェイスを有効にしていること。 このアクションにより、VM 上のそのネットワーク インターフェイスに仮想スイッチが作成されます。 
    1. デバイスのローカル UI で、 **[Compute]\(コンピューティング\)** に移動します。 仮想スイッチの作成に使用するネットワーク インターフェイスを選択します。

        > [!IMPORTANT] 
        > コンピューティング用に構成できるポートは 1 つだけです。

    1. そのネットワーク インターフェイスでコンピューティングを有効にします。 そのネットワーク インターフェイスに対応する仮想スイッチが Azure Stack Edge Pro GPU によって作成、管理されます。

1. 少なくとも 1 つの VM がデバイスにデプロイされていること。 この VM を作成するには、[Azure portal を使用した Azure Stack Edge Pro への VM のデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)に関する記事の手順を参照してください。

1. VM は **[停止]** 状態になっている必要がある。 VM を停止するには、 **[仮想マシン] > [概要]** にアクセスし、停止する VM を選択します。 [VM のプロパティ] ページで **[停止]** を選択し、確認を指示されたら **[はい]** を選択します。 ネットワーク インターフェイスを追加、編集、または削除する前に、VM を停止する必要があります。

    ![VM のプロパティ ページからの VM の停止](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>ネットワーク インターフェイスの追加

デバイスにデプロイされている仮想マシンにネットワーク インターフェイスを追加するには、次の手順に従います。 

1. 停止した仮想マシンにアクセスし、次に、 **[VM のプロパティ]** ページにアクセスします。 **[ネットワーク]** を選択します。
    
    ![[VM のプロパティ] ページの [ネットワーク] の選択](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. **[ネットワーク]** ブレードのコマンドバーで、 **[+ ネットワーク インターフェイスの追加]** を選択します。

    ![ネットワーク インターフェイスの追加の選択](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. **[ネットワーク インターフェイスの追加]** ブレードで、次のパラメーターを入力します。

    
    |列 1  |列 2  |
    |---------|---------|
    |名前     | リソース グループ内の一意の名前。 名前は、ネットワーク インターフェイスの作成後に変更することはできません。 複数のネットワーク インターフェイスを簡単に管理するには、[名前付け規則](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)に記載されている提案を使用します。     |
    |仮想ネットワーク| ネットワーク インターフェイス上でコンピューティングを有効にしたときに、デバイス上に作成された仮想スイッチに関連付けられる仮想ネットワーク。 デバイスに関連付けられる仮想ネットワークは 1 つだけです。         |         
    |Subnet     | 選択した仮想ネットワーク内のサブネット。 このフィールドには、コンピューティングを有効にしたネットワーク インターフェイスに関連付けられているサブネットが自動的に入力されます。         |       
    |IP 割り当て   | ネットワーク インターフェイスの静的または動的 IP。 静的 IP は、指定されたサブネットの範囲内の使用可能な空き IP アドレスである必要があります。 環境に DHCP サーバーが存在する場合は、動的を選択します。        | 

    ![ネットワーク インターフェイス ブレードの追加](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. ネットワーク インターフェイスの作成が進行中であることが通知されます。

    ![ネットワーク インターフェイスが作成されたときの通知](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  ネットワーク インターフェイスが正常に作成されると、ネットワーク インターフェイスの一覧が更新され、新しく作成されたインターフェイスが表示されます。

    ![ネットワーク インターフェイスの更新された一覧](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>ネットワーク インターフェイスの編集

デバイスにデプロイされている仮想マシンに関連付けられたネットワーク インターフェイスを編集するには、次の手順に従います。

1. 停止した仮想マシンにアクセスし、 **[VM のプロパティ]** ページにアクセスします。 **[ネットワーク]** を選択します。

1. ネットワーク インターフェイスの一覧で、編集するインターフェイスを選択します。 選択したネットワーク インターフェイスの右端で、[編集] アイコン (鉛筆) を選択します。  

    ![編集するネットワーク インターフェイスの選択](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. **[ネットワーク インターフェイスの編集]** ブレードでは、ネットワーク インターフェイスの IP 割り当てのみを変更できます。 ネットワーク インターフェイスに関連付けられている名前、仮想ネットワーク、およびサブネットは、作成後に変更することはできません。 **IP 割り当て** を静的に変更し、変更を保存します。

    ![ネットワーク インターフェイスの IP 割り当ての変更](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. ネットワーク インターフェイスの一覧が更新され、更新されたネットワーク インターフェイスが表示されます。


## <a name="detach-a-network-interface"></a>ネットワーク インターフェイスの切断

デバイスにデプロイされている仮想マシンに関連付けられたネットワーク インターフェイスを切断または削除するには、次の手順に従います。

1. 停止した仮想マシンにアクセスし、 **[VM のプロパティ]** ページにアクセスします。 **[ネットワーク]** を選択します。

1. ネットワーク インターフェイスの一覧で、編集するインターフェイスを選択します。 選択したネットワーク インターフェイスの右端で、[切断] アイコン (プラグの抜き取り) を選択します。  

    ![切断するネットワーク インターフェイスの選択](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. インターフェイスが完全に切断されると、ネットワーク インターフェイスの一覧が更新され、残りのインターフェイスが表示されます。

## <a name="next-steps"></a>次のステップ

Azure Stack Edge Pro デバイスに仮想マシンをデプロイする方法については、[Azure portal を使用した仮想マシンのデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)に関するページを参照してください。
