---
title: 既存の Azure NetApp Files の SMB ボリュームを変換して SMB 継続的可用性を使用する | Microsoft Docs
description: 既存の Azure NetApp Files の SMB ボリュームを変換することによる SMB CA の有効化について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/10/2021
ms.author: b-juche
ms.openlocfilehash: b55b7e5632ad852b199121179772fa078129cdb5
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109755103"
---
# <a name="convert-existing-smb-volumes-to-use-continuous-availability"></a>既存の SMB ボリュームを変換して継続的可用性を使用する 

[新しい SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)ときに、SMB 継続的可用性 (CA) 機能を有効にすることができます。 また、既存の SMB ボリュームを変換して、SMB CA 機能を有効にすることもできます。  この記事では、既存のボリュームを変換することによって SMB CA を有効にする方法について説明します。  

> [!IMPORTANT]
> この手順には、元のボリュームからの、CA 共有に対して有効になっている新しいボリュームへのカットオーバーが含まれます。 そのため、このプロセスのメンテナンス期間を計画する必要があります。 

## <a name="steps"></a>手順

1. [SMB 継続的可用性共有が登録されている](https://aka.ms/anfsmbcasharespreviewsignup)ことを確認します。  
2. SMB ボリュームを使用しているアプリケーションを停止します。 
3. 既存のボリュームの[オンデマンド スナップショットを作成](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)します。 
4. 既存のボリュームから **[スナップショット]** を選択して、スナップショットの一覧を表示します。
5. 復元するスナップショットを右クリックし、メニュー オプションから **[新しいボリュームに復元]** を選択します。
    
    ![[新しいボリュームに復元] オプションを示すスナップショット。](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

6. 表示される [ボリュームの作成] ウィンドウで、新しいボリュームの情報を指定します。  

    * **ボリューム名**    
    作成するボリュームの名前を指定します。   
    名前はリソース グループ内で一意である必要があります。 3 文字以上になるようにしてください。 任意の英数字を使用できます。

    * **クォータ**   
    ボリュームに割り当てる論理ストレージの量を指定します。

    ![[ボリュームの作成] ウィンドウを示すスナップショット。](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

7. [ボリュームの作成] ウィンドウの [プロトコル] セクションで、 **[継続的可用性を有効にする]** オプションがオンになっていることを確認します。

    ![[継続的可用性を有効にする] オプションを示すスナップショット。](../media/azure-netapp-files/enable-continuous-availability-option.png) 

8. **[Review + create]\(レビュー + 作成\)** をクリックします。 **Create** をクリックしてください。   
    新しいボリュームでは、スナップショットで使用されていたものと同じプロトコルが使用されます。
   スナップショットから復元された新しいボリュームが [ボリューム] ビューに表示されます。

9. 新しいボリュームが作成されたら、選択したボリュームのブレードから **[Mount instructions]\(マウント手順\)** をクリックします。 次に、指示に従って新しいボリュームをマウントします。    

10. 新しいボリューム マウント ポイントを使用するようにアプリケーションを再構成します。   

11. 手順 2 で停止したアプリケーションを再起動します。 

12. アプリケーションで新しいボリュームの利用が開始され、新しいボリュームへの復元プロセスが完了したら、必要に応じて元のボリュームを削除できます。  

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md)
* [Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)