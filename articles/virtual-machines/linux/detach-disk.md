---
title: Linux VM からデータ ディスクを切断する - Azure | Microsoft Docs
description: CLI 2.0 または Azure Portal を使用して、Azure の仮想マシンからデータ ディスクをデタッチする方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 572fe5bd4d6d79bb9dd94353732e273282e2a0af
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>データ ディスクを Linux 仮想マシンから切断する方法

仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。 そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。 

> [!WARNING]
> ディスクを切断した場合、自動的には削除されません。 Premium Storage のサブスクリプションにお申込みいただいている場合は、ディスクのストレージ料金が引き続き発生します。 詳細については [Premium Storage 利用時の料金と課金](../windows/premium-storage.md#pricing-and-billing)に関する記事を参照してください。 
> 
> 

再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。  

## <a name="detach-a-data-disk-using-cli-20"></a>CLI 2.0 を使用してデータ ディスクを切断する

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

ディスクはストレージに残りますが、仮想マシンからは切断されています。


## <a name="detach-a-data-disk-using-the-portal"></a>ポータルを使用してデータ ディスクを切断する方法
1. 左側のメニューで **[Virtual Machines]** を選択します。
2. 切断するデータ ディスクが接続されている仮想マシンを選択し、**[停止]** をクリックして VM の割り当てを解除します。
3. 仮想マシンのウィンドウで、**[ディスク]** を選択します。
4. **[ディスク]** ウィンドウの上部にある **[編集]** を選択します。
5. **[ディスク]** ウィンドウで、切断するデータ ディスクの右端にある ![切断ボタンのイメージ](./media/detach-disk/detach.png) 切断ボタンをクリックします。
5. ディスクが削除されたら、ウィンドウの上部にある [保存] をクリックします。
6. 仮想マシンのウィンドウで **[概要]** をクリックし、ウィンドウの上部にある **[開始]** ボタンをクリックして VM を再起動します。

ディスクはストレージに残りますが、仮想マシンからは切断されています。


## <a name="next-steps"></a>次の手順
データ ディスクを再利用する場合は、[別の VM にそのデータ ディスクをアタッチ](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)します。

