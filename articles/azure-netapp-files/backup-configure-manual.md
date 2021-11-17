---
title: Azure NetApp Files 用に手動バックアップを構成する | Microsoft Docs
description: Azure NetApp Files ボリューム用に手動バックアップを構成する方法について説明します。
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
ms.date: 11/10/2021
ms.author: b-juche
ms.openlocfilehash: 5b5d2cafbbd70e63e2b3a039e94f1fc66106203c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319599"
---
# <a name="configure-manual-backups-for-azure-netapp-files"></a>Azure NetApp Files 用に手動バックアップを構成する 

Azure NetApp Files バックアップは、ボリューム レベルで "*ポリシーベースの*" (スケジュールされた) バックアップと "*手動*" (オンデマンド) バックアップをサポートしています。 同じボリューム内で両方の種類のバックアップを使用できます。 構成プロセス中、ポリシーベースのバックアップまたは手動バックアップを行う前に、Azure NetApp Files ボリュームのバックアップ機能を有効にします。 

この記事では、手動バックアップの構成方法について説明します。 ポリシーベースのバックアップ構成については、[ポリシーベースのバックアップ構成](backup-configure-policy-based.md)に関するページをご覧ください。  

> [!IMPORTANT]
> Azure NetApp Files バックアップ機能は現在プレビューの段階です。 機能にアクセスするための順番待ちリクエストを、「 **[Azure NetApp Files バックアップ パブリック プレビュー](https://aka.ms/anfbackuppreviewsignup)** 」ページから送信する必要があります。 Azure NetApp Files バックアップ機能は、Azure NetApp Files チームから正式な確認メールが届いてからご利用ください。

## <a name="about-manual-backups"></a>手動バックアップについて  

バックアップ (ポリシーベースまたは手動) を行うには、すべての Azure NetApp Files ボリュームでバックアップ機能を有効にする必要があります。   

バックアップ機能を有効にしたら、手動でボリュームをバックアップするオプションを利用できます。 手動バックアップでは、アクティブなファイル システムのポイントインタイム スナップショットが作成され、そのスナップショットが Azure ストレージ アカウントにバックアップされます。

手動バックアップの動作を次に示します。  

* ボリュームでバックアップが既に有効で、バックアップ ポリシーで構成されていても、そのボリューム上で手動バックアップを作成できます。  ただし、ボリュームに対する未処理の手動バックアップ要求は 1 つしか存在できません。 バックアップ ポリシーを割り当てたときに、ベースライン転送がまだ進行中だと、そのベースライン転送が完了するまで、手動バックアップの作成はブロックされます。

* バックアップに使用する目的で既存のスナップショットを指定しない限り、手動のバックアップを作成すると、ボリュームでスナップショットが自動生成されます。 その後、そのスナップショットは Azure ストレージに転送されます。 ボリュームで作成されたスナップショットは、次の手動バックアップが作成されるまで保持されます。 後続の手動バックアップの間、以前のスナップショットが削除されます。 最後の手動バックアップで生成されたスナップショットは削除できません。 

## <a name="enable-backup-functionality"></a>バックアップ機能を有効にする

手動バックアップを作成する前に、対象となるボリュームのバックアップ機能を有効にします (まだ有効にしていない場合)。 

1. **[ボリューム ]** に移動し、バックアップを有効にする特定のボリュームを選択します。
2. **[構成]** をクリックします。
3. [バックアップの構成] ページで、 **[有効]** の設定を **[オン]** に切り替えます。   
    コンテナー フィールドは事前に入力されています。 
4. **[OK]** をクリックします。   

![[バックアップの構成] ウィンドウの有効設定を示すスクリーンショット。](../media/azure-netapp-files/backup-configure-enabled.png)

## <a name="create-a-manual-backup-for-a-volume"></a>ボリュームの手動バックアップを作成する

1. **[ボリューム ]** に移動し、手動バックアップを作成するボリュームを選択します。
2. **[Add Backup]\(バックアップを追加\)** を選択します。
3. [新しいバックアップ] ウィンドウが表示されたら、次のように操作します。   

    1. **[Name]** フィールドにバックアップの名前を指定します。   
    
        * 手動バックアップ名は、3 から 256 文字で指定する必要があります。   
        * ベスト プラクティスとして、実際のバックアップ名の前に次の形式でプレフィックスを追加します。 これにより、(バックアップが保持されたまま) ボリュームが削除された場合に、手動バックアップを特定しやすくなります。   

            `NetAppAccountName-CapacityPoolName-VolumeName`   

            たとえば、NetApp アカウントが `account1`、容量プールが `pool1`、ボリューム名が `vol1` の場合、 手動バックアップの名前は次のようになります。    

            `account1-pool1-vol1-backup1`   

            短い形式のバックアップ名を使用する場合も、バックアップ リストに表示されるように、NetApp アカウント、容量プール、ボリューム名を特定できる情報が名前に含まれていることを確認してください。
            
    2. バックアップに既存のスナップショットを使用する場合、 **[既存のスナップショットを使用する]** オプションを選択します。  このオプションを使用するとき、バックアップに使用されている既存のスナップショット名と [名前] フィールドが一致していることを確認します。 

4. **Create** をクリックしてください。 

    手動バックアップを作成すると、バックアップに指定した名前と同じ名前を使って、ボリューム上にもスナップショットが作成されます。 このスナップショットは、アクティブなファイル システムの現在の状態を表しています。 これは、Azure ストレージに転送されます。 バックアップが完了すると、ボリュームのバックアップ リスト内に手動バックアップ エントリが表示されます。

![新しいバックアップ ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/backup-new.png)


## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files バックアップについて](backup-introduction.md)
* [Azure NetApp Files バックアップの要件と考慮事項](backup-requirements-considerations.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [ポリシー ベースのバックアップの構成](backup-configure-policy-based.md)
* [バックアップ ポリシーを管理する](backup-manage-policies.md)
* [バックアップを検索する](backup-search.md)
* [バックアップを新しいボリュームに復元する](backup-restore-new-volume.md)
* [ボリュームのバックアップ機能を無効にする](backup-disable.md)
* [ボリュームのバックアップを削除する](backup-delete.md)
* [ボリュームのバックアップ メトリック](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)


