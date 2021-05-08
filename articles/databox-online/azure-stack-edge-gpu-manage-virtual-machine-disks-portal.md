---
title: Azure portal を使用して Azure Stack Edge Pro GPU、Pro R、Mini R の VM ディスクを管理する
description: Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、Azure Stack Edge Mini R にデプロイされている VM 上のデータ ディスクの追加または切断など、Azure portal を使用してディスクを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080045"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure portal を使用して Azure Stack Edge Pro GPU の VM 上のディスクを管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure portal を使用して、Azure Stack Edge Pro デバイスにデプロイされている仮想マシン (VM) 上のディスクをプロビジョニングできます。 ローカルの Azure Resource Manager 経由でデバイスにディスクがプロビジョニングされ、デバイスの容量が消費されます。 ディスクの追加、ディスクの切断などの操作は、Azure portal 経由で行うことができます。これにより、ローカルの Azure Resource Manager を呼び出し、ストレージをプロビジョニングします。 

この記事では、Azure portal を使用した既存の VM へのデータ ディスクの追加、データ ディスクの切断、また、最後に VM 自体のサイズ変更の方法について説明します。 

        
## <a name="about-disks-on-vms"></a>VM 上のディスクについて

VM には、OS ディスクとデータ ディスクを含めることができます。 デバイスにデプロイされたどの仮想マシンにも、1 つのオペレーティング システム ディスクが取り付けられています。 この OS ディスクには、VM の作成時に選択された OS がプリインストールされています。 このディスクにはブートボリュームが含まれています。

> [!NOTE]
> デバイス上の VM の OS ディスク サイズを変更することはできません。 OS ディスク サイズは、選択した VM のサイズによって決まります。 


一方、データ ディスクは、デバイス上で実行されている VM に接続されたマネージド ディスクです。 データ ディスクは、アプリケーション データを格納するために使用されます。 データ ディスクは通常、SCSI ドライブです。 VM のサイズによって、VM に接続できるデータ ディスクの数が決まります。 既定では、ディスクをホストするために Premium Storage が使用されます。

デバイスにデプロイされた VM に一時ディスクが含まれる場合があります。 一時ディスクはアプリケーションやプロセスのために短期間の保存場所を提供するものであり、ページやスワップ ファイルなどのデータ格納のみを意図しています。 一時ディスクのデータは、メンテナンス イベント中、または VM の再デプロイ時に失われる可能性があります。 VM を正常に標準再起動している間、一時ディスク上のディスクは残ります。 


## <a name="prerequisites"></a>前提条件

Azure portal を使用してデバイスで実行されている VM 上のディスクの管理を開始する前に、次のことを確認してください。


1. アクティブ化済みの Azure Stack Edge Pro GPU デバイスにアクセスできること。 また、デバイスでコンピューティング用のネットワーク インターフェイスを有効にしていること。 このアクションにより、VM 上のそのネットワーク インターフェイスに仮想スイッチが作成されます。 
    1. デバイスのローカル UI で、 **[Compute]\(コンピューティング\)** に移動します。 仮想スイッチの作成に使用するネットワーク インターフェイスを選択します。

        > [!IMPORTANT] 
        > コンピューティング用に構成できるポートは 1 つだけです。

    1. そのネットワーク インターフェイスでコンピューティングを有効にします。 そのネットワーク インターフェイスに対応する仮想スイッチが Azure Stack Edge Pro GPU によって作成、管理されます。

1. デバイスには、少なくとも 1 つの VM がデプロイされています。 この VM を作成するには、「[Azure portal を使用した Azure Stack Edge Pro への VM のデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)」の手順を参照してください。



## <a name="add-a-data-disk"></a>データ ディスクの追加

デバイスにデプロイされている仮想マシンにディスクを追加するには、こちらの手順に従います。 

