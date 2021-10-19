---
title: Azure NetApp Files NFS またはデュアル プロトコル ボリュームのエクスポート ポリシーを構成する - Azure NetApp Files
description: Azure NetApp Files を使用して NFS ボリュームへのアクセスを制御するエクスポート ポリシーの構成方法について説明します
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.openlocfilehash: 2412673dd71c6fbe6a1f070451db24d748694a8d
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129811124"
---
# <a name="configure-export-policy-for-nfs-or-dual-protocol-volumes"></a>NFS またはデュアル プロトコル ボリュームのエクスポート ポリシーを構成する

NFS プロトコル (NFSv3 および NFSv4.1) またはデュアル プロトコル (NFSv3 と SMB、または NFSv4.1 と SMB) を使用する Azure NetApp Files ボリュームへのアクセスを制御するために、エクスポート ポリシーを構成できます。 

最大 5 つのエクスポートポリシー ルールを作成できます。

## <a name="configure-the-policy"></a>ポリシーを構成する 

1.  **[ボリューム]** ページで、エクスポート ポリシーを構成するボリュームを選択したら、 **[エクスポート ポリシー]** を選択します。 また、ボリュームの作成時にエクスポート ポリシーを構成することもできます。

2.  エクスポート ポリシー ルールを作成するには、次の情報を指定します。   
    * **インデックス**: ルールのインデックス番号を指定します。  
      
      エクスポート ポリシーは、最大 5 つのルールで構成できます。 ルールは、リスト内の対応するインデックス番号の順に評価されます インデックス番号が小さいルールが先に評価されます。 たとえば、インデックス番号が 1 のルールは、インデックス番号が 2 のルールよりも前に評価されます。 

    * **許可されたクライアント**: 次のいずれかの形式で値を指定します。  
      * IPv4 アドレス。 例: `10.1.12.24`
      * ビット数として表されたサブネット マスクを指定した IPv4 アドレス。 例: `10.1.12.10/4`
      * コンマ区切りの IP アドレス。 コンマで区切ることによって、1 つの規則に複数のホスト IP を入力できます。 長さの制限は 4,096 文字です。 例: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **アクセス**:次のアクセス タイプのいずれかを選択します。  
      * アクセス権なし 
      * 読み取りと書き込み
      * [読み取り専用]

    * **[読み取り専用]** と **[読み取り/書き込み]** : NFSv4.1 で Kerberos 暗号化を使用する場合は、「[NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)」の手順に従ってください。  Kerberos のパフォーマンスへの影響については、[NFSv4.1 ボリュームでの Kerberos のパフォーマンスに対する影響](performance-impact-kerberos.md)に関する記事を参照してください。 

      ![Kerberos のセキュリティ オプション](../media/azure-netapp-files/kerberos-security-options.png) 

    * **ルート アクセス**: `root` アカウントがボリュームにアクセスできるかどうかを指定します。  既定では、ルート アクセスは **[On]\(オン\)** に設定され、`root` アカウントはボリュームにアクセスできます。  このオプションは、NFSv4.1 Kerberos ボリュームでは使用できません。

      ![エクスポート ポリシー](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

    * **chown モード**: ファイルとディレクトリの所有権管理機能の設定に応じて、所有権変更モードを変更できます。  次の 2 つのオプションを使用できます。   

      * `Restricted` (既定値) - ルート ユーザーのみがファイルとディレクトリの所有権を変更できます。
      * `Unrestricted` - ルート以外のユーザーは、自身が所有するファイルとディレクトリの所有権を変更できます。  

        **`Chown Mode`** の設定には、登録の要件と考慮事項が適用されます。 [Unix のアクセス許可の構成と所有権モードの変更](configure-unix-permissions-change-ownership-mode.md)に関する記事の手順に従ってください。  

      ![所有権変更モード オプションを示すスクリーンショット。](../media/azure-netapp-files/chown-mode-export-policy.png) 

## <a name="next-steps"></a>次のステップ 
* [ボリュームをマウントまたはマウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Unix のアクセス許可を構成して所有権モードを変更する](configure-unix-permissions-change-ownership-mode.md) 
* [スナップショットを管理する](azure-netapp-files-manage-snapshots.md)
