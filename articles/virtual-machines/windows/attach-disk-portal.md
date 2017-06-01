---
title: "管理されていないデータ ディスクを Windows VM に接続する - Azure | Microsoft Docs"
description: "Azure Portal で Resource Manager デプロイ モデルを使用して、管理されていない新規または既存のデータ ディスクを Windows VM に接続する方法。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d02f92a8809efd6f58312af8cb40739299ea28f6
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-attach-an-unmanaged-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Azure Portal で Windows VM に管理されていないデータ ディスクを接続する方法

この記事では、管理されていない新規と既存の両方のディスクを Azure Portal で Windows 仮想マシンに接続する方法について説明します。 [PowerShell を使用してデータ ディスクを接続する](./attach-disk-ps.md)こともできます。 接続する前に、次のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](sizes.md)」を参照してください。
* Premium Storage を使用するには、DS シリーズまたは GS シリーズの仮想マシンが必要です。 これらの仮想マシンでは、Premium および Standard のストレージ アカウントのディスクを使用できます。 Premium Storage は特定のリージョンで使用できます。 詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
* 新しいディスクの場合、接続時に Azure で自動的に作成されるので、最初に作成する必要はありません。


[Powershell を使用してデータ ディスクを接続する](attach-disk-ps.md)こともできます。


## <a name="find-the-virtual-machine"></a>仮想マシンの検索
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 左側のメニューの **[仮想マシン]** をクリックします。
3. 一覧から仮想マシンを選択します。
4. [仮想マシン] ブレードで、**[ディスク]** をクリックします。
   
次の手順に従って、[新しいディスク](#option-1-attach-a-new-disk)または[既存のディスク](#option-2-attach-an-existing-disk)を接続します。

## <a name="option-1-attach-and-initialize-a-new-disk"></a>オプション 1: 新しいディスクを接続し、初期化する
1. **[ディスク]** ブレードで、**[+ Add data disk]** (データ ディスクの追加) をクリックします。
2. **[Attach managed disk] (管理ディスクの接続)** ブレードで、**[名前]** にディスクの名前を入力し、**[ソースの種類]** で **[新規 (空のディスク)]** を選択します。
3. **ストレージ コンテナー** の下にある **[参照]** ボタンをクリックし、新しい VHD を格納するストレージ アカウントとコンテナーまで移動して、**[選択]** をクリックします。 
  
   ![ディスク設定を確認する](./media/attach-disk-portal/attach-empty-unmanaged.png)
   
3. データ ディスクの設定が完了したら、**[OK]** をクリックします。
4. **[ディスク]** ブレードに戻り、**[保存]** をクリックしてディスクを VM の構成に追加します。


### <a name="initialize-a-new-data-disk"></a>新しいデータ ディスクの初期化

1. 仮想マシンへの接続 手順については、「 [Windows が実行されている Azure 仮想マシンに接続してログオンする方法](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
1. VM で **[スタート]** メニューをクリックし、「**diskmgmt.msc**」と入力して **Enter** キーを押します。 ディスクの管理スナップインが開始されます。
2. 新しい未初期化のディスクがディスクの管理によって認識され、[ディスクの初期化] ウィンドウが表示されます。
3. 新しいディスクが選択されていることを確認し、**[OK]** をクリックしてディスクを初期化します。
4. 新しいディスクが、**未割り当て**として表示されます。 ディスクの任意の場所を右クリックし、**[新しいシンプル ボリューム]** を選択します。 **[新しいシンプル ボリューム ウィザード]** が起動します。
5. すべての設定を既定値のままウィザードを進め、完了したら **[完了]** を選択します。
6. ディスクの管理を閉じます。
7. 新しいディスクは使用する前にフォーマットする必要がある、というポップアップが表示されます。 **[ディスクのフォーマット]** をクリックします。
8. **[新しいディスクのフォーマット]** ダイアログで設定を確認し、**[開始]** をクリックします。
9. ディスクをフォーマットするとデータがすべて削除されることを警告するメッセージが表示されます。**[OK]** をクリックします。
10. フォーマットが完了したら、**[OK]** をクリックします。


## <a name="option-2-attach-an-existing-disk"></a>オプション 2: 既存のディスクを接続する
1. **[ディスク]** ブレードで、**[+ Add data disk]** (データ ディスクの追加) をクリックします。
2. **[管理されていないディスクの接続]** ブレードで、**[ソースの種類]** として **[既存の BLOB]** を選択します。

    ![ディスク設定を確認する](./media/attach-disk-portal/attach-existing-unmanaged.png)

    3. **[参照]** をクリックして、既存の VHD が配置されているストレージ アカウントとコンテナーに移動します。 VHD をクリックし、**[選択]** をクリックします。
4. **[管理されていないディスクの接続]** ブレードで **[OK]** をクリックします。
5. **[ディスク]** ブレードで、**[保存]** をクリックしてディスクを VM の構成に追加します。
   


## <a name="use-trim-with-standard-storage"></a>Standard Storage での TRIM の使用

Standard Storage (HDD) を使用する場合は、TRIM を有効にする必要があります。 TRIM はディスク上の未使用のブロックを破棄するため、実際に使用しているストレージにのみ課金されます。 これにより、サイズの大きいファイルを作成した後に削除した場合、コストを節約できます。 

次のコマンドを実行すると、TRIM の設定を確認できます。 Windows VM 上でコマンド プロンプトを開いて、次のように入力します。

```
fsutil behavior query DisableDeleteNotify
```

このコマンドが 0 を返す場合、TRIM は適切に有効化されています。 1 が返される場合は、次のコマンドを実行して TRIM を有効にします。
```
fsutil behavior set DisableDeleteNotify 0
```
                
ディスクからデータを削除した後は、TRIM でデフラグを実行して、TRIM 操作が正しくフラッシュすることを確認できます。

```
defrag.exe <volume:> -l
```

## <a name="next-steps"></a>次のステップ
アプリケーションで D: ドライブを使用してデータを保存する必要がある場合は、 [Windows 一時ディスクのドライブ文字を変更](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)できます。


