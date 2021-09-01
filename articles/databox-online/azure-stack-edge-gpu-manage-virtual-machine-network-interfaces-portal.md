---
title: Azure portal を使用して Azure Stack Edge Pro 上の VM ネットワーク インターフェイスを管理する方法
description: Azure portal を使用して Azure Stack Edge Pro GPU にデプロイされている VM 上のネットワーク インターフェイスを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 27927f1e5f31243d4d2dcbfd9ea1f4da09b8b544
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736810"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure portal を使用して Azure Stack Edge Pro GPU の VM 上のネットワーク インターフェイスを管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure portal、テンプレート、Azure PowerShell コマンドレット、および Azure CLI/Python スクリプトを使用して、Azure Stack Edge デバイス上に仮想マシン (VM) を作成および管理できます。 この記事では、Azure portal を使用して、Azure Stack Edge デバイス上で実行されている VM 上のネットワーク インターフェイスを管理する方法について説明します。

VM を作成するときに、作成する仮想ネットワーク インターフェイスを 1 つ指定します。 仮想マシンの作成後に、1 つまたは複数のネットワーク インターフェイスを仮想マシンに追加できます。 既存のネットワーク インターフェイスについて、既定のネットワーク インターフェイス設定を変更することもできます。

この記事では、既存の VM にネットワーク インターフェイスを追加する方法、IP の種類 (静的と動的) などの既存の設定を変更する方法、既存のインターフェイスをデタッチまたは削除する方法について説明します。

        
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

1. デバイスには、少なくとも 1 つの VM がデプロイされています。 この VM を作成するには、[Azure portal を使用した Azure Stack Edge Pro への VM のデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)に関する記事の手順を参照してください。

