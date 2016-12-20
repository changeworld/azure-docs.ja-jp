---
title: "Linux VM のサイズを変更する方法 | Microsoft Docs"
description: "スケールアップまたは VM のサイズを変更することで Linux 仮想マシンをスケールアップまたはスケールダウンする方法。"
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mikewasson
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: 0032c330288ea37508c919b50a41a5e58fc28e90


---
# <a name="how-to-resize-a-linux-vm"></a>Linux VM のサイズを変更する方法
## <a name="overview"></a>概要
仮想マシン (VM) をプロビジョニングした後、[VM のサイズ][vm-sizes]を変更することで、VM をスケールアップまたはスケールダウンできます。 場合によっては、先に VM の割り当てを解除する必要があります。 これは、VM をホストしているハードウェア クラスターで新しいサイズを使用できない場合に発生する可能性があります。

この記事では、[Azure CLI][azure-cli] を使用して Linux VM のサイズを変更する方法を示します。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="resize-a-linux-vm"></a>Linux VM のサイズを変更する
VM のサイズを変更するには、次の手順を実行します。

1. 次の CLI コマンドを実行します。 このコマンドは、VM がホストされているハードウェア クラスターで使用できる VM のサイズを一覧表示します。
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. 目的のサイズが表示される場合は、次のコマンドを実行して VM のサイズを変更します。
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    この処理中に、VM が再起動します。 再起動後に、既存の OS とデータ ディスクがリマップされます。 一時ディスク上に格納されているデータはすべて失われます。
   
    `--enable-boot-diagnostics` オプションを使用すると、[ブート診断][boot-diagnostics]で、起動に関連するすべてのエラーをログに記録できます。
3. 目的のサイズが表示されない場合は、次のコマンドを実行して VM の割り当てを解除します。その後、サイズを変更し、VM を再起動します。
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > VM の割り当てを解除すると、VM に割り当てられているすべての動的 IP アドレスも解放されます。 OS とデータ ディスクには影響しません。
   > 
   > 

## <a name="next-steps"></a>次のステップ
スケーラビリティを高めるには、複数の VM インスタンスを実行してスケール アウトします。 詳細については、「[仮想マシン スケール セット[scale-set]での Linux マシンの自動スケール]」を参照してください。 

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md



<!--HONumber=Nov16_HO3-->