1. データ ディスクを追加する仮想マシンにアクセスし、 **[概要]** ページにアクセスします。 **[ディスク]** を選択します。
    
    ![[概要] ページで [ディスク] を選択する](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. **[ディスク]** ブレードの **[データ ディスク]** で、 **[新しいディスクを作成し接続する]** を選択します。

    ![新しいディスクの作成と接続](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. **[新しいディスクの作成]** ブレードで、次のパラメーターを入力します。

    
    |フィールド  |説明  |
    |---------|---------|
    |名前     | リソース グループ内の一意の名前。 名前は、データ ディスクの作成後に変更することはできません。     |
    |サイズ| データ ディスクのサイズ (GiB)。 データ ディスクの最大サイズは、選択した VM のサイズによって決まります。 ディスクをプロビジョニングする場合は、デバイスの実際の領域と、容量を消費する実行中の他の VM ワークロードも考慮する必要があります。  |         

    ![[新しいディスクの作成] ブレード](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    **[OK]** を選択して続行します。

1. **[概要]** ページの **[ディスク]** に、新しいディスクに対応するエントリが表示されます。 既定値をそのまま使用するか、有効な論理ユニット番号 (LUN) をディスクに割り当てて、 **[保存]** を選択します。 LUN は、SCSI ディスクの一意識別子です。 詳細については、「[LUN とは](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun)」を参照してください。

    ![[概要] ページ上の新しいディスク](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. ディスクの作成が進行中であることが通知されます。 ディスクが正常に作成されると、仮想マシンが更新されます。 

    ![ディスク作成の通知](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. **[概要]** ページに戻ります。 ディスクの一覧が更新され、新しく作成されたデータ ディスクが表示されます。

    ![データディスクの更新された一覧](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>データ ディスクを変更する

デバイスにデプロイされている仮想マシンに関連付けられたディスクを変更するには、こちらの手順に従います。

1. 変更するデータ ディスクがある仮想マシンに移動し、 **[概要]** ページに移動します。 **[ディスク]** を選択します。

1. データ ディスクの一覧で、変更するディスクを選択します。 選択したディスクの右端で、編集アイコン (鉛筆) を選択します。  

    ![変更するディスクを選択する](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. **[ディスクの変更]** ブレードでは、ディスクのサイズのみを変更できます。 ディスクに関連付けられた名前は、作成後に変更することはできません。 **[サイズ]** を変更し、変更を保存します。

    ![データ ディスクのサイズを変更する](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > データ ディスクは、拡張のみ可能です。ディスクを縮小することはできません。

1. **[概要]** ページで、ディスクの一覧が最新の情報に更新され、更新されたディスクが表示されます。


## <a name="attach-an-existing-disk"></a>既存のディスクの接続

デバイスにデプロイされている仮想マシンに既存のディスクを接続するには、こちらの手順に従います。

1. 既存のディスクを接続する仮想マシンにアクセスし、 **[概要]** ページに移動します。 **[ディスク]** を選択します。
    
    ![[ディスク] を選択する ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. **[ディスク]** ブレードの **[データ ディスク]** で、 **[既存のディスクの接続]** を選択します。

    ![[既存のディスクの接続] を選択する](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. 既定の LUN を受け入れるか、有効な LUN を割り当てます。 ドロップダウン リストから既存のデータ ディスクを選択します。 [保存] を選択します。

    ![既存のディスクを選択する](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    **[保存]** を選択して続行します。

1. 仮想マシンが更新されたことを示す通知が表示されます。 VM が更新されたら、 **[概要]** ページに戻ります。 ページを最新の情報に更新して、データ ディスクの一覧に新しく接続されたディスクを表示します。

    ![[概要] ページでデータ ディスクの更新された一覧を表示する](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>データ ディスクの切断

デバイスにデプロイされている仮想マシンに関連付けられたデータ ディスクを切断または削除するには、こちらの手順に従います。

> [!NOTE]
> - VM の実行中にデータ ディスクを削除することができます。 VM からディスクを切断する前に、それがアクティブに使用されていないことを確認してください。
> - ディスクを切断した場合、自動的には削除されません。

1. データ ディスクを切断する仮想マシンにアクセスし、 **[概要]** ページに移動します。 **[ディスク]** を選択します。

    ![[ディスク] を選択する](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. ディスクの一覧で、切断するディスクを選択します。 選択したディスクの右端で、切断アイコン (ばつ印) を選択します。 選択したエントリが切断されます。 **[保存]** を選択します。 

    ![切断するディスクを選択する](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. ディスクが切断されると、仮想マシンが更新されます。 **[概要]** ページを最新の情報に更新して、データ ディスクの更新された一覧を表示します。

    ![[保存] を選択する](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>次のステップ

Azure Stack Edge Pro デバイスに仮想マシンをデプロイする方法については、[Azure portal を使用した仮想マシンのデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)に関するページを参照してください。