1. VM は **[停止]** 状態になっている必要がある。 VM を停止するには、 **[仮想マシン]** に移動し、停止する VM を選択します。 VM の **[詳細]** ページで **[停止]** を選択した後、確認を求められたら **[はい]** を選択します。 ネットワーク インターフェイスを追加、編集、または削除する前に、VM を停止する必要があります。

    ![Azure Stack Edge で仮想マシンを停止するときの確認プロンプトを示すスクリーンショット。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>ネットワーク インターフェイスの追加

デバイスにデプロイされている仮想マシンにネットワーク インターフェイスを追加するには、次の手順に従います。<!--There's no obvious way to add a new NIC to a VM or to an Edge resource group in the portal. To update these procedures, I need to create my own test VM, which I can start and stop, create a new NIC for, detach a NIC from the stopped VM, etc.-->

1. 停止した仮想マシンに移動して、 **[ネットワーク]** を選択します。
    
    ![仮想マシンの [詳細] タブを示すスクリーンショット。 VM の [ネットワーク] ブレードを開く [ネットワーク] ラベルが強調されています。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. **[ネットワーク]** ブレードのコマンドバーで、 **[+ ネットワーク インターフェイスの追加]** を選択します。

    ![仮想マシンの [ネットワーク] ブレードを示すスクリーンショット。 [+ ネットワーク インターフェイスの追加] ボタンが強調されています。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. **[ネットワーク インターフェイスの追加]** ブレードで、次のパラメーターを入力します。

    |フィールド    |説明  |
    |---------|-------------|
    |名前     | エッジ リソース グループ内の一意の名前。 名前は、ネットワーク インターフェイスの作成後に変更することはできません。 複数のネットワーク インターフェイスを簡単に管理するには、[名前付け規則](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)に記載されている提案を使用します。     |
    |エッジ リソース グループを選択する |ネットワーク インターフェイスを追加するエッジ リソース グループを選択します。|   
    |仮想ネットワーク| ネットワーク インターフェイス上でコンピューティングを有効にしたときに、デバイス上に作成された仮想スイッチに関連付けられる仮想ネットワーク。 デバイスに関連付けられる仮想ネットワークは 1 つだけです。         |
    |Subnet     | 選択した仮想ネットワーク内のサブネット。 このフィールドには、コンピューティングを有効にしたネットワーク インターフェイスに関連付けられているサブネットが自動的に入力されます。         |
    |IP アドレスの割り当て   | ネットワーク インターフェイスの静的または動的 IP。 静的 IP は、指定されたサブネットの範囲内の使用可能な空き IP アドレスである必要があります。 環境に DHCP サーバーが存在する場合は、動的を選択します。        |

    ![仮想マシンの [ネットワーク インターフェイスの追加] ブレードを示すスクリーンショット。 [Add]\(追加\) ボタンが強調表示されています。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. ネットワーク インターフェイスの作成が進行中であることが通知されます。

    ![ネットワーク インターフェイスが作成されている通知を示すスクリーンショット。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  ネットワーク インターフェイスが正常に作成されると、ネットワーク インターフェイスの一覧が更新され、新しく作成されたインターフェイスが表示されます。

    ![仮想マシンの [ネットワーク] ブレードのスクリーンショット。 新しく作成された仮想マシンのエントリが強調されています。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>ネットワーク インターフェイスの編集

デバイスにデプロイされている仮想マシンに関連付けられたネットワーク インターフェイスを編集するには、次の手順に従います。

1. 停止した仮想マシンに移動し、仮想マシンの **[詳細]** で **[ネットワーク]** を選択します。

1. ネットワーク インターフェイスの一覧で、編集するインターフェイスを選択します。 選択したネットワーク インターフェイスの右端で、[編集] アイコン (鉛筆) を選択します。  

    ![仮想マシンの [ネットワーク] ブレードを示すスクリーンショット。 ネットワーク インターフェイスの名前と、エントリの鉛筆アイコンが強調されています。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. **[ネットワーク インターフェイスの編集]** ブレードでは、ネットワーク インターフェイスの IP 割り当てのみを変更できます。 ネットワーク インターフェイスに関連付けられている名前、エッジ リソース グループ、仮想ネットワーク、サブネットは、作成した後で変更することはできません。 **IP の割り当て** を静的に変更し、変更を保存します。

    ![仮想マシンの ネットワーク インターフェイスの編集」ブレードのスクリーンショット。 IP アドレスの割り当て領域と [保存] ボタンが強調されています。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. ネットワーク インターフェイスの一覧が更新され、更新されたネットワーク インターフェイスが表示されます。


## <a name="detach-a-network-interface"></a>ネットワーク インターフェイスの切断

デバイスにデプロイされている仮想マシンに関連付けられたネットワーク インターフェイスを切断または削除するには、次の手順に従います。

1. 停止した仮想マシンに移動し、仮想マシンの **[詳細]** で **[ネットワーク]** を選択します。

1. ネットワーク インターフェイスの一覧で、編集するインターフェイスを選択します。 選択したネットワーク インターフェイスの右端で、[切断] アイコン (プラグの抜き取り) を選択します。  

    ![仮想マシンの [ネットワーク] ブレードのスクリーンショット。 ネットワーク インターフェイスの名前と、そのデタッチ アイコンが強調されています。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. ネットワーク インターフェイスのデタッチの確認を求めるメッセージが表示されます。 **[はい]** を選択します。

    ![仮想マシンからのネットワーク インターフェイスのデタッチの意思を確認する通知を示すスクリーンショット。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-2.png)

    インターフェイスが完全に切断されると、ネットワーク インターフェイスの一覧が更新され、残りのインターフェイスが表示されます。


## <a name="delete-a-network-interface"></a>ネットワーク インターフェイスの削除

仮想マシンにアタッチされていないネットワーク インターフェイスを削除するには、次の手順のようにします。

1. **[仮想マシン]** に移動した後、 **[リソース]** ページに移動します。 **[ネットワーク]** を選択します。
    
    ![仮想マシンの [リソース] ページの [ネットワーク] タブを示すスクリーンショット。 [リソース] ペインと [ネットワーク] タブのラベルが強調されています。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-1.png)

1. **[ネットワーク]** ブレードで、削除するネットワーク インターフェイスの削除アイコン (ごみ箱) を選択します。 削除アイコンは、VM にアタッチされていないネットワーク インターフェイスに対してだけ表示されます。

    ![仮想マシン リソースの [ネットワーク] ブレードを示すスクリーンショット。 アタッチされていないネットワーク インターフェイスの削除アイコンが強調されています。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-2.png)

1. ネットワーク インターフェイスの削除の確認を求めるメッセージが表示されます。 この操作を元に戻すことはできません。 **[はい]** を選択します。

    ![選択したネットワーク インターフェイスの削除の確認を求める通知のスクリーンショット。](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-3.png)
  
    ネットワーク インターフェイスの削除が完了すると、ネットワーク インターフェイスが一覧から削除されます。

## <a name="next-steps"></a>次のステップ

Azure Stack Edge Pro デバイスに仮想マシンをデプロイする方法については、[Azure portal を使用した仮想マシンのデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)に関するページを参照してください。
