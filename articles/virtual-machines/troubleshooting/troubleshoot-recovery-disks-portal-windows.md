---
title: Azure Portal で Windows VM のトラブルシューティングを行う | Microsoft Docs
description: Azure Portal から OS ディスクを復旧 VM に接続して、Azure の Windows 仮想マシンの問題のトラブルシューティングを行う方法について説明します。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735230"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>Azure Portal から OS ディスクを復旧 VM にアタッチして Windows VM のトラブルシューティングを行う
Azure の Windows 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合は、仮想ハード ディスク (VHD) でトラブルシューティング手順を行う必要がある場合があります。 一般的な例として、VM の正常な起動を妨げる失敗したアプリケーション更新が挙げられます。 この記事では、Azure Portal で仮想ハード ディスクを別の Windows VM に接続してエラーを修正してから、元の VM を再作成する方法について詳しく説明します。 

## <a name="recovery-process-overview"></a>回復プロセスの概要
トラブルシューティングのプロセスは次のとおりです。

1. 影響を受けている VM を停止します。
1. VM の OS ディスクのスナップショットを作成します。
1. スナップショットから仮想ハード ディスクを作成します。
1. トラブルシューティングのために、仮想ハード ディスクを別の Windows VM に接続してマウントします。
1. トラブルシューティング用 VM に接続します。 元の仮想ハード ディスクで、ファイルを編集するか、任意のツールを実行して問題を解決します。
1. 仮想ハード ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。
1. VM の OS ディスクをスワップします。

> [!NOTE]
> この記事は、アンマネージド ディスクを使用する VM には適用されません。

## <a name="take-a-snapshot-of-the-os-disk"></a>OS ディスクのスナップショットを取得する
スナップショットは、仮想ハード ディスクの完全な読み取り専用コピーです。 進行中のプロセスをすべてクリアするために、スナップショットを取得する前に VM をクリーン シャットダウンすることをお勧めします。 OS ディスクのスナップショットを作成するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。 サイドバーから **[仮想マシン]** を選択してから、問題が発生している VM を選択します。
1. 左側のウィンドウで **[ディスク]** を選択してから、OS ディスクの名前を選択します。
    ![ディスク設定時の OS ディスクの名前を示すスクリーンショット。](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. OS ディスクの **[概要]** ページで、 **[スナップショットの作成]** を選択します。
1. OS ディスクと同じ場所にスナップショットを作成します。

## <a name="create-a-disk-from-the-snapshot"></a>スナップショットからディスクを作成する
スナップショットからディスクを作成するには、次の手順を実行します。

1. Azure portal から **Cloud Shell** を選択します。

    ![Azure Cloud Shell を開くためのボタンを示すスクリーンショット。](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 次の PowerShell コマンドを実行して、スナップショットからマネージド ディスクを作成します。 サンプル名を適切な名前に置き換えます。

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. コマンドが正常に実行された場合は、指定したリソース グループ内に新しいディスクが表示されます。

## <a name="attach-the-disk-to-another-vm"></a>ディスクを別の VM に接続する
次のいくつかの手順では、トラブルシューティングのために別の VM を使用します。 ディスクをトラブルシューティング用 VM にアタッチした後は、そのディスクの内容を参照して編集できます。 このプロセスにより、すべての構成エラーを修正したり、追加のアプリケーションまたはシステム ログ ファイルを確認したりできるようになります。 ディスクを別の VM に接続するには、次の手順を実行します。

1. ポータルでリソース グループを選択し、トラブルシューティング用 VM を選択します。 **[ディスク]**  >  **[編集]**  >  **[データ ディスクの追加]** の順に選択します。

    ![ポータルで既存のディスクをアタッチするための選択を示すスクリーンショット。](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. **[データディスク]** の一覧で、特定した VM の OS ディスクを選択します。 OS ディスクが表示されない場合は、トラブルシューティング用の VM と OS ディスクが同じリージョン (場所) にあることを確認します。 
3. **[保存]** をクリックして変更を適用します。

## <a name="mount-the-attached-data-disk-to-the-vm"></a>接続されたデータ ディスクを VM にマウントする

1. トラブルシューティング用 VM へのリモート デスクトップ接続を開きます。 
2. トラブルシューティング用 VM で、 **[サーバー マネージャー]** を開き、 **[ファイル サービスと記憶域サービス]** を選択します。 

    ![[サーバー マネージャー] で [ファイル サービスと記憶域サービス] が選択されているところを示すスクリーンショット。](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. データ ディスクが自動的に検出され、接続されます。 接続されているディスクの一覧を表示するには、 **[ディスク]** を選択します。 データ ディスクを選び、ドライブ文字を含むボリューム情報を表示できます。 次の例は、ドライブ **F** を使用してアタッチされているデータ ディスクを示しています。

    ![[サーバー マネージャー] にアタッチされているディスクとボリュームの情報を示すスクリーンショット。](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>元の仮想ハード ディスクで問題を修正する
既存の仮想ハード ディスクをマウントすることで、必要に応じてメンテナンスやトラブルシューティングの手順を実行できるようになります。 すべてのエラーを解決したら、次の手順に進みます。

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>元の仮想ハード ディスクのマウントを解除して切断する
トラブルシューティング用 VM から既存の仮想ハード ディスクをデタッチします。 仮想ハード ディスクをトラブルシューティング用 VM にアタッチするリースを解放するまで、仮想ハード ディスクを他の VM で使用することはできません。

1. VM へのリモート デスクトップ セッションから、 **[サーバー マネージャー]** を開き、 **[ファイル サービスと記憶域サービス]** を選択します。

    ![[サーバー マネージャー] で [ファイル サービスと記憶域サービス] が選択されているところを示すスクリーンショット。](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. **[ディスク]** を選択し、データ ディスクを右クリックして、 **[オフラインにする]** を選択します。

    ![[サーバー マネージャー] でデータ ディスクがオフラインとして設定されているところを示すスクリーンショット。](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. 仮想ハード ディスクを VM からデタッチします。 Azure Portal で VM を選択し、 **[ディスク]** を選択します。 
4. **[編集]** を選択し、アタッチした OS ディスクを選択して、 **[削除]** を選択します。

    ![既存の仮想ハード ディスクをデタッチするための選択を示すスクリーンショット。](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    続行する前に、VM でデータ ディスクが正常に削除されるまで待ちます。

## <a name="swap-the-os-disk-for-the-vm"></a>VM の OS ディスクをスワップする

Azure portal では、VM の OS ディスクの変更がサポートされるようになりました。 そのためには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。 サイドバーから **[仮想マシン]** を選択してから、問題が発生している VM を選択します。
1. 左側のウィンドウで **[ディスク]** を選択してから、 **[OS ディスクのスワップ]** を選択します。
   
    ![Azure portal で OS ディスクをスワップするためのボタンを示すスクリーンショット。](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 修復した新しいディスクを選択してから、VM の名前を入力して変更を確認します。 そのディスクがリストに表示されない場合は、トラブルシューティング用 VM からディスクをデタッチしてから 10 分から 15 分待ちます。 また、ディスクが VM と同じ場所にあることも確認してください。
1. **[OK]** を選択します。

## <a name="next-steps"></a>次のステップ
VM への接続の問題が発生した場合は、[Azure VM へのリモート デスクトップ接続のトラブルシューティング](troubleshoot-rdp-connection.md)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Windows VM でのアプリケーションの接続の問題のトラブルシューティング](troubleshoot-app-connection.md)に関する記事をご覧ください。

Azure Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/management/overview.md)」をご覧ください。


