---
title: Azure portal を使用して Azure Stack Edge Pro GPU、Pro R、Mini R 上の VM のサイズを変更する
description: Azure portal を使用して Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、Azure Stack Edge Mini R で実行されている仮想マシン (VM) のサイズを変更する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079866"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure portal を使用して Azure Stack Edge Pro GPU 上の VM のサイズを変更する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスにデプロイされている仮想マシン (VM) のサイズを変更する方法について説明します。

       
## <a name="about-vm-sizing"></a>VM のサイズ設定について

VM のサイズにより、VM で利用できる計算リソース (CPU、GPU、メモリなど) の量が決定されます。 仮想マシンは、アプリケーション ワークロードに適した VM サイズを使用して作成する必要があります。 

すべてのマシンが同じハードウェア上で実行される場合でも、マシンのサイズによってディスク アクセスの制限が異なります。 これは、VM 間のディスク アクセス全体を管理するのに役立ちます。 ワークロードが増えた場合は、既存の仮想マシンのサイズを変更することもできます。

詳細については、[デバイスでサポートされている VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md)に関するページを参照してください。


## <a name="prerequisites"></a>前提条件

Azure portal を使用して、デバイスで実行されている VM のサイズを変更する前に、次のことを確認してください。

1. 少なくとも 1 つの VM がデバイスにデプロイされている。 この VM を作成するには、[Azure portal を使用した Azure Stack Edge Pro への VM のデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)に関する記事の手順を参照してください。

1. VM は **[停止]** 状態になっている必要がある。 VM を停止するには、 **[仮想マシン] > [概要]** にアクセスし、停止する VM を選択します。 [概要] ページで **[停止]** を選択し、確認を求めるダイアログが表示されたら **[はい]** を選択します。 VM のサイズを変更する前に、VM を停止する必要があります。

    ![[概要] ページから VM を停止する](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>VM のサイズを変更する

デバイスにデプロイされている仮想マシンのサイズを変更するには、こちらの手順に従います。 

1. 停止した仮想マシンにアクセスし、 **[概要]** ページにアクセスします。 **[VM size (change)]\(VM サイズ (変更)\)** を選択します。
    
    ![[概要] ページで VM サイズの変更を選択する](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. **[Change VM size]\(VM サイズの変更\)** ブレードのコマンド バーで **[VM サイズ]** を選択し、 **[変更]** を選択します。

    ![新しい VM サイズを選択する](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. 仮想マシンが更新されていることを示す通知が表示されます。 仮想マシンが正常に更新されると、 **[概要]** ページが更新され、サイズ変更された VM が表示されます。

    ![VM をサイズ変更する ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>次のステップ

Azure Stack Edge Pro デバイスに仮想マシンをデプロイする方法については、[Azure portal を使用した仮想マシンのデプロイ](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)に関するページを参照してください。
