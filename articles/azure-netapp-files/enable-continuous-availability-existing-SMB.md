---
title: 既存の Azure NetApp Files の SMB ボリュームで継続的可用性を有効にする | Microsoft Docs
description: 既存の Azure NetApp Files の SMB ボリュームで SMB 継続的可用性を有効にする方法について説明します。
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
ms.date: 08/18/2021
ms.author: b-juche
ms.openlocfilehash: d9af43bad8f6db6b50070368be732f20fb1fbde8
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429832"
---
# <a name="enable-continuous-availability-on-existing-smb-volumes"></a>既存の SMB ボリュームで継続的可用性を有効にする

[新しい SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md#continuous-availability)ときに、SMB 継続的可用性 (CA) 機能を有効にすることができます。 また、既存の SMB ボリュームで SMB CA を有効にすることもできます。この記事では、その方法について説明します。

## <a name="considerations"></a>注意事項

* 現在、[ **[スナップショット パスを非表示にする]** ](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) オプションは、CA が有効な SMB ボリュームには影響しません。  

* `~snapshot` (他の SMB ボリュームでの走査に使用できる) ディレクトリは、CA が有効な SMB ボリュームでは表示されません。 手動で `~snapshot\<snapshotName>` を入力してスナップショットにアクセスすることもできます。

## <a name="steps"></a>手順

1. [SMB 継続的可用性共有が登録されている](https://aka.ms/anfsmbcasharespreviewsignup)ことを確認します。  
2. SMB CA を有効にしたい SMB ボリュームをクリックします。 **[Edit]** をクリックします。  
3. 表示される [編集] ウィンドウで、 **[継続的可用性を有効にする]** チェックボックスをオンにします。   
    ![[継続的可用性を有効にする] オプションを示すスナップショット。](../media/azure-netapp-files/enable-continuous-availability.png)

4. サーバーを再起動します。   

    > [!NOTE]
    > **[継続的可用性を有効にする]** オプションを選択しただけでは、自動的に既存の SMB セッションが継続的に使用できるようになるわけではありません。 このオプションを選択したら、サーバーを再起動して変更を有効にします。  

5. 次のコマンドを使用して、CA が有効になっていて、ボリュームをマウントしているシステムで使用されていることを確認します。

    ```powershell-interactive
    get-smbconnection | select -Property servername,ContinuouslyAvailable
    ```
 
    新しいバージョンの PowerShell のインストールが必要になる場合があります。 

    サーバー名がわかっている場合は、コマンドで `-ServerName` パラメーターを使用できます。 [Get-SmbConnection](/powershell/module/smbshare/get-smbconnection?view=windowsserver2019-ps&preserve-view=true) powershell コマンドの詳細を参照してください。

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md)