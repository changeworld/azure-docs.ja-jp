---
title: Unix のアクセス許可を構成し、Azure NetApp Files NFS およびデュアル プロトコル ボリュームの所有権モードを変更する | Microsoft Docs
description: Unix のアクセス許可の設定方法と、Azure NetApp Files NFS およびデュアル プロトコル ボリュームの所有権モードの変更オプションについて説明します。
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
ms.date: 08/06/2021
ms.author: b-juche
ms.openlocfilehash: bfb794b1344dbc4723ca699f572b487b54d60c77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781318"
---
# <a name="configure-unix-permissions-and-change-ownership-mode-for-nfs-and-dual-protocol-volumes"></a>Unix のアクセス許可を構成し、NFS およびデュアル プロトコル ボリュームの所有権モードを変更する

`Unix` セキュリティ スタイルの Azure NetApp Files NFS ボリュームまたはデュアル プロトコル ボリュームの場合、**Unix のアクセス許可** を設定するオプションと、**所有権モードの変更** ( **`Chown Mode`** ) オプションがあります。 これらの設定は、ボリュームの作成時またはボリュームの作成後に指定できます。 

## <a name="unix-permissions"></a>Unix のアクセス許可   

Azure NetApp Files の **Unix のアクセス許可** 機能を使用すると、マウント パスの変更アクセス許可を指定できます。 この設定は、マウント パスの下のファイルには適用されません。   

Unix のアクセス許可設定は、既定で `0770` に設定されています。 この既定の設定では、所有者とグループに読み取り、書き込み、実行のアクセス許可が付与されますが、他のユーザーにはアクセス許可は付与されません。 

 カスタム Unix アクセス許可の値 (例: `0755`) を指定して、所有者、グループ、または他のユーザーに必要なアクセス許可を付与できます。  

## <a name="change-ownership-mode"></a>所有権モードの変更   

所有権モードの変更 ( **`Chown Mode`** ) 機能を使用すると、ファイルとディレクトリの所有権管理機能を設定できます。  ボリュームのエクスポート ポリシーで設定を指定または変更できます。 **`Chown Mode`** には、次の 2 つのオプションを使用できます。   

* `Restricted` (既定値) - ルート ユーザーのみがファイルとディレクトリの所有権を変更できます。
* `Unrestricted` - ルート以外のユーザーは、自身が所有するファイルとディレクトリの所有権を変更できます。

## <a name="considerations"></a>考慮事項  

* 指定した Unix アクセス許可は、ボリューム マウント ポイント (ルート ディレクトリ) にのみ適用されます。  
* リージョン間レプリケーション構成内のソースまたは宛先のボリュームに対する Unix アクセス許可を変更することはできません。 

## <a name="steps"></a>手順

1. Unix のアクセス許可と所有権モードの変更機能は、現在プレビュー段階です。 これらの機能を初めて使用する場合は、事前にこれらの機能を登録する必要があります。   

    1. **Unix のアクセス許可** 機能を登録します。   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions
        ```

    2.  **所有権モードの変更** 機能を登録します。    

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```

    3. 機能の登録の状態を確認します。    

        > [!NOTE]
        > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が `Registered` になってから続行してください。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions  
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```
        
    また、[Azure CLI のコマンド](/cli/azure/feature) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 

2. [NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)とき、または [デュアル プロトコル ボリュームを作成する](create-volumes-dual-protocol.md)ときに、 **[プロトコル]** タブで **Unix のアクセス許可** を指定し、所有権モード ( **`Chown Mode`** ) の設定を変更できます。 

    次の例は、NFS ボリュームの [ボリュームの作成] 画面を示しています。 

    ![NFS の [ボリュームの作成] 画面を示すスクリーンショット。](../media/azure-netapp-files/unix-permissions-create-nfs-volume.png)

3. 既存の NFS またはデュアル プロトコル ボリュームの場合、**Unix のアクセス許可** を設定または変更し、次のように **所有権モードを変更** できます。  

    1. Unix のアクセス許可を変更するには、その **ボリューム** を右クリックし、 **[編集]** を選択します。 表示される [編集] ウィンドウで、 **[Unix のアクセス許可]** の値を指定します。  
        ![[Unix のアクセス許可] の [編集] 画面を示すスクリーンショット。](../media/azure-netapp-files/unix-permissions-edit.png)

    2. 所有権モードの変更を修正するには、その **ボリューム** をクリックし、 **[ポリシーのエクスポート]** をクリックして **`Chown Mode`** 設定を変更します。  
        ![[ポリシーのエクスポート] 画面を示すスクリーンショット。](../media/azure-netapp-files/chown-mode-edit.png)

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files のデュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md) 
* [エクスポート ポリシーを構成する](azure-netapp-files-configure-export-policy.md)
