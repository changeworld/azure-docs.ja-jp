---
title: "一般化された Azure VM からの VM イメージのキャプチャ | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで作成された一般化された Azure VM から VM イメージをキャプチャする方法について説明します"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 8f1d488fd8f71bf90c8bf7b7c1544445ffbd7686
ms.contentlocale: ja-jp
ms.lasthandoff: 05/27/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>一般化された Azure VM から VM イメージをキャプチャする方法
この記事では、Azure PowerShell を使用して、一般化された Azure VM のイメージを作成する方法について説明します。 その後、そのイメージを使用して、別の VM を作成できます。 イメージには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。 イメージには仮想ネットワーク リソースは含まれないため、新しい VM を作成するときにこれらのリソースを設定する必要があります。 

## <a name="prerequisites"></a>前提条件
Azure PowerShell Version 1.0.x 以降がインストールされている必要があります。 PowerShell がインストールされていない場合、インストール手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview) 」を参照してください。

## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep を使用して Windows VM を一般化する

特に重要な点は、Sysprep がすべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 Sysprep の詳細については、「 [Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳しくは、「 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 初めて VHD を Azure にアップロードする前に Sysprep を実行する場合、Sysprep の実行前に [VM の準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ができていることを確認してください。 
> 
> 

1. Windows 仮想マシンへのサインイン
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを **%windir%\system32\sysprep** に変更し、`sysprep.exe` を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
5. **[OK]**をクリックします。
   
    ![Sysprep の開始](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep は完了時に仮想マシンをシャットダウンします。 VM は再起動しないでください。


`sudo waagent -deprovision+user` を使用して Linux VM を一般化してから、PowerShell を使用して VM をキャプチャすることもできます。 CLI を使用して VM をキャプチャする方法については、[Azure CLI で Linux 仮想マシンを一般化してキャプチャする方法](../linux/capture-image.md)に関する記事をご覧ください。

## <a name="log-in-to-azure-powershell"></a>Azure PowerShell へのログイン
1. Azure PowerShell を開き、Azure アカウントにサインインします。
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Azure アカウント資格情報を入力するためのポップアップ ウィンドウが開きます。
2. 使用可能なサブスクリプションのサブスクリプション ID を取得します。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. このサブスクリプション ID を使用して、適切なサブスクリプションを設定します。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>VM の割り当てを解除して、一般化状態に設定
1. VM リソースの割り当てを解除します。
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Azure Portal で VM の *[状態]* が **[停止済み]** から **[停止済み (割り当て解除)]** に変わります。
2. 仮想マシンの状態を **[一般化]**に設定します。 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. VM の状態を確認します。 VM の **[OSState/generalized (OS の状態/一般化)]** セクションの **[DisplayStatus (ステータス表示)]** が **[VM が汎用化されました]** に設定されています。  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>イメージの作成
1. 次のコマンドを使用して、仮想マシンのイメージをコピー先ストレージ コンテナーにコピーします。 イメージは、元の仮想マシンと同じストレージ アカウントに作成されます。 `-Path` パラメーターにより、JSON テンプレートのコピーがローカルに保存されます。 `-DestinationContainerName` パラメーターは、イメージを格納するコンテナーの名前です。 コンテナーが存在しない場合、コンテナーは自動的に作成されます。
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    イメージの URL は、JSON ファイル テンプレートから取得できます。 イメージの完全なパスは、**resources** > **storageProfile** > **osDisk** > **image** > **uri** セクションにあります。 イメージの URL は `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`のようになります。
   
    ポータルで URI を確認することもできます。 イメージは、ストレージ アカウントの **system** という名前のコンテナーにコピーされます。 

## <a name="next-steps"></a>次のステップ
* [イメージから VM を作成](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)します。


