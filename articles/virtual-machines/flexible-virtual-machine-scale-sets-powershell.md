---
title: Azure PowerShell を使用してフレキシブル スケール セットに仮想マシンを作成する
description: PowerShell を使用して、フレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 45f998dbba1ffac99fc8d491cb90694a76c11f98
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165757"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-powershell"></a>PowerShell を使用してフレキシブル スケール セットに仮想マシンを作成する

**適用対象:** :heavy_check_mark: フレキシブル スケール セット


この記事では、PowerShell を使用して、フレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。 フレキシブル スケール セットの詳細については、[仮想マシン スケール セットのフレキシブル オーケストレーション モード](flexible-virtual-machine-scale-sets.md)に関するページを参照してください。 

> [!CAUTION]
> オーケストレーション モードは、スケール セットを作成するときに定義され、後で変更または更新することはできません。


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。


## <a name="get-started-with-flexible-scale-sets"></a>フレキシブル スケール セットの使用を開始する

Azure PowerShell を使用してフレキシブル仮想マシン スケール セットを作成します。

### <a name="add-multiple-vms-to-a-scale-set"></a>複数の VM をスケール セットに追加する 

次の例では、仮想マシン プロファイル (VM の種類、ネットワーク構成、ストレージの種類など)、および作成するインスタンスの数 (SKU 容量 = 2) を指定します。 

1. IP アドレスの構成を作成します。

    ```azurepowershell-interactive
    $ipConfig = New-AzVmssIpConfig -Name "myIPConfig"
    -SubnetId "${vnetid}/subnets/default" `
    -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id
    ```

1. 構成オブジェクトを作成します。

    構成オブジェクトには、スケール セットを作成するためのコア情報が格納されます。

    ```azurepowershell-interactive
    $vmssConfig = New-AzVmssConfig -Location $loc
    -SkuCapacity 2 -SkuName "Standard_DS1_v2"
    -OrchestrationMode 'Flexible' `
    -PlatformFaultDomainCount 1
    ```

1. ギャラリーから仮想マシン イメージを参照します。

    ```azurepowershell-interactive
    Set-AzVmssStorageProfile $vmssConfig -OsDiskCreateOption "FromImage"
    -ImageReferencePublisher "Canonical" -ImageReferenceOffer "UbuntuServer"
    -ImageReferenceSku "18.04-LTS" `
    -ImageReferenceVersion "latest"
    ```

1. 仮想マシンでの認証に関する情報を設定します。

    ```azurepowershell-interactive
    Set-AzVmssOsProfile $vmssConfig -AdminUsername $cred.UserName
    -AdminPassword $cred.Password -ComputerNamePrefix $vmname
    ```

1. 仮想ネットワークを構成オブジェクトにアタッチします。

    ```azurepowershell-interactive
    Add-AzVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig
    -Name "network-config" -Primary $true
    -IPConfiguration $ipConfig `
    -NetworkApiVersion '2020-11-01'
    ```

1. 構成オブジェクトを使用してスケール セットを作成します。

    この手順は完了までに数分かかることがあります。 

    ```azurepowershell-interactive
    New-AzVmss -ResourceGroupName $rgname
    -Name $vmssName `
    -VirtualMachineScaleSet $vmssConfig
    ```

### <a name="add-a-single-vm-to-a-scale-set"></a>1 つの VM をスケール セットに追加する

次の例では、VM プロファイルを指定せずにフレキシブルなスケール セットを作成する方法を示します。この場合、障害ドメイン数は 1 に設定されます。 仮想マシンが作成され、フレキシブル スケール セットに追加されます。

1. Azure PowerShell にログインし、デプロイのサブスクリプションと変数を指定します。 

    ```azurepowershell-interactive
    Connect-AzAccount
    Set-AzContext `
        -Subscription "00000000-0000-0000-0000-000000000" 
    
    $loc = "eastus" 
    $rgname = "myResourceGroupFlexible" 
    $vmssName = "myFlexibleVMSS" 
    $vmname = "myFlexibleVM"
    ```

1. ネットワークや VM SKU のように、VM プロファイル パラメーターは指定しません。

    ```azurepowershell-interactive
    $VmssConfigWithoutVmProfile = new-azvmssconfig -location $loc -platformfaultdomain 1 `
    $VmssFlex = new-azvmss -resourcegroupname $rgname -vmscalesetname $vmssName -virtualmachinescaleset $VmssConfigWithoutVmProfile 
    ```
 
1. フレキシブル スケール セットに VM を追加します。

    ```azurepowershell-interactive
    $vm = new-azvm -resourcegroupname $rgname -location $loc -name $vmname -credential $cred -domainnamelabel $domainName -vmssid $VmssFlex.id 
    ```


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure portal でフレキシブル スケール セットを作成する方法について学習する](flexible-virtual-machine-scale-sets-portal.md)
