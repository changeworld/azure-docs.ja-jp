<properties
   pageTitle="Linux VM のサイズを変更する方法 | Microsoft Azure"
   description="スケールアップまたは VM のサイズを変更することで Linux 仮想マシンをスケールアップまたはスケールダウンする方法。"
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# Linux VM のサイズを変更する方法

## Overview 

仮想マシン (VM) をプロビジョニングした後、[VM のサイズ][vm-sizes]を変更することで、VM をスケールアップまたはスケールダウンできます。場合によっては、先に VM の割り当てを解除する必要があります。これは、VM をホストしているハードウェア クラスターで新しいサイズを使用できない場合に発生する可能性があります。

この記事では、[Azure CLI][azure-cli] を使用して Linux VM のサイズを変更する方法を示します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] クラシック デプロイメント モデル。


## Linux VM のサイズを変更する 

VM のサイズを変更するには、次の手順を実行します。

1. 次の CLI コマンドを実行します。このコマンドは、VM がホストされているハードウェア クラスターで使用できる VM のサイズを一覧表示します。

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. 目的のサイズが表示される場合は、次のコマンドを実行して VM のサイズを変更します。

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    この処理中に、VM が再起動します。再起動後に、既存の OS とデータ ディスクがリマップされます。一時ディスク上に格納されているデータはすべて失われます。

    `--enable-boot-diagnostics` オプションを使用すると、[ブート診断][boot-diagnostics]で、起動に関連するすべてのエラーをログに記録できます。

3. 目的のサイズが表示されない場合は、次のコマンドを実行して VM の割り当てを解除します。その後、サイズを変更し、VM を再起動します。

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] VM の割り当てを解除すると、VM に割り当てられているすべての動的 IP アドレスも解放されます。OS とデータ ディスクには影響しません。
   
## 次のステップ

スケーラビリティを高めるには、複数の VM インスタンスを実行してスケール アウトします。詳細については、「[仮想マシン スケール セットでの Linux マシンの自動スケール][scale-set]」を参照してください。

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md
[vm-sizes]: virtual-machines-linux-sizes.md

<!---HONumber=AcomDC_0824_2016-->