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
ms.date: 10/15/2021
ms.author: b-juche
ms.openlocfilehash: 8dace313ed9296dd514fb059e8ed681a6276af0f
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074534"
---
# <a name="enable-continuous-availability-on-existing-smb-volumes"></a>既存の SMB ボリュームで継続的可用性を有効にする

[新しい SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md#continuous-availability)ときに、SMB 継続的可用性 (CA) 機能を有効にすることができます。 また、既存の SMB ボリュームで SMB CA を有効にすることもできます。この記事では、その方法について説明します。

## <a name="considerations"></a>注意事項

* 現在、[ **[スナップショット パスを非表示にする]**](snapshots-edit-hide-path.md) オプションは、CA が有効な SMB ボリュームには影響しません。  

* `~snapshot` (他の SMB ボリュームでの走査に使用できる) ディレクトリは、CA が有効な SMB ボリュームでは表示されません。 手動で `~snapshot\<snapshotName>` を入力してスナップショットにアクセスすることもできます。

## <a name="steps"></a>手順

1. [SMB 継続的可用性共有が登録されている](https://aka.ms/anfsmbcasharespreviewsignup)ことを確認します。  

    SQL Server と [FSLogix のユーザー プロファイル コンテナー](../virtual-desktop/create-fslogix-profile-container.md)でのみ継続的可用性を有効にする必要があります。 SMB 継続的可用性共有を SQL Server と FSLogix のユーザー プロファイル コンテナー以外のワークロードに使用することはサポートされて *いません*。 この機能は現在、Windows SQL Server でサポートされています。 Linux SQL Server では現在サポートされていません。 SQL Server のインストールに管理者以外のアカウント (ドメイン) を使用している場合は、必要なセキュリティ特権がアカウントに割り当てられていることを確認してください。 必要なセキュリティ特権 (`SeSecurityPrivilege`) がドメイン アカウントになく、ドメイン レベルで特権を設定できない場合は、Active Directory 接続の **"セキュリティ特権ユーザー"** フィールドを使用して、そのアカウントに特権を付与できます。 「[Active Directory 接続を作成する](create-active-directory-connections.md#create-an-active-directory-connection)」を参照してください。
            
3. SMB CA を有効にしたい SMB ボリュームをクリックします。 **[Edit]** をクリックします。  
4. 表示される [編集] ウィンドウで、 **[継続的可用性を有効にする]** チェックボックスをオンにします。   
    ![[継続的可用性を有効にする] オプションを示すスナップショット。](../media/azure-netapp-files/enable-continuous-availability.png)

4. 既存の SMB 共有に接続している Windows システムを再起動します。   

    > [!NOTE]
    > **[継続的可用性を有効にする]** オプションを選択しただけでは、自動的に既存の SMB セッションが継続的に使用できるようになるわけではありません。 このオプションを選択したら、サーバーを再起動して変更を有効にします。  

5. 次のコマンドを使用して、CA が有効になっていて、ボリュームをマウントしているシステムで使用されていることを確認します。

    ```powershell-interactive
    get-smbconnection | select -Property servername,ContinuouslyAvailable
    ```
 
    新しいバージョンの PowerShell のインストールが必要になる場合があります。 

    サーバー名がわかっている場合は、コマンドで `-ServerName` パラメーターを使用できます。 [Get-SmbConnection](/powershell/module/smbshare/get-smbconnection?view=windowsserver2019-ps&preserve-view=true) PowerShell コマンドの詳細を参照してください。

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md)
